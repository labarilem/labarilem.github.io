---
title: "Automating Callbacks to Async/Await Migrations for Mocha Tests: Part 2"
excerpt: >
  In this second part we will go deeper into AST editing techniques and show how to automatically migrate tests that use the supertest NPM library.
date: 2023-10-19
last_modified_at: 2023-10-20
categories:
  - My Projects
tags:
  - Programming

share: false
---

In the [previous article](https://marcolabarile.me/my%20projects/2023/10/13/automating-callbacks-to-async-migration-in-mocha-tests-1/) we showed how to automate the migration from callbacks to async/await for Mocha tests using [jscodeshift](https://github.com/facebook/jscodeshift). We started from the [jscodeshift-ts-template](https://github.com/labarilem/jscodeshift-ts-template) repo and added the final implementation to the [ts-codemods](https://github.com/labarilem/ts-codemods) repo.

We know by now that not all code can be automatically migrated. So is there anything else we can do? If you happen to use some known test libraries or frameworks that come with their code structure, you can migrate them too with jscodeshift in most cases. In this article, we'll have a look at [supertest](https://github.com/ladjs/supertest) and show how to create a codemod, built on top of the previous one, that migrates supertest code too.

## The problem

We want to convert supertest calls inside callback-based Mocha tests to async/await calls. We already built the [async-it](https://github.com/labarilem/ts-codemods#async-it) codemod to deal with Mocha tests, so we can reuse it in the new transformer.

Our files now look like this:

```typescript
import { describe } from 'mocha';
import { expect } from 'chai';
import { agent } from 'supertest';

describe('my api resource', function () {
  it('replies with code 200', function (done) {
    agent()
      .get('https://myapi.com/resource')
      .end(function (err, res) {
        if (err) return done(err);
        expect(res.statusCode).to.equal(200);
        expect(res.body.email).to.equal('hello@myapi.com');
        done();
      });
  });
});
```

a good conversion of this test file could look like:

```typescript
import { describe } from 'mocha';
import { expect } from 'chai';
import { agent } from 'supertest';

describe('my api resource', function () {
  it('replies with code 200', async function() {
    const res = await agent()
      .get('https://myapi.com/resource');;
    expect(res.statusCode).to.equal(200);
    expect(res.body.email).to.equal('hello@myapi.com');
  });
});
```

To perform this migration, we need to:
1. Locate all supertest `end` calls inside Mocha `it` functions and make them async
2. Remove the typical `if (err) return done(err);` statement found inside `end` calls
3. Extract the `end` call's callback body to the outer function's body
4. Make the `end` call async and assign its result to a variable
5. Remove the `end` call, while keeping the other parts of the fluent call chain

## Building the transformer

Let's go over the [codemod development workflow](https://marcolabarile.me/my%20projects/2023/10/13/automating-callbacks-to-async-migration-in-mocha-tests-1/#:~:text=a%20look%20at-,the%20ideal%20workflow,-for%20building%20transformers) steps together.

### 1. Creating a test case

Let's say this is our input code:

```typescript
import { describe } from 'mocha';
import { expect } from 'chai';
import { agent } from 'supertest';

describe('my api resource', function () {
  it('replies with code 200', function (done) {
    agent()
      .get('https://myapi.com/resource')
      .end(function (err, res) {
        if (err) return done(err);
        expect(res.statusCode).to.equal(200);
        expect(res.body.email).to.equal('hello@myapi.com');
        done();
      });
  });
});
```

this is what we'd expect as output:

```typescript
import { describe } from 'mocha';
import { expect } from 'chai';
import { agent } from 'supertest';

describe('my api resource', function () {
  it('replies with code 200', async function() {
    const res = await agent()
      .get('https://myapi.com/resource');;
    expect(res.statusCode).to.equal(200);
    expect(res.body.email).to.equal('hello@myapi.com');
  });
});
```

In real-life scenarios we might also want to test whether the codemod produces code with conflicting variables declarations. It can happen when having multiple supertest calls inside the same `it` call.

Be careful when using codemods, and always check the transformed code.
{: .notice--warning}

## 2. Exploring the AST

Let's go to [AST explorer](https://astexplorer.net/) and have a look at the AST of our test case's input code.

![AST explorer screenshot](/assets/images/2023-10-14-automating-callbacks-to-async-migration-in-mocha-tests-2/ast-explorer.jpg){: class="center"}

These are the nodes we're looking for (assuming we have already identified `it` functions):
1. All *CallExpression*s whose callee is an *MemberExpression* and its property name is `end`. We might also check that the object of the expression is a *CallExpression* to be a little more confident we are in a fluent call chain.
2. From here it's trivial to get the argument, a *FunctionExpression*, and its respective parameters.
3. In the body of this function, we look for *IfStatement*s whose condition is an *Identifier* having the same name as the `err` parameter. We do this so the codemod works even when that argument is not called `end`.

## 3. Implementing the codemod

We'll add the new transformer in the [ts-codemods](https://github.com/labarilem/ts-codemods) repository, so we can import the transformer we built in the previous article.

Let's go over it step by step:

1. Calling the `async-it` transformer to migrate Mocha tests code to async/await:

    ```typescript
    export default function asyncSupertestTransformer(file: FileInfo, api: API) {
      const j = api.jscodeshift;
      const root = j(file.source);

      const options: AsyncItTransformerOptions = {
        describeFuncName: 'describe',
        itFuncName: 'it',
        skipFuncName: 'skip',
        doneFuncName: 'done',
        rmDoneFunc: true,
        rmDoneFuncMode: 'rm'
      };

      const endFuncName = 'end';

      // make 'it' func args async and remove all 'done' func calls
      const itFuncCalls = asyncItAstTransformer(j, root, options);
    }
    ```

    the transformer returns the collection of `it` function calls nodes in the current file. We'll start looking for our nodes inside the children of that collection.

2. Finding all supertest `end` calls:

    ```typescript
    itFuncCalls
      .find(j.CallExpression, {
        callee: {
          type: 'MemberExpression',
          property: {
            name: endFuncName
          },
          object: {
            type: 'CallExpression'
          }
        },
      })
    ```

    we also want to check the arguments of these calls, but not with the jscodeshift `find` method. We'll do it in the `forEach` that processes our nodes because when replacing AST nodes inside a loop, sometimes a new loop iteration might be triggered with elements that need to be filtered out.
    
    Let's add our loop with this type guard to prevent the issue:

    ```typescript
    // ...
    .forEach(call => {
      // this is the type guard
      if (call?.node?.arguments?.length !== 1 ||
        (call.node.arguments[0]?.type !== 'FunctionExpression' &&
          call.node.arguments[0].type !== 'ArrowFunctionExpression') ||
        call.node.arguments[0].async ||
        call.node.arguments[0].params?.length !== 2 ||
        (call.node.arguments[0] as FunctionExpression | ArrowFunctionExpression)
          .params.some(x => x?.type !== 'Identifier'))
        return;
    }
    ```

3. Removing the return statement in the `end` callback:

    ```typescript
    // ...
    const errArgName = (endFuncArg.params[0] as Identifier).name;
    const resArgName = (endFuncArg.params[1] as Identifier).name;

    j(endFuncArg.body)
      .find(j.IfStatement, {
        test: {
          type: 'Identifier',
          name: errArgName
        }
      })
      .remove();
    ```

    note that we also parse the callback parameters' names here to make sure we remove relevant code.

3. Extracting the body of the `end` callback into the body of the outer function (supposedly the `it` function's callback):

    ```typescript
    // ...
    for (const statement of endFuncArg.body.body) {
      itFuncArgBody.push(statement);
    }
    ```

4. Replacing the old `end` call with the new async version:

    ```typescript
    // ...
    const baseCall = (call.node.callee as MemberExpression).object as CallExpression;
    const assignAsyncResult = j.variableDeclaration('const', [
      j.variableDeclarator(
        j.identifier(resArgName),
        j.awaitExpression(baseCall)
      )
    ]);
    j(call).replaceWith(assignAsyncResult);
    ```

    to preserve the call chain, we replace the `end` call with a subset of the whole call chain.
  
5. Writing the output code, with a final check to prevent writing when there are no changes to the input AST.

    ```typescript
    return itFuncCalls.size() > 0 ? root.toSource({ lineTerminator: '\n', quote: 'single' }) : null;
    ```
    
    since we're calling the `async-it` transformer, we need to check if it detected any callback-based `it` calls.

### 4. Running tests

Now we're ready to run our tests with Jest:

```sh
$ npm test
```

```sh
> ts-codemods@1.0.0 test
> jest

 PASS  __tests__/async-supertest.test.ts
  async-supertest transformer
    ./src/async-supertest
      √ transforms correctly using "async-supertest/function" data (138 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.225 s, estimated 9 s
Ran all test suites.
```

Everything looks good, so our workflow ends here. Of course, in a real-world scenario, you might want to add more than one test to be sure your codemod does its job without breaking the source code.

## Conclusion

We showed how to implement our transformer with jscodeshift. It can help automate more code when migrating away from callback-based Mocha tests.

You can find the full implementation and a ready-to-use script in the [ts-codemods](https://github.com/labarilem/ts-codemods) repository. If you have any questions/suggestions/ideas, please feel free to open an issue there!
