## Solution

The CI/CD pipeline supports a hybrid on-prem/cloud application architecture, where a service runs on the company's infrastructure and communicates with cloud resources through an API. This setup includes web apps for internal administrative tasks as well as a separate customer-facing app hosted in the cloud.

### Outline

The project follows a monorepo structure, with services organized in separate directories.

The CI/CD process is triggered when commits are pushed to specific branches in the repository:

- `master` branch triggers the pipeline for the test environment.
- `production` branch triggers the pipeline for test, staging and production environments.

Any code linting is already handled by pre-commit scripts, ensuring code quality before it reaches the CI/CD pipeline.

Secrets, such as API keys or credentials, are stored as variables within GitLab CI settings. These secrets are referenced using environment variables when needed. Similarly, any variables that differ between the staging and production environments are stored as `<env>_<variable_name>` and then combined into a common environment variable for use in pipeline steps.

Container images that are built during the pipeline are stored in the GitLab Container Registry service, ensuring they are readily available for deployment.

The cloud infrastructure uses AWS services and is built and configured using the Serverless Framework for infrastructure as code (IaC).

Intemediary Docker images are created to reduce pipeline runtime by avoiding dependency installs/builds unless they have been updated.

### Improvements

In order to enhance the overall security of the CI/CD pipeline and the application:

- Security scanning of code and container images should be incorporated into the pipeline. This includes running code analysis tools and vulnerability scanners to identify any potential security flaws.
- Integration tests and acceptance tests should be given more focus and expanded to cover a wider range of scenarios to ensure the reliability and stability of the application. Unit tests have less significance as the code for microservices is typically simpler. Acceptance tests (end-to-end tests) provide more confidence but require a longer time to execute. Integration tests are a good trade-off as they require less time to run but provide an overall view of how the microservices will behave.
- Consider implementing canary deployment from the staging environment to the production environment. This approach allows a controlled rollout of new features or updates to a subset of users before a full release, mitigating any potential risks and giving an opportunity to collect feedback.
