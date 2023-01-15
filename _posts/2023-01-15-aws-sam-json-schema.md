---
title: "AWS SAM JSON Schema in VS Code"
excerpt: >
  The AWS team has released the JSON Schema for SAM templates. Let's try it out in VS Code.
date: 2023-01-14
last_modified_at: 2023-01-15
preview:
  image: /assets/images/2023-01-15-aws-sam-json-schema/vscode.jpg
categories:
  - News
tags:
  - AWS
share: false
---

The AWS team has released the JSON Schema for SAM templates. It had a [bug](https://github.com/aws/serverless-application-model/discussions/2645#discussioncomment-4236068) initially but now it's working flawlessly.

The official schema will help you write SAM templates faster, without checking the AWS docs every time you need to use a new property.

Here's how to use it in VS Code:

1. Make sure the [YAML](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml) extension is installed.
2. Add the following line to the top of your YAML template file:
```
# yaml-language-server: $schema=https://raw.githubusercontent.com/aws/serverless-application-model/main/samtranslator/schema/schema.json
```

And it's done! Now you will get suggestions from VS Code:

![VS Code SAM Schema](/assets/images/2023-01-15-aws-sam-json-schema/vscode.jpg){: class="center"}
