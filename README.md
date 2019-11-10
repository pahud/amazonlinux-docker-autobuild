# amazonlinux-docker-autobuild

This repo provides a very simple `Dockerfile` that helps you build your own amazonlinux docker image with the latest security update.

# Use with AWS CDK

You may use [@pahud/cdk-autobuild-patterns](https://www.npmjs.com/package/@pahud/cdk-autobuild-patterns) to create your own AtuoBuild stack that contains:

- [x] A new **AWS CodeBuild** project with the github repo you specified as the build source
- [x] A new **Amazon ECR** private repo that stores the docker image built from the CodeBuild.
- [x] **Amazon CloudWatch Events** scheduled rule with the rate your specified(e.g. daily, weekly or even hourly)

## Example

```js
import cdk = require('@aws-cdk/core');
import autobuild = require('@pahud/cdk-autobuild-patterns');
import codebuild = require('@aws-cdk/aws-codebuild');
import events = require('@aws-cdk/aws-events');
 
 
const app = new cdk.App();
 
const env = {
  region: app.node.tryGetContext('region') || process.env.CDK_INTEG_REGION || process.env.CDK_DEFAULT_REGION,
  account: app.node.tryGetContext('account') || process.env.CDK_INTEG_ACCOUNT || process.env.CDK_DEFAULT_ACCOUNT
};
 
const autoBuild = new autobuild.AutoBuildStack(app, 'AutoBuild', {
  env,
  source: codebuild.Source.gitHub({
    owner: 'pahud',
    repo: 'amazonlinux-docker-autobuild'
  }),
  schedule: events.Schedule.rate(cdk.Duration.days(1)),
  repositoryName: 'amazonlinux-autobuild'
})
```


