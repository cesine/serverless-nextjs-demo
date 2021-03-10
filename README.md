# From Serverless Next.js Demo Instructions

https://github.com/serverless-nextjs/serverless-next.js/tree/master/packages/serverless-components/nextjs-component/examples/create-next-app

# Development

- `nvm use`
- `npm i`
- `npm run dev`
- Open in browser: `http://localhost:3000/nextjs-demo/0.0.0`

## Testing Locally with AWS SAM CLI

- `aws-vault exec [profile] -- code .` to startup VS Code with AWS env set
- `serverless` to setup the deploy directory
- `make copy-router` to copy router code locally
- `make sam-debug`
- Load in browser: http://127.0.0.1:3000/app/1.2.3
- Start 'Attach to SAM CLI' Debug Profile
- At this point the container should startup, debugger will attach, and breakpoints will work

## Install Modules

`npm i`

## Build App

`npm run build`

## Delete old S3 Bucket

`aws-vault exec pwrdrvr-admin -- aws s3 rm --recursive s3://66rb0ct-9zcltne/`

## Publishing - Next.js Serverless

```
# Publish the App
rm .env # Can't have a .env... it messes up serverless
export AWS_REGION=us-east-1
aws-vault exec pwrdrvr-admin -- npx serverless

# Remove all files from S3 bucket if needed
aws-vault exec pwrdrvr-admin -- aws s3 rm --recursive s3://66rb0ct-9zcltne/
```

## Publishing - MicroApp

- Update Lambda function code and create version alias
  - Locations to change
    - Makefile: `IMAGE_TAG` (1.0.0) and `LAMBDA_ALIAS` (v_0_0_1)
    - deploy.json
    - next.config.js
    - package.json (not required, but nice)
  - Note: this compiles the code and builds the docker image
  - `aws-vault exec pwrdrvr-admin -- make aws-ecr-login`
  - `aws-vault exec pwrdrvr-admin -- make aws-ecr-publish-svc`
  - Create first time / new version number
    - `aws-vault exec pwrdrvr-admin -- make aws-create-alias-svc`
  - Update version alias if already exists
    - `aws-vault exec pwrdrvr-admin -- make aws-update-alias-svc`
- Deploy the updated HTML and link to lambda from api gateway
  - `export AWS_REGION=us-east-2`
  - `aws-vault exec pwrdrvr-admin -- dotnet run --project ~/pwrdrvr/microapps-cdk/src/PwrDrvr.MicroApps.DeployTool/`
- Cleanup failed publish
  - `aws-vault exec pwrdrvr-admin -- aws s3 rm --recursive s3://pwrdrvr-apps/nextjs-demo/`
  - `aws-vault exec pwrdrvr-admin -- aws s3 rm --recursive s3://pwrdrvr-apps-staging/nextjs-demo/`
