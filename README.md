# create-dotnet-lambda-artifact-gh-action

Repository containing Ohpen's Github Action to apply Terraform configuration with Ohpen standard.

- [create-dotnet-lambda-artifact-gh-action](#create-dotnet-lambda-artifact-gh-action)
  - [code-of-conduct](#code-of-conduct)
  - [github-action](#github-action)

## code-of-conduct

Go crazy on the pull requests :) ! The only requirements are:

> - Use _conventional-commits_.
> - Include _jira-tickets_ in your commits.
> - Create/Update the documentation of the use case you are creating, improving or fixing. **[Boy scout](https://biratkirat.medium.com/step-8-the-boy-scout-rule-robert-c-martin-uncle-bob-9ac839778385) rules apply**. That means, for example, if you fix an already existing workflow, please include the necessary documentation to help everybody. The rule of thumb is: _leave the place (just a little bit)better than when you came_.

### github-action

This action performs a [_dotnet lambda package_](https://github.com/aws/aws-extensions-for-dotnet-cli#package) on the specified lambda. The (required) inputs are:

- _region_: aws region name.
- _access-key_: user access key to be used.
- _secret-key_: user secret key to be used.
- _account: AWS account id.
- _role-name: Role to assume before uploading lambda package to s3.
- _version: Service version that the package(artifact) represents.
- _service-name: Name of the service that the package represents.
- _function-project-folder: Folder where the lambda .csproj file is located.
- _function-project-name: Name of the lambda function.
- _application-framework: framework name in which lambda package will be build.
- _bucket-name: Bucket where lambda package will be uploaded.

Here is an example:

```yaml
name: CI
on:
  pull_request:
    branches: ["main"]
jobs:
  upload-lambda-artifact:
    needs: [build]
    runs-on: ubuntu-latest
    strategy:
      matrix:
        include:
          - folder: "src/Functions/ExampleFunction"
            name: "ExampleFunction"
          - folder: "src/Functions/HealthCheck"
            name: "HealthCheck"
          - folder: "src/WebApi"
            name: "WebApi"
    steps:
      - uses: actions/checkout@v2
      - name: dotnet tool install -g Amazon.Lambda.Tools
        run: dotnet tool install -g Amazon.Lambda.Tools --version 5.2.0
      - uses: ohpensource/create-dotnet-lambda-artifact-gh-action@0.0.0.1
        name: Create and upload lambda artifact
        with:
          region: $REGION
          access-key: $COR_AWS_ACCESS_KEY_ID
          secret-key: $COR_AWS_SECRET_ACCESS_KEY
          account: $ARTIFACTS_AWS_ACCOUNT_ID
          role-name: $COR_CICD_AUTOMATION_ROLE_NAME
          version: $GITHUB_HEAD_REF
          service-name: $SERVICE_NAME
          function-project-folder: ${{ matrix.folder }}
          function-project-name: ${{ matrix.name }}
          application-framework: "netcoreapp3.1"
          bucket-name: $ARTIFACTS_BUCKET_NAME
```
