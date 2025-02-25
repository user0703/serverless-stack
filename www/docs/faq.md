---
title: Frequently Asked Questions
description: "Frequently asked questions about Serverless Stack (SST)"
---

import config from "../config";

### Do we need another framework for serverless?

While [Serverless Framework](https://github.com/serverless/serverless) and [SAM](https://github.com/aws/serverless-application-model) have been around for a while and are quite popular, the local development experience for them isn't great. And they require you to define your resources using the really verbose [CloudFormation YAML](https://serverless-stack.com/chapters/what-is-infrastructure-as-code.html#aws-cloudformation) (or JSON).

In comparison, SST features:

- A [Live Lambda Development](live-lambda-development.md) environment, that introduces a completely new local development experience for serverless.
- And it uses [AWS CDK](https://serverless-stack.com/chapters/what-is-aws-cdk.html), allowing you to define your resources using regular programming languages.

We think these two details, drastically sets SST apart from the rest of the options out there.

### Can I use all the CDK constructs in SST?

Yes. The only caveats are:

- [`sst.App`](constructs/App.md) is included by default and is used in place of `cdk.App`.
- [`sst.Stack`](constructs/Stack.md) is necessary for SST to be able to [deploy to multiple stages](deploying-your-app.md#deploying-to-a-stage) and is used in place of `cdk.Stack`.
- [`sst.Function`](constructs/Function.md) is necessary for the [Live Lambda Development](live-lambda-development.md) environment. But if you don't need that you can use the CDK function constructs.

### Do I have to use the SST higher-level constructs?

No, you don't have to use them. But they can be really handy when building out your serverless app. For example, the [`sst.Api`](constructs/Api.md) construct gives you a really nice interface for defining your routes and giving them permissions.

```js
const api = new Api(this, "Api", {
  routes: {
    "GET  /notes": "src/list.main",
    "POST /notes": "src/create.main",
  },
});

api.attachPermissions(["s3", "dynamodb"]);
```

### Is SST ready to be used in production?

SST is still under active development. And we are constantly fixing bugs and supporting new use cases and setups.

That said, we see SST being used in production by quite a few of our users over on [Seed](https://seed.run).

So feel free to give it a try and let us know if you run into any issues.

### Can I trust a new framework like SST for my projects at work?

SST is relatively new but [the team behind it](https://anoma.ly) has been working on serverless related projects for the past few years. The <a href={ config.guide } target="\_blank">Serverless Stack Guide</a> has been around since 2017. [Seed](https://seed.run) has been generating revenue since 2018. We are also backed by some of the [most prominent investors in Silicon Valley](https://anoma.ly).

So checkout <a href={ config.roadmap } target="\_blank">our public roadmap</a> to see where SST is headed.

### Is SST flexible enough to support my setup?

SST has a couple of defaults, like a built-in linter, type checker, and bundler. It also has a list of higher-level constructs.

You can disable the linter, provide your own TypeScript config, and disable bundling. You also don't have to use the higher-level constructs that SST has and just use the native CDK ones.

If you hit a limitation, feel free to hop into our <a href={ config.slack } target="\_blank">Slack community</a> and let us know about it.

### Why does SST not use CDK's built-in way to build Node.js functions?

CDK has a construct for building Lambda functions, [aws-cdk-lib/aws-lambda-nodejs](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_lambda_nodejs-readme.html). But SST does not use it. Here's why.

SST's [Live Lambda Development environment](live-lambda-development.md) allows you to test your Lambda functions live. To do this, it watches your file changes and transpiles your functions. While they are being transpiled, it blocks any requests that are made to them. To ensure a great experience, it needs to do this process as fast as possible. So we decided to use esbuild, since [it's the fastest option](https://esbuild.github.io/faq/#why-is-esbuild-fast). We also maintain an esbuild service internally and call it programmatically to make rebuilds as fast as possible.

It also makes sense to build the Lambda functions in a similar way while running `sst deploy`.

In addition, we also decided to use esbuild to transpile your CDK code, so you can use the same flavor of JS as your Lambda functions.

### How often is SST's version of CDK updated?

SST internally includes CDK, so you don't have to. We update this version fairly frequently. But if you need the latest version right away, open an issue and we'll push out an update.

### What's the connection to Serverless Framework?

Originally when SST was released, it was meant to be a way to [use CDK alongside your Serverless Framework apps](https://serverless-stack.com/chapters/using-aws-cdk-with-serverless-framework.html). While you can still do that. SST's [Live Lambda Development](live-lambda-development.md) makes it a first-class dev environment for serverless apps.

So you don't have to use Serverless Framework together with SST.
