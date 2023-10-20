---
title: "Automating Callbacks to Async/Await Migrations for Mocha Tests: Part 1"
excerpt: >
  Doing this migration in a large codebase can be tedious but necessary. The article will show you how to automate this task using JS code transformation tools.
date: 2023-10-13
last_modified_at: 2023-10-14
categories:
  - My Projects
tags:
  - Programming

share: false
---

In the ever-evolving landscape of Javascript software development, the shift towards modern features like async/await has become a crucial aspect of increasing code maintainability. However, for those with large codebases reliant on Mocha tests, this transition can be a tedious, time-demanding task.

Let's explore how automatic code transformation tools can significantly ease this migration, allowing developers to focus on reviewing the changes instead of manually editing hundreds of files.

## An intro to jscodeshift

In 2015 Facebook published [jscodeshift](https://github.com/facebook/jscodeshift), an open-source AST transformation tool for Javascript/Typescript codebases. It is built on top of [recast](https://github.com/benjamn/recast) and provides a fluent and functional API that makes it easy to manipulate ASTs.

The tool was designed to simplify the process of automating large-scale codebase updates, making it easier for developers to transition their code in response to evolving standards and best practices.

Initially, jscodeshift gained traction within the Facebook engineering community, proving invaluable for transforming their massive codebase in a safe, consistent, and efficient manner. Then, the OSS community around jscodeshift started contributing to its development.

If you want to learn more about the tool, you can check [Awesome jscodeshift](https://github.com/sejoker/awesome-jscodeshift) and [Awesome Codemods](https://github.com/rajasegar/awesome-codemods).

## The problem

We want to convert callback-based Mocha tests into async/await-based tests.

Our files now look like this:

```typescript
import { describe } from "mocha";
import { expect } from "chai";

describe("a test suite", function () {
  it("a test", function (done) {
    setTimeout(() => {
      expect(true).to.be.true;
      done();
    }, 1);
  });
});
```

a good conversion of this test file could look like:

```typescript
import { describe } from "mocha";
import { expect } from "chai";
import { promisify } from "util";

const sleep = promisify(setTimeout);

describe("a test suite", function () {
  it("a test", async function () {
    await sleep(1);
    expect(true).to.be.true;
  });
});
```

To perform this migration, a SW engineer would typically:
1. Locate all the callback-based tests
2. Convert the 2nd argument passed to the Mocha `it` function into an async function
3. Remove any remaining `done` calls in the function's body
4. Adapt the other callback-based async calls in the function's body to the new changes

We can confidently automate the steps from 1 to 3, but let's examine the last one.
In this example, an AST parser cannot know whether `setTimeout` has an async signature or needs to be promisified. Since performing automatic changes on those functions could introduce sneaky bugs, be unreliable, and require a review from the SW engineer anyway, it's not worth it to automate at 100%.

Our goal then is to automate steps 1-3 with jscodeshit, build a reusable script, and use it to migrate each file.

## Building the transformer

Before jumping to the code, let's have a look at the ideal workflow for building transformers with jscodeshift:
1. Create a test case consisting of an input code and the expected output code.
2. Examine the AST of your input code with [AST explorer](https://astexplorer.net/). Select `recast` as the parser and eventually select `typescript` as the parser in recast's settings. Then paste your code and you will be able to explore the corresponding AST. This makes it easy to find the nodes you want to edit and what their path is in the AST.
3. Use the jscodeshift API to implement the changes.
4. Run the test and make sure it passes. If it doesn't, go to step 2.

For every new feature you want to add to your transformer, just start a new workflow.

Let's go over these steps together to build our codemod.

### 1. Creating a test case

Let's say this is our input code:

```typescript
import { describe } from 'mocha';
import { expect } from 'chai';

describe('a test suite', function () {
  it('a test', function (done) {
    setTimeout(() => {
      expect(true).to.be.true;
      done();
    }, 1);
  });
});
```

this is what we'd expect as output:

```typescript
import { describe } from 'mocha';
import { expect } from 'chai';

describe('a test suite', function () {
  it('a test', async function() {
    setTimeout(() => {
      expect(true).to.be.true;
    }, 1);
  });
});
```

we have changed the `it` callback arg signature to an async parameterless function and removed every call to the `done` function in the body. Then a manual review would be required to find the best way to convert each call in the test to an awaitable call.

Be careful when using codemods, and always check the transformed code.
{: .notice--warning}

But there are other ways to do the migration. Some good candidates:
- Do not remove calls to `done` so after the codemod we get transpilation errors (with Typescript) or runtime errors (with Javascript). This way we can be sure unawaited async calls won't slip through your review.
- Instead of removing all calls to `done`, replace them with a `throw` statement. This has similar benefits and will make your code compile too when using Typescript.

I have implemented these options in the codemod I built, but we won't go over them in this article to keep it short.
{: .notice--info}

## 2. Exploring the AST

In [AST explorer](https://astexplorer.net/) you can mouse over code paths and see the corresponding AST node. This shows us the paths of the nodes that we need to find and edit.

![AST explorer screenshot](/assets/images/2023-10-14-automating-callbacks-to-async-migration-in-mocha-tests-1/ast-explorer.jpg){: class="center"}

These are the nodes we're looking for:
1. All *CallExpression*s whose callee is an *Identifier* and its name is `done`.
2. Then in the children of those nodes we need to look for *CallExpression*s whose callee is an *Identifier* and its name is `it`.
3. Now take the 2nd argument of `it`, it's a *FunctionExpression*. We'll replace this node with an async parameterless function.
4. In the body of this function, we look for *CallExpression*s whose callee is an *Identifier* with a name equal to `done` and remove them.

## 3. Implementing the codemod

We can use [jscodeshift-ts-template](https://github.com/labarilem/jscodeshift-ts-template) to get started. It's a GitHub template repository I made, based on the official jscodeshift conventions to build transformers. The template includes tests and a debug config for VS Code too.

The full implementation is in my [ts-codemods](https://github.com/labarilem/ts-codemods) repository. Let's go over it step by step:

1. Finding `describe` calls. We also check the arguments count and types.

    ```typescript
    const itFuncCalls = root
      .find(j.CallExpression, {
        callee: {
          type: 'Identifier',
          name: 'describe'
        },
      })
      .filter(path => path.node.arguments.length === 2 &&
        path.node.arguments[0].type !== 'FunctionExpression' &&
        path.node.arguments[1].type === 'FunctionExpression'
      )
    ```

2. Finding `it` calls inside the `describe`s. We also check the arguments count and types and exclude async calls. There is no need to transform them as we assume they have already been migrated to async/await.

    ```typescript
    // ...
    // find all child func expression
    // it calls will always be inside the 2nd argument of describe
    .find(j.FunctionExpression)
    // find all 'it' func calls
    .find(j.CallExpression, path => {
      // allow it func calls
      const isItCall = ((path.callee.type === 'Identifier' &&
        path.callee.name === 'it') ||
        // allow it.skip calls
        (path.callee.type === 'MemberExpression' &&
          path.callee.object.type === 'Identifier' &&
          path.callee.object.name === 'it' &&
          path.callee.property.type === 'Identifier' &&
          path.callee.property.name === 'skip')) &&
        // check args
          path.arguments.length === 2 &&
          path.arguments[0].type !== 'FunctionExpression' &&
          (path.arguments[1].type === 'FunctionExpression' ||
            path.arguments[1].type === 'ArrowFunctionExpression') &&
          path.arguments[1].async === false;
      return isItCall;
    });
    ```

    we want to include `it.skip` calls too in our transformation.
    Also, note that we allow *ArrowFunctionExpression*s too as arguments.

3. Replacing the callback parameter of `it` calls with a parameterless async function.

    ```typescript
    // ...
    itFuncCalls.forEach(call => {
        const funcArg = (call.node.arguments[1] as FunctionExpression);
        const asyncFuncArg = j.functionExpression.from({
          async: true,
          body: funcArg.body,
          params: []
        });
        const newCallArgs = [call.node.arguments[0], asyncFuncArg];
        call.replace(j.callExpression(call.node.callee, newCallArgs))
      });
    ```

4. Removing all `done` calls inside `it`s callbacks.

    ```typescript
    // ...

    function isOnlyStatementInArrowFunc(path: ASTPath<any>) {
      const parentBody = path.parent?.node?.body;
      const parentType = path.parent?.node?.type;
      return parentBody
        && parentType === 'ArrowFunctionExpression'
        && (!Array.isArray(parentBody) || parentBody.length === 1);
    }

    const doneCalls = itFuncCalls.find(j.FunctionExpression)
      .find(j.CallExpression, {
        callee: {
          type: 'Identifier',
          name: 'done'
        },
      });
    doneCalls.forEach(call => {
      // remove the relevant func calls we've found
      if (isOnlyStatementInArrowFunc(call))
        j(call).replaceWith(j.blockStatement([]));
      else
        j(call).remove();
    });
    ```

    Sometimes removing the call is not a good idea. One particular case is when the call is the only statement in an arrow function's body. If you remove the call in that case, the new arrow function in the AST will be rendered like this:
    ```typescript
    setTimeout(() => /* there was a 'done()' here */);
    ```
    thus generating an invalid Typescript code. So, in this case, we replace the whole body of the arrow function with an empty block, ending up with:
    ```typescript
    setTimeout(() => {});
    ```
    which compiles without errors.
  
5. Writing the output code, with a final check to prevent writing when there are no changes to the input AST.

    ```typescript
    return itFuncCalls.size() > 0 ? root.toSource({ lineTerminator: '\n', quote: 'single' }) : null;
    ```

### 4. Running tests

Now we're ready to run our tests with Jest:

```sh
$ npm test
```

```sh
> ts-codemods@1.0.0 test
> jest

 PASS  __tests__/async-it.test.ts
  async-it transformer
    ./src/async-it
      √ transforms correctly using "async-it/callback-rm" data (145 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.186 s, estimated 3 s
Ran all test suites.
```

Everything looks good, so our workflow ends here. Of course, in a real-world scenario, you might want to add more than 1 test to be sure your codemod does its job without breaking the source code.

## Conclusion

We showed how to implement our transformer with jscodeshift. It can effectively reduce the SW engineer's effort when migrating large Mocha test suites.

You can find the full implementation and a ready-to-use script in the [ts-codemods](https://github.com/labarilem/ts-codemods) repository. If you have any questions/suggestions/ideas, please feel free to open an issue there.

See you at the [next article](https://marcolabarile.me/my%20projects/2023/10/13/automating-callbacks-to-async-migration-in-mocha-tests-2) of this series!
