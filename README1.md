# KGS DevOps Terraform Pipeline

## Description

This project is used as a pipeline reference for all the CTS: PM&A projects. This allows us to apply bulk updates to pipelines for all projects created for our group. 

## Table of Contents

- [Description](#description)
- [Dependencies](#dependencies)
- [Jobs Included](#jobs-included)
  - [Detailed Job Descriptions](#detailed-job-descriptions)
- [Design](#design)
- [How To Use](#how-to-use)
- [Environment Variables](#environment-variables)
- [Common Issues and Troubleshooting](#common-issues-and-troubleshooting)
  - [Common Issues](#common-issues)
  - [Troubleshooting](#troubleshooting)
- [Contribution Guidelines](#contribution-guidelines)
- [Contact Information](#contact-information)
- [Examples](#examples)
- [Versioning](#versioning)
- [Update Log](#update-log)


## Dependencies

* IAM Role that allows the KGS shared runners access to the account.
* The default tags in our pipeline (If you don't put any this will work as intended):
  * autoscale
  * ec2
  * kgs-shared

[Back to Top](#table-of-contents)

## Jobs Included

* Sast Scanner (`sast`, `validate-sast-report`)
* Initialize Terraform (`prepare`)
* Linting (`linting`)
    * Pylint
    * Yapf
    * eslint
    * jsonlint
    * Terraform Validation
* Planning (`build`)
* Deploying (`deploy`)
* Destroying (`destroy`)

### Detailed Job Descriptions
- **Sast Scanner**: Scans the source code for security vulnerabilities.
  - `sast`: Runs the SAST scan.
  - `validate-sast-report`: Validates the SAST report to ensure it meets the set thresholds.
- **Initialize Terraform**: Prepares the Terraform environment.
  - `prepare`: Initializes the Terraform configuration.
- **Linting**: Ensures code quality and consistency.
  - `pylint`: Runs pylint to lint Python code.
  - `yapf`: Runs yapf to format Python code.
  - `eslint`: Runs eslint to lint JavaScript code.
  - `jsonlint`: Validates JSON files.
  - `validate`: Validates the Terraform configuration.
- **Planning**: Generates an execution plan for Terraform.
  - `plan`: Runs `terraform plan` to create an execution plan.
- **Deploying**: Applies the Terraform plan to deploy infrastructure.
  - `apply`: Runs `terraform apply` to apply the execution plan.
- **Destroying**: Destroys the Terraform-managed infrastructure.
  - `dev-destroy`: Destroys infrastructure in development environments.
  - `prod-destroy`: Destroys infrastructure in production environments.

[Back to Top](#table-of-contents)

## Design

[Back to Top](#table-of-contents)

## How To Use

When using this, please ensure you have tags set to `default` in your `.gitlab-ci.yml` script if you do not have access to this job's default runner tags.

Add the minimum config for `variables`, `include`, and `stages`, sections in your `.gitlab-ci.yml`:

```yaml
variables:
  SAST_SEVERITY_THRESHOLD: High
  TF_BASE_COMMAND: ''
  TF_EXTRA_COMMAND: ''

include:
  project: 'kgs-dev-ops/devops-terraform-pipeline'
  file: 'gitlab-terraform-ci.yml'

stages:
  - sast
  - validate-sast-report
  - prepare
  - linting
  - build
  - deploy
  - destroy
```

[Back to Top](#table-of-contents)

## Environment Variables

- **`SAST_SEVERITY_THRESHOLD`**: Sets the severity threshold for the SAST scan (e.g., `High`).
- **`TF_BASE_COMMAND`**: Base Terraform command (default is empty).
- **`TF_EXTRA_COMMAND`**: Additional Terraform command options (default is empty).
- **`CI_PROJECT_DIR`**: Directory where the project is checked out.
- **`CI_API_V4_URL`**: URL for the GitLab API.
- **`CI_PROJECT_ID`**: ID of the GitLab project.
- **`CI_COMMIT_REF_NAME`**: Name of the branch being built.
- **`CI_DEFAULT_BRANCH`**: Default branch of the project.

[Back to Top](#table-of-contents)

## Common Issues and Troubleshooting

### Common Issues
1. **IAM Role Issues**: Ensure the IAM Role allows the KGS shared runners access to the account.
2. **Missing Tags**: Make sure to include the default tags (`autoscale`, `ec2`, `kgs-shared`) in your pipeline configuration.
3. **Pipeline Failures**: Check the pipeline logs for detailed error messages.

### Troubleshooting
1. **Check IAM Role Permissions**: Verify IAM Role permissions are correctly configured.
2. **Review Pipeline Logs**: Check the logs of each job to identify the root cause of failures.
3. **Validate Environment Variables**: Ensure all required environment variables are correctly set.

[Back to Top](#table-of-contents)

## Contribution Guidelines

We welcome contributions from team members. Please follow these guidelines for contributing:

1. **Coding Standards**: Follow the coding standards and best practices used in the project.
2. **Submit Pull Requests**: When submitting changes, please create a pull request and request a review from a project maintainer.
3. **Documentation**: Update the documentation (including this README) as necessary to reflect changes made in the code.
4. **Testing**: Ensure that all tests pass and that new features include appropriate test coverage.

[Back to Top](#table-of-contents)

## Contact Information

For support or questions, please contact:

- **DevOps Team**: [devops-team@example.com](mailto:devops-team@example.com)
- **Support Channel**: [Link to support channel]

[Back to Top](#table-of-contents)

## Examples

Here's an example of a `.gitlab-ci.yml` configuration for a basic project:

```yaml
variables:
  SAST_SEVERITY_THRESHOLD: High
  TF_BASE_COMMAND: ''
  TF_EXTRA_COMMAND: ''

include:
  project: 'kgs-dev-ops/devops-terraform-pipeline'
  file: 'gitlab-terraform-ci.yml'

stages:
  - sast
  - validate-sast-report
  - prepare
  - linting
  - build
  - deploy
  - destroy

linting:
  stage: linting
  script:
    - echo "Running linting..."

build:
  stage: build
  script:
    - echo "Building project..."

deploy:
  stage: deploy
  script:
    - echo "Deploying project..."
```

[Back to Top](#table-of-contents)

## Versioning

To reference a specific version of the pipeline, update the include section in your .gitlab-ci.yml file to point to the desired version:

```
include:
  project: 'kgs-dev-ops/devops-terraform-pipeline'
  file: 'gitlab-terraform-ci.yml'
  ref: 'v1.2.3'  # Replace with the desired version
```

[Back to Top](#table-of-contents)

## Update Log

### 2024-09-11 - Jacob Pierce - STRY0110336
 - Summary of Changes:
   - Added a condition to ensure that the apply job only runs if all preceding stages succeed.
   - Allowed jsonlint job to fail without stopping the pipeline.
 - Reason for Changes:
   - To prevent deployment attempts when build or any critical linting stage fails.
   - To allow non-critical jsonlint to fail without halting the pipeline.

[Back to Top](#table-of-contents)
