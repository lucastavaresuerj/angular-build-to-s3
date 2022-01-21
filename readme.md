I use this two repos as references for create this project:

- https://github.com/lyraddigital/angular-devops, wich has a video: https://www.youtube.com/watch?v=CbldTE7xGy8
- https://github.com/quick-refs/github-aws-cicd, wich also has a video: https://www.youtube.com/watch?v=PIPtfjKaB58

# Steps

## Configure Github

### Create a WebHook Token for AWS

- On your github account go to [developer settings](https://github.com/settings/tokens)
- Generate a new personal access token
  - On Note give a name like `AWS-TOKEN`
  - On Expiration you can set for `No expiration`
  - On Select scopes:
    - Select the checkbox for `repo`
    - Select the checkbox for `admin:repo_hook`
  - Click on `Generate token` button
  - Copy the persornal access token, **it only will appear this time**

### Copy or Fork this repository

Make sure that all files of this repository are in your repository

## Configure AWS

### AWS Secrets Manager

This service will store your webhook token from github and will provide the value on cloudFormation template [cf_codebuild.yaml](https://github.com/lucastavaresuerj/angular-build-to-s3/blob/main/cf_codebuild.yaml)

- Go to [AWS Secrets Manager
  ](https://console.aws.amazon.com/secretsmanager/home?region=us-east-1#!/home)
- Select `Store a new secret`
- On Secret type, change for `Other type of secret`
- On Key/value:
  - Give the `key` a name, is `GITHUB_ACCESS_TOKEN`
  - For `value`, the token you saved whem you created a WebHook Token for AWS at Gitbuh
- Select `next`
- On Secret name, give a name for you secret, in the template is `github-access-token`
- Select `next`
- Select `next` again
- Select `Store`

Now we are good to create the stack on CloudFormation

### Create a Stack at CloudFormation

- Go to [AWS CloudFormation page](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/)
- Select `Create stack`
- On `Specify template` section, select `Upload a template file` and then `Choose file`, the file is [cf_codebuild.yaml](https://github.com/lucastavaresuerj/angular-build-to-s3/blob/main/cf_codebuild.yaml)
- Select `Next`
- On `Stack name`, give a name for your stack
- On `Parameters` section:
  - Leave `CodeBuildEnvironmentImage` and `GitHubBranch` untouched
  - On `GitHubOwner` write your GitHub username, in my case, I wrote `lucastavaresuerj`
  - On `GitHubRepository`, write the name of the repo that has your code, in my case, I wrote `angular-build-to-s3`
- Select `Next`
- Select `Next` again
- Check the checkbox `I acknowledge that AWS CloudFormation might create IAM resources.`, so you acknowledge that CloudFormation might create IAM resources
- Select `Create stack`

## Deploy

- Make a change at the projete and commit to the `main` branch with message like `[build] some change`, it will only trigger the CodeBuild if the `[build]` string is present at the commit message
- The code build will start to... build your angular projet and will save at a S3 bucket wich is beeing used for host the web server. You can find this web server url at your cloud formation statck `Outputs` tab at the key `S3BucketSecureURL`
