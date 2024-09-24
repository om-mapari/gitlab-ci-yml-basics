
You: 

Help me to learn gitlab ci yaml pipelines. I am completely beginner. 
List down all the topic I should learn.

ChatGPT: 

Here’s a list of topics you should learn to get started with GitLab CI YAML pipelines as a beginner:
 
1. **GitLab Basics** :
  - Overview of GitLab and CI/CD concepts

  - Setting up a GitLab project
 
2. **Introduction to GitLab CI** :
  - What is GitLab CI/CD?

  - GitLab runners and types (shared vs. specific runners)

  - YAML basics (syntax and structure)
 
3. **.gitlab-ci.yml Basics** : 
  - Creating a `.gitlab-ci.yml` file

  - Basic pipeline structure (stages, jobs, scripts)
 
  - Using `stages` to define pipeline flow
 
  - Defining `jobs` (name, script, stage, etc.)
 
4. **Pipeline Execution** :
  - How pipelines are triggered (push, merge request, schedule, etc.)

  - Job execution and parallelism

  - Pipeline status and logs
 
5. **Using Variables** : 
  - Defining and using environment variables (`CI variables`, `custom variables`)

  - Secrets and sensitive data management
 
6. **Caching and Artifacts** : 
  - Using `cache` to speed up pipelines
 
  - Defining and using `artifacts` (for build outputs, test reports, etc.)
 
7. **Environments and Deployments** : 
  - Using `environments` to define different deployment targets

  - Auto DevOps and environment-specific deployments
 
8. **Pipeline Optimization** : 
  - Using `only` and `except` rules to limit when jobs run
 
  - Job dependencies (`needs`, `dependencies`)
 
  - Retrying jobs (`retry` option)
 
9. **Testing and Quality Gates** :
  - Running unit tests and generating test reports
 
  - Using `rules` for conditional execution

  - Implementing quality gates (e.g., linting, code analysis)
 
10. **Advanced Features** :
 
- Using `include` to reuse pipeline code

- Matrix builds and dynamic pipelines

- Child pipelines and multi-project pipelines
 
1. **Security and Compliance** :

- Security scanning (SAST, DAST, Dependency Scanning)

- Compliance pipelines and approval rules
 
1. **Monitoring and Notifications** :

- Pipeline monitoring and analytics

- Setting up notifications (email, Slack, etc.)
 
1. **Troubleshooting and Debugging** :

- Common pipeline issues and how to debug failures

- Using the CI/CD pipeline UI for troubleshooting
 
1. **Integration with Cloud Services** :

- Deploying to cloud environments (AWS, Azure, GCP)

- Kubernetes integration and Helm charts

Learning these topics will give you a solid foundation in GitLab CI pipelines. Let me know if you’d like more detailed explanations or examples on any of them!

You: 

What should i learn in .gitlab-ci basics

ChatGPT: 

In the **.gitlab-ci.yml basics** , you should focus on understanding the following core concepts:1. **.gitlab-ci.yml File Structure**  
- Learn the basic syntax and structure of the `.gitlab-ci.yml` file.

- YAML syntax (indentation, key-value pairs, etc.)

- Proper formatting for pipelines to function correctly.
2. **Stages**  
- Understanding `stages` and how they define the sequence of the pipeline.
 
- Example stages: `build`, `test`, `deploy`.

- How to create custom stages.

- The concept of sequential execution based on stages.


```yaml
stages:
  - build
  - test
  - deploy
```
3. **Jobs**  
- Defining `jobs` which are the individual steps within a stage.
 
- Each job should have a `script` to run commands or tasks.
 
- Associating jobs with a specific `stage`.


```yaml
job_name:
  stage: build
  script:
    - echo "Building the project"
```
4. **Script**  
- The `script` keyword specifies the list of commands a job will execute.

- You can run shell commands, scripts, or any other tasks.

- Multiple commands can be listed sequentially.


```yaml
job_name:
  script:
    - echo "This is a job"
    - echo "Running multiple commands"
```
5. **Runners** 
- Understanding GitLab Runners, which are used to execute your pipeline jobs.

- Difference between shared runners and specific runners.

- Learn how jobs are picked up by runners and executed.
6. **Artifacts**  
- Using `artifacts` to define files that should be saved after a job finishes.

- These can include build files, logs, or other outputs.

- Artifacts are used for passing data between jobs or stages.


```yaml
job_name:
  script:
    - echo "Building files"
  artifacts:
    paths:
      - build/
```
7. **Cache**  
- Use `cache` to store dependencies and speed up pipeline executions.

- Caching can prevent the need to re-download or rebuild the same resources for every job.


```yaml
cache:
  paths:
    - node_modules/
```
8. **Job Execution Order** 
- Jobs in the same stage run in parallel.

- Jobs in subsequent stages wait for jobs in previous stages to complete.

- Learn the order of execution and how stages determine job flow.
9. **Basic Pipeline Triggering** 
- Understanding how pipelines are triggered automatically (e.g., on code push or merge request).

- You can also manually trigger pipelines or schedule them at set intervals.
10. **Pipeline Visualization** 
- Learn how to visualize the pipeline in GitLab’s UI.

- GitLab provides a visual representation of the pipeline flow, job statuses, and logs.

By mastering these basic concepts, you’ll be able to write and manage simple pipelines in GitLab. Let me know if you want a hands-on example!

You: 

Explain 8th point Job execution order in details

ChatGPT: 

### Job Execution Order in GitLab CI Pipelines 
The **job execution order**  in GitLab CI/CD pipelines depends on how jobs are assigned to stages. The pipeline is executed based on the stages, and jobs within each stage have specific rules regarding their execution order.
Here’s a detailed breakdown of how this works:


---

1. **Jobs in the Same Stage Run in Parallel**  
- **Parallel Execution** : All jobs assigned to the same stage are executed **in parallel**  (i.e., at the same time), provided enough runners are available.
 
- **No dependency**  between jobs in the same stage unless explicitly defined.
 
- Example: If you have three jobs (`job1`, `job2`, and `job3`) in the `test` stage, they will all run simultaneously if runners are available.


```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project"

test_job1:
  stage: test
  script:
    - echo "Running test 1"

test_job2:
  stage: test
  script:
    - echo "Running test 2"

deploy_job:
  stage: deploy
  script:
    - echo "Deploying the project"
```

In this example:
 
- The `test_job1` and `test_job2` run **in parallel**  after the `build_job` completes, assuming both have available runners.


---

2. **Jobs in Different Stages Run Sequentially**  
- **Sequential Execution** : Stages are executed **in sequence** , meaning the jobs in the `next stage` will not start until all jobs in the current stage finish.
 
- For example, if the pipeline has the stages `build`, `test`, and `deploy`, the `test` stage will only start once all jobs in the `build` stage are complete.
If any job in a stage fails, GitLab will **not proceed**  to the next stage by default, causing the entire pipeline to stop unless otherwise specified.

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project"

test_job:
  stage: test
  script:
    - echo "Running tests"

deploy_job:
  stage: deploy
  script:
    - echo "Deploying the project"
```

In this example:
 
- `build_job` runs first, completing the `build` stage.
 
- Once `build_job` finishes, the `test_job` starts as part of the `test` stage.
 
- Once the `test_job` is complete, the `deploy_job` will start.


---

3. **Job Dependencies Between Stages**  
- You can define dependencies between jobs in different stages using the `needs` keyword or by explicitly managing artifacts passed between jobs. This allows a job in a later stage to run only after its dependent jobs finish successfully.

Example with dependencies:


```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project"
  artifacts:
    paths:
      - build_output/

test_job:
  stage: test
  dependencies:
    - build_job
  script:
    - echo "Testing with build output"
    - ls build_output/

deploy_job:
  stage: deploy
  script:
    - echo "Deploying"
```

In this example:
 
- `test_job` depends on `build_job`, which means it will wait until `build_job` finishes and use its artifacts.
 
- `deploy_job` will run after `test_job` completes successfully.


---

4. **Job Execution with Conditional Rules**  
- You can define **conditional rules**  for jobs to run based on certain criteria like branch names, commit tags, or manual triggers using `only`, `except`, and `rules` directives.

- This can influence job execution order by skipping or running certain jobs depending on the conditions.


```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project"

test_job:
  stage: test
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
  script:
    - echo "Running tests on main branch"

deploy_job:
  stage: deploy
  script:
    - echo "Deploying the project"
```

In this example:
 
- The `test_job` will only run if the commit is on the `main` branch.

- This gives you control over when certain jobs should or shouldn’t run, impacting the overall pipeline flow.


---

5. **Failure Impact on Pipeline Execution** 
- If a job in a stage fails, GitLab will by default stop the pipeline from progressing to the next stage. This means that no further jobs will be executed in subsequent stages.
 
- To handle failures, you can use the `allow_failure` option for specific jobs, letting the pipeline proceed even if a particular job fails.


```yaml
build_job:
  stage: build
  script:
    - echo "Building"

test_job:
  stage: test
  script:
    - exit 1
  allow_failure: true

deploy_job:
  stage: deploy
  script:
    - echo "Deploying"
```

In this case:
 
- `test_job` is allowed to fail (`exit 1`), but the pipeline will still proceed to the `deploy_job`.


---

6. **
### Job Execution Order in GitLab CI Pipelines 
The **job execution order**  in GitLab CI/CD pipelines depends on how jobs are assigned to stages. The pipeline is executed based on the stages, and jobs within each stage have specific rules regarding their execution order.
Here’s a detailed breakdown of how this works:


---

1. **Jobs in the Same Stage Run in Parallel**  
- **Parallel Execution** : All jobs assigned to the same stage are executed **in parallel**  (i.e., at the same time), provided enough runners are available.
 
- **No dependency**  between jobs in the same stage unless explicitly defined.
 
- Example: If you have three jobs (`job1`, `job2`, and `job3`) in the `test` stage, they will all run simultaneously if runners are available.


```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project"

test_job1:
  stage: test
  script:
    - echo "Running test 1"

test_job2:
  stage: test
  script:
    - echo "Running test 2"

deploy_job:
  stage: deploy
  script:
    - echo "Deploying the project"
```

In this example:
 
- The `test_job1` and `test_job2` run **in parallel**  after the `build_job` completes, assuming both have available runners.


---

2. **Jobs in Different Stages Run Sequentially**  
- **Sequential Execution** : Stages are executed **in sequence** , meaning the jobs in the `next stage` will not start until all jobs in the current stage finish.
 
- For example, if the pipeline has the stages `build`, `test`, and `deploy`, the `test` stage will only start once all jobs in the `build` stage are complete.
If any job in a stage fails, GitLab will **not proceed**  to the next stage by default, causing the entire pipeline to stop unless otherwise specified.

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project"

test_job:
  stage: test
  script:
    - echo "Running tests"

deploy_job:
  stage: deploy
  script:
    - echo "Deploying the project"
```

In this example:
 
- `build_job` runs first, completing the `build` stage.
 
- Once `build_job` finishes, the `test_job` starts as part of the `test` stage.
 
- Once the `test_job` is complete, the `deploy_job` will start.


---

3. **Job Dependencies Between Stages**  
- You can define dependencies between jobs in different stages using the `needs` keyword or by explicitly managing artifacts passed between jobs. This allows a job in a later stage to run only after its dependent jobs finish successfully.

Example with dependencies:


```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project"
  artifacts:
    paths:
      - build_output/

test_job:
  stage: test
  dependencies:
    - build_job
  script:
    - echo "Testing with build output"
    - ls build_output/

deploy_job:
  stage: deploy
  script:
    - echo "Deploying"
```

In this example:
 
- `test_job` depends on `build_job`, which means it will wait until `build_job` finishes and use its artifacts.
 
- `deploy_job` will run after `test_job` completes successfully.


---

4. **Job Execution with Conditional Rules**  
- You can define **conditional rules**  for jobs to run based on certain criteria like branch names, commit tags, or manual triggers using `only`, `except`, and `rules` directives.

- This can influence job execution order by skipping or running certain jobs depending on the conditions.


```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project"

test_job:
  stage: test
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
  script:
    - echo "Running tests on main branch"

deploy_job:
  stage: deploy
  script:
    - echo "Deploying the project"
```

In this example:
 
- The `test_job` will only run if the commit is on the `main` branch.

- This gives you control over when certain jobs should or shouldn’t run, impacting the overall pipeline flow.


---

5. **Failure Impact on Pipeline Execution** 
- If a job in a stage fails, GitLab will by default stop the pipeline from progressing to the next stage. This means that no further jobs will be executed in subsequent stages.
 
- To handle failures, you can use the `allow_failure` option for specific jobs, letting the pipeline proceed even if a particular job fails.


```yaml
build_job:
  stage: build
  script:
    - echo "Building"

test_job:
  stage: test
  script:
    - exit 1
  allow_failure: true

deploy_job:
  stage: deploy
  script:
    - echo "Deploying"
```

In this case:
 
- `test_job` is allowed to fail (`exit 1`), but the pipeline will still proceed to the `deploy_job`.


---

6. Job Execution with `needs`**  
- Using the `needs` keyword allows jobs to skip the stage order and run as soon as their dependent jobs are done.

- This allows you to run jobs in parallel across stages, optimizing pipeline execution time.


```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building"

test_job:
  stage: test
  script:
    - echo "Testing"

deploy_job:
  stage: deploy
  needs: ["build_job"]  # deploy can run after build without waiting for tests
  script:
    - echo "Deploying"
```

Here:
 
- The `deploy_job` starts as soon as `build_job` finishes, even if `test_job` is still running.


---


### Summary of Key Points: 
 
- **Same stage jobs** : Run in parallel.
 
- **Different stage jobs** : Run sequentially, with the next stage waiting for all jobs in the current stage to complete.
 
- **Job dependencies** : Can be explicitly defined to control execution flow between jobs across stages.
 
- **Failure handling** : Pipelines can be configured to stop or continue after a failure.
 
- **Parallel across stages** : Jobs can run across stages using the `needs` keyword, bypassing the sequential stage order.

This understanding helps you design efficient pipelines that maximize the use of resources and minimize execution time!

You: 

How to define variable in jobs

ChatGPT: 

In GitLab CI, you can define variables at multiple levels (pipeline, job, project) and use them within jobs. Here's how you can define variables specifically **in jobs** :1. **Defining Variables within a Job** You can define variables inside a specific job using the `variables` keyword. These variables are only available for that job and override any project or pipeline-level variables if they have the same name.
### Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  variables:
    BUILD_ENV: "production"  # Variable defined inside this job
    TIMEOUT: "60"
  script:
    - echo "Building project in $BUILD_ENV environment"
    - echo "Timeout is set to $TIMEOUT seconds"

test_job:
  stage: test
  script:
    - echo "Running tests"
```

In this example:
 
- The `BUILD_ENV` and `TIMEOUT` variables are defined inside `build_job`.
 
- You can reference these variables in the job's `script` section using `$VARIABLE_NAME`.


---

2. **Using Predefined GitLab Variables** 
GitLab provides a set of predefined environment variables that you can use inside jobs. For example:


```yaml
build_job:
  stage: build
  script:
    - echo "Pipeline ID is $CI_PIPELINE_ID"
    - echo "Running on branch $CI_COMMIT_BRANCH"
```
Predefined variables like `$CI_PIPELINE_ID` and `$CI_COMMIT_BRANCH` are automatically set by GitLab and can be used in any job.

---

3. **Environment-Specific Variables in Jobs** 
You can use variables to define environment-specific settings, such as different settings for development, staging, or production environments.


```yaml
stages:
  - deploy

deploy_dev:
  stage: deploy
  variables:
    ENVIRONMENT: "development"
  script:
    - echo "Deploying to $ENVIRONMENT"

deploy_prod:
  stage: deploy
  variables:
    ENVIRONMENT: "production"
  script:
    - echo "Deploying to $ENVIRONMENT"
```

In this example:
 
- The `ENVIRONMENT` variable is defined differently for each job (`deploy_dev` and `deploy_prod`).

- The correct value is used in the job's deployment process.


---

4. **Overriding Variables** Variables defined at the **job level**  override: 
- **Global**  (pipeline-level) variables
 
- **Project-level**  variables
 
- **Group-level**  variables

Example:


```yaml
variables:
  GLOBAL_VAR: "global_value"

stages:
  - build

build_job:
  stage: build
  variables:
    GLOBAL_VAR: "local_value"  # Overriding global variable
  script:
    - echo "GLOBAL_VAR is $GLOBAL_VAR"
```

In this example:
 
- `GLOBAL_VAR` is overridden in `build_job` and has a value of `local_value` instead of `global_value`.


---

5. **Defining Variables in the Job and Accessing Them** 
To define variables in a job and access them within the job:
 
1. **Define the variable**  under `variables`.
 
2. **Access**  the variable using `$` syntax in the `script`.

### Example: 


```yaml
stages:
  - build

build_job:
  stage: build
  variables:
    APP_ENV: "development"
    DEBUG: "true"
  script:
    - echo "Building in $APP_ENV environment"
    - if [ "$DEBUG" == "true" ]; then echo "Debug mode is enabled"; fi
```

In this example:
 
- The `APP_ENV` and `DEBUG` variables are defined.
 
- You can access them within the script using `$APP_ENV` and `$DEBUG`.


---


### Summary: 
 
- **Job-level variables**  are defined using the `variables` keyword within a job.
 
- These variables can be used by referencing them in the `script` section with `$`.

- They override variables defined at higher levels (pipeline, project, group).

You: 

Explain usecase of before script

ChatGPT: 

The `before_script` keyword in GitLab CI is used to define a set of commands that will be executed **before**  each job’s `script` section. This is particularly useful when there are common setup tasks that need to be performed across multiple jobs or even all jobs in a pipeline.Here’s a breakdown of how and why you might use `before_script` in a GitLab CI pipeline:

---

Use Cases of `before_script`:1. **Environment Setup**  (Common Across Jobs) 
- **Use Case** : If all your jobs need a common setup, such as installing dependencies, exporting environment variables, or preparing the environment.

- Example: Installing packages or setting up paths before running your actual job.


```yaml
before_script:
  - apt-get update -y
  - apt-get install -y python3-pip
  - export PATH="$PATH:/usr/local/bin"

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Building project"

test_job:
  stage: test
  script:
    - echo "Running tests"
```

In this example:
 
- The `before_script` installs Python and updates the system for **all jobs**  in the pipeline (both `build_job` and `test_job`).
 
- Each job’s `script` will execute after the `before_script` is run.
2. **Installing Dependencies or Tools**  
- **Use Case** : Many jobs require similar dependencies to be installed. You can install those once in the `before_script` to ensure that the environment is ready for each job.

- Example: Installing Node.js or Python dependencies for jobs that involve building and testing a project.


```yaml
before_script:
  - npm install

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - npm run build

test_job:
  stage: test
  script:
    - npm test
```

Here:
 
- `npm install` is run **before**  both the `build_job` and `test_job` to install Node.js dependencies.
3. **Authentication or Login**  
- **Use Case** : Jobs that interact with cloud platforms, private repositories, or other services might need authentication (e.g., logging into Docker or AWS). You can centralize this process in `before_script` rather than repeating it in each job.

- Example: Logging into Docker Hub or AWS CLI before running any jobs that push Docker images or deploy to AWS.


```yaml
before_script:
  - docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"

stages:
  - build
  - deploy

build_job:
  stage: build
  script:
    - docker build -t myapp .

deploy_job:
  stage: deploy
  script:
    - docker push myapp
```

In this example:
 
- The `docker login` command is executed before both `build_job` and `deploy_job`, so they both have authenticated access to Docker Hub.
4. **Setting Global Environment Variables**  
- **Use Case** : If certain environment variables need to be set up globally and used in multiple jobs.

- Example: Exporting paths, setting up versions of languages, or other global configurations.


```yaml
before_script:
  - export APP_ENV=production
  - export VERSION=1.0.0

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Building version $VERSION in $APP_ENV environment"

test_job:
  stage: test
  script:
    - echo "Testing version $VERSION in $APP_ENV environment"
```

In this case:
 
- The `before_script` sets up the environment variables `APP_ENV` and `VERSION` for both `build_job` and `test_job`.
5. **Database Setup**  
- **Use Case** : You can use `before_script` to set up a database or prepare data for integration tests.

- Example: Starting a Docker container for a test database, running migrations, or seeding data.


```yaml
before_script:
  - docker run -d -p 5432:5432 --name db postgres
  - sleep 10 # Wait for DB to be ready
  - psql -h localhost -U postgres -c "CREATE DATABASE test_db;"

stages:
  - test

test_job:
  stage: test
  script:
    - echo "Running tests against test_db"
```

In this example:
 
- The `before_script` starts a PostgreSQL database and creates a test database before the `test_job` runs.
6. **The `before_script` keyword in GitLab CI is used to define a set of commands that will be executed **before**  each job’s `script` section. This is particularly useful when there are common setup tasks that need to be performed across multiple jobs or even all jobs in a pipeline.Here’s a breakdown of how and why you might use `before_script` in a GitLab CI pipeline:

---

Use Cases of `before_script`:1. **Environment Setup**  (Common Across Jobs) 
- **Use Case** : If all your jobs need a common setup, such as installing dependencies, exporting environment variables, or preparing the environment.

- Example: Installing packages or setting up paths before running your actual job.


```yaml
before_script:
  - apt-get update -y
  - apt-get install -y python3-pip
  - export PATH="$PATH:/usr/local/bin"

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Building project"

test_job:
  stage: test
  script:
    - echo "Running tests"
```

In this example:
 
- The `before_script` installs Python and updates the system for **all jobs**  in the pipeline (both `build_job` and `test_job`).
 
- Each job’s `script` will execute after the `before_script` is run.
2. **Installing Dependencies or Tools**  
- **Use Case** : Many jobs require similar dependencies to be installed. You can install those once in the `before_script` to ensure that the environment is ready for each job.

- Example: Installing Node.js or Python dependencies for jobs that involve building and testing a project.


```yaml
before_script:
  - npm install

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - npm run build

test_job:
  stage: test
  script:
    - npm test
```

Here:
 
- `npm install` is run **before**  both the `build_job` and `test_job` to install Node.js dependencies.
3. **Authentication or Login**  
- **Use Case** : Jobs that interact with cloud platforms, private repositories, or other services might need authentication (e.g., logging into Docker or AWS). You can centralize this process in `before_script` rather than repeating it in each job.

- Example: Logging into Docker Hub or AWS CLI before running any jobs that push Docker images or deploy to AWS.


```yaml
before_script:
  - docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"

stages:
  - build
  - deploy

build_job:
  stage: build
  script:
    - docker build -t myapp .

deploy_job:
  stage: deploy
  script:
    - docker push myapp
```

In this example:
 
- The `docker login` command is executed before both `build_job` and `deploy_job`, so they both have authenticated access to Docker Hub.
4. **Setting Global Environment Variables**  
- **Use Case** : If certain environment variables need to be set up globally and used in multiple jobs.

- Example: Exporting paths, setting up versions of languages, or other global configurations.


```yaml
before_script:
  - export APP_ENV=production
  - export VERSION=1.0.0

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Building version $VERSION in $APP_ENV environment"

test_job:
  stage: test
  script:
    - echo "Testing version $VERSION in $APP_ENV environment"
```

In this case:
 
- The `before_script` sets up the environment variables `APP_ENV` and `VERSION` for both `build_job` and `test_job`.
5. **Database Setup**  
- **Use Case** : You can use `before_script` to set up a database or prepare data for integration tests.

- Example: Starting a Docker container for a test database, running migrations, or seeding data.


```yaml
before_script:
  - docker run -d -p 5432:5432 --name db postgres
  - sleep 10 # Wait for DB to be ready
  - psql -h localhost -U postgres -c "CREATE DATABASE test_db;"

stages:
  - test

test_job:
  stage: test
  script:
    - echo "Running tests against test_db"
```

In this example:
 
- The `before_script` starts a PostgreSQL database and creates a test database before the `test_job` runs.
6. Customizing the `before_script` Per Job**  
- **Use Case** : You may want different jobs to have different `before_script` commands while some share common commands. You can override the global `before_script` in individual jobs.
 
- Example: Defining a global `before_script` and overriding it in specific jobs.


```yaml
before_script:
  - echo "This is a global before_script"

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Building project"

test_job:
  stage: test
  before_script:   # Custom before_script for this job
    - echo "This is a custom before_script for testing"
  script:
    - echo "Running tests"
```

In this example:
 
- The `build_job` uses the global `before_script`, while `test_job` has its own customized `before_script`.


---


### Key Points to Remember: 
 
- **Global `before_script`** : Defined at the top level and executed before every job.
 
- **Job-specific `before_script`** : You can define a different `before_script` for a specific job, which will override the global one for that job.
 
- **Avoid Duplication** : Use `before_script` to avoid duplicating setup tasks (e.g., installing dependencies, logging into services) in every job.
 
- **Runs Before `script`** : `before_script` commands always run **before**  the commands listed in the `script` section of the job.
Using `before_script` effectively can simplify your GitLab CI pipelines, reduce repetition, and ensure consistency across different jobs in the pipeline.

You: 

What is include in pipeline

ChatGPT: 

In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

Types of `include` Sources** 
GitLab supports multiple sources from which you can include files:
 
1. **Local File** 
  - Include a file from the current repository.
 
2. **Project File** 
  - Include a file from another GitLab project.
 
3. **Remote URL** 
  - Include a file from a remote location, such as a public URL.
 
4. **Template** 
  - Use a predefined template provided by GitLab.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

Types of `include` Sources** 
GitLab supports multiple sources from which you can include files:
 
1. **Local File** 
  - Include a file from the current repository.
 
2. **Project File** 
  - Include a file from another GitLab project.
 
3. **Remote URL** 
  - Include a file from a remote location, such as a public URL.
 
4. **Template** 
  - Use a predefined template provided by GitLab.


---

Syntax for `include`** You can use the `include` keyword in `.gitlab-ci.yml` to specify one or more configuration files. Here's how each type works:1. **Include a Local File** 
This includes a file from the same repository.


```yaml
include:
  - local: 'path/to/another-config.yml'
```
 
- In this case, the file `path/to/another-config.yml` will be included in the main pipeline.

- Useful when breaking down complex pipelines into smaller configuration files.
2. **Include a File from Another Project** 
This includes a file from a different project within the same GitLab instance.


```yaml
include:
  - project: 'namespace/project-name'
    file: '/path/to/shared-config.yml'
```

- This is useful for sharing pipeline configurations between projects in the same GitLab instance.
3. **Include from a Remote URL** 
This includes a file from an external URL, such as a public GitHub repository or any other web resource.


```yaml
include:
  - remote: 'https://example.com/path/to/external-config.yml'
```

- The pipeline will load the YAML configuration from the URL provided.

- Useful for sharing pipelines across multiple instances or external systems.
4. **Include a Template** 
GitLab provides predefined templates for common tasks like Docker builds, security scans, etc. You can include these templates directly.


```yaml
include:
  - template: 'Auto-DevOps.gitlab-ci.yml'
```
 
- GitLab has a list of available [CI templates](https://gitlab.com/gitlab-org/gitlab/-/tree/master/lib/gitlab/ci/templates) .


---

**Multiple Includes** You can include multiple configuration files by listing them in the `include` section. Each file will be processed in the order it's listed.

```yaml
include:
  - local: 'ci/build-config.yml'
  - local: 'ci/test-config.yml'
  - project: 'namespace/another-project'
    file: '/ci/shared-config.yml'
  - remote: 'https://example.com/external-config.yml'
```

In this example:

- The pipeline includes multiple local, project, and remote files.

- All files are merged into the main pipeline configuration.


---

**Override Behavior** When using `include`, the included configuration files are merged with your main `.gitlab-ci.yml` file. If there are conflicting settings, the configuration in the **main**  file (i.e., `.gitlab-ci.yml`) takes precedence and can override the included configuration.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

Types of `include` Sources** 
GitLab supports multiple sources from which you can include files:
 
1. **Local File** 
  - Include a file from the current repository.
 
2. **Project File** 
  - Include a file from another GitLab project.
 
3. **Remote URL** 
  - Include a file from a remote location, such as a public URL.
 
4. **Template** 
  - Use a predefined template provided by GitLab.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

Types of `include` Sources** 
GitLab supports multiple sources from which you can include files:
 
1. **Local File** 
  - Include a file from the current repository.
 
2. **Project File** 
  - Include a file from another GitLab project.
 
3. **Remote URL** 
  - Include a file from a remote location, such as a public URL.
 
4. **Template** 
  - Use a predefined template provided by GitLab.


---

Syntax for `include`** You can use the `include` keyword in `.gitlab-ci.yml` to specify one or more configuration files. Here's how each type works:1. **Include a Local File** 
This includes a file from the same repository.


```yaml
include:
  - local: 'path/to/another-config.yml'
```
 
- In this case, the file `path/to/another-config.yml` will be included in the main pipeline.

- Useful when breaking down complex pipelines into smaller configuration files.
2. **Include a File from Another Project** 
This includes a file from a different project within the same GitLab instance.


```yaml
include:
  - project: 'namespace/project-name'
    file: '/path/to/shared-config.yml'
```

- This is useful for sharing pipeline configurations between projects in the same GitLab instance.
3. **Include from a Remote URL** 
This includes a file from an external URL, such as a public GitHub repository or any other web resource.


```yaml
include:
  - remote: 'https://example.com/path/to/external-config.yml'
```

- The pipeline will load the YAML configuration from the URL provided.

- Useful for sharing pipelines across multiple instances or external systems.
4. **Include a Template** 
GitLab provides predefined templates for common tasks like Docker builds, security scans, etc. You can include these templates directly.


```yaml
include:
  - template: 'Auto-DevOps.gitlab-ci.yml'
```
 
- GitLab has a list of available [CI templates](https://gitlab.com/gitlab-org/gitlab/-/tree/master/lib/gitlab/ci/templates) .


---

**Multiple Includes** You can include multiple configuration files by listing them in the `include` section. Each file will be processed in the order it's listed.

```yaml
include:
  - local: 'ci/build-config.yml'
  - local: 'ci/test-config.yml'
  - project: 'namespace/another-project'
    file: '/ci/shared-config.yml'
  - remote: 'https://example.com/external-config.yml'
```

In this example:

- The pipeline includes multiple local, project, and remote files.

- All files are merged into the main pipeline configuration.


---

**Override Behavior** When using `include`, the included configuration files are merged with your main `.gitlab-ci.yml` file. If there are conflicting settings, the configuration in the **main**  file (i.e., `.gitlab-ci.yml`) takes precedence and can override the included configuration.

---

Use Cases for `include`**  
1. **Splitting Large Pipelines** : 
  - If your `.gitlab-ci.yml` file is large or has different jobs for multiple environments (build, test, deploy), you can split them into multiple files and include them.


```yaml
include:
  - local: 'ci/build.yml'
  - local: 'ci/test.yml'
  - local: 'ci/deploy.yml'
```
 
2. **Shared Configuration Across Projects** :
  - If multiple projects need the same pipeline configuration (e.g., a standard CI flow), you can host a shared configuration in a central repository and include it in each project.


```yaml
include:
  - project: 'namespace/common-ci'
    file: '/ci/common-config.yml'
```
 
3. **Including Templates** :
  - Use GitLab's pre-defined templates for things like Auto DevOps or specific languages (e.g., Python, Ruby, etc.).


```yaml
include:
  - template: 'Python.gitlab-ci.yml'
```
 
4. **Use of External CI/CD Files** :
  - If your pipeline needs configurations from a third-party service or an external system, you can include the configurations from a remote URL.


```yaml
include:
  - remote: 'https://thirdparty-service.com/ci-config.yml'
```


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

Types of `include` Sources** 
GitLab supports multiple sources from which you can include files:
 
1. **Local File** 
  - Include a file from the current repository.
 
2. **Project File** 
  - Include a file from another GitLab project.
 
3. **Remote URL** 
  - Include a file from a remote location, such as a public URL.
 
4. **Template** 
  - Use a predefined template provided by GitLab.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

Types of `include` Sources** 
GitLab supports multiple sources from which you can include files:
 
1. **Local File** 
  - Include a file from the current repository.
 
2. **Project File** 
  - Include a file from another GitLab project.
 
3. **Remote URL** 
  - Include a file from a remote location, such as a public URL.
 
4. **Template** 
  - Use a predefined template provided by GitLab.


---

Syntax for `include`** You can use the `include` keyword in `.gitlab-ci.yml` to specify one or more configuration files. Here's how each type works:1. **Include a Local File** 
This includes a file from the same repository.


```yaml
include:
  - local: 'path/to/another-config.yml'
```
 
- In this case, the file `path/to/another-config.yml` will be included in the main pipeline.

- Useful when breaking down complex pipelines into smaller configuration files.
2. **Include a File from Another Project** 
This includes a file from a different project within the same GitLab instance.


```yaml
include:
  - project: 'namespace/project-name'
    file: '/path/to/shared-config.yml'
```

- This is useful for sharing pipeline configurations between projects in the same GitLab instance.
3. **Include from a Remote URL** 
This includes a file from an external URL, such as a public GitHub repository or any other web resource.


```yaml
include:
  - remote: 'https://example.com/path/to/external-config.yml'
```

- The pipeline will load the YAML configuration from the URL provided.

- Useful for sharing pipelines across multiple instances or external systems.
4. **Include a Template** 
GitLab provides predefined templates for common tasks like Docker builds, security scans, etc. You can include these templates directly.


```yaml
include:
  - template: 'Auto-DevOps.gitlab-ci.yml'
```
 
- GitLab has a list of available [CI templates](https://gitlab.com/gitlab-org/gitlab/-/tree/master/lib/gitlab/ci/templates) .


---

**Multiple Includes** You can include multiple configuration files by listing them in the `include` section. Each file will be processed in the order it's listed.

```yaml
include:
  - local: 'ci/build-config.yml'
  - local: 'ci/test-config.yml'
  - project: 'namespace/another-project'
    file: '/ci/shared-config.yml'
  - remote: 'https://example.com/external-config.yml'
```

In this example:

- The pipeline includes multiple local, project, and remote files.

- All files are merged into the main pipeline configuration.


---

**Override Behavior** When using `include`, the included configuration files are merged with your main `.gitlab-ci.yml` file. If there are conflicting settings, the configuration in the **main**  file (i.e., `.gitlab-ci.yml`) takes precedence and can override the included configuration.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

Types of `include` Sources** 
GitLab supports multiple sources from which you can include files:
 
1. **Local File** 
  - Include a file from the current repository.
 
2. **Project File** 
  - Include a file from another GitLab project.
 
3. **Remote URL** 
  - Include a file from a remote location, such as a public URL.
 
4. **Template** 
  - Use a predefined template provided by GitLab.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

**In GitLab CI, the `include` keyword allows you to import or reference other CI configuration files into your pipeline. This is useful for modularizing and reusing pipeline configurations, making them more maintainable and organized.Here’s a detailed explanation of what `include` is and its use cases:

---

What is `include` in GitLab CI?** The `include` keyword allows you to import external YAML configuration files into your `.gitlab-ci.yml` pipeline file. This helps you split your pipeline configuration into multiple files, share common pipeline templates across projects, or reuse common configurations from remote sources.

---

Why Use `include`?**  
1. **Modularity** : You can break down large pipeline configurations into smaller, manageable files.
 
2. **Reuse** : Common pipelines, templates, or shared job configurations can be reused across multiple projects.
 
3. **Centralized Management** : When using shared templates, changes in one place propagate to all pipelines using that template.


---

Types of `include` Sources** 
GitLab supports multiple sources from which you can include files:
 
1. **Local File** 
  - Include a file from the current repository.
 
2. **Project File** 
  - Include a file from another GitLab project.
 
3. **Remote URL** 
  - Include a file from a remote location, such as a public URL.
 
4. **Template** 
  - Use a predefined template provided by GitLab.


---

Syntax for `include`** You can use the `include` keyword in `.gitlab-ci.yml` to specify one or more configuration files. Here's how each type works:1. **Include a Local File** 
This includes a file from the same repository.


```yaml
include:
  - local: 'path/to/another-config.yml'
```
 
- In this case, the file `path/to/another-config.yml` will be included in the main pipeline.

- Useful when breaking down complex pipelines into smaller configuration files.
2. **Include a File from Another Project** 
This includes a file from a different project within the same GitLab instance.


```yaml
include:
  - project: 'namespace/project-name'
    file: '/path/to/shared-config.yml'
```

- This is useful for sharing pipeline configurations between projects in the same GitLab instance.
3. **Include from a Remote URL** 
This includes a file from an external URL, such as a public GitHub repository or any other web resource.


```yaml
include:
  - remote: 'https://example.com/path/to/external-config.yml'
```

- The pipeline will load the YAML configuration from the URL provided.

- Useful for sharing pipelines across multiple instances or external systems.
4. **Include a Template** 
GitLab provides predefined templates for common tasks like Docker builds, security scans, etc. You can include these templates directly.


```yaml
include:
  - template: 'Auto-DevOps.gitlab-ci.yml'
```
 
- GitLab has a list of available [CI templates](https://gitlab.com/gitlab-org/gitlab/-/tree/master/lib/gitlab/ci/templates) .


---

**Multiple Includes** You can include multiple configuration files by listing them in the `include` section. Each file will be processed in the order it's listed.

```yaml
include:
  - local: 'ci/build-config.yml'
  - local: 'ci/test-config.yml'
  - project: 'namespace/another-project'
    file: '/ci/shared-config.yml'
  - remote: 'https://example.com/external-config.yml'
```

In this example:

- The pipeline includes multiple local, project, and remote files.

- All files are merged into the main pipeline configuration.


---

**Override Behavior** When using `include`, the included configuration files are merged with your main `.gitlab-ci.yml` file. If there are conflicting settings, the configuration in the **main**  file (i.e., `.gitlab-ci.yml`) takes precedence and can override the included configuration.

---

Use Cases for `include`**  
1. **Splitting Large Pipelines** : 
  - If your `.gitlab-ci.yml` file is large or has different jobs for multiple environments (build, test, deploy), you can split them into multiple files and include them.


```yaml
include:
  - local: 'ci/build.yml'
  - local: 'ci/test.yml'
  - local: 'ci/deploy.yml'
```
 
2. **Shared Configuration Across Projects** :
  - If multiple projects need the same pipeline configuration (e.g., a standard CI flow), you can host a shared configuration in a central repository and include it in each project.


```yaml
include:
  - project: 'namespace/common-ci'
    file: '/ci/common-config.yml'
```
 
3. **Including Templates** :
  - Use GitLab's pre-defined templates for things like Auto DevOps or specific languages (e.g., Python, Ruby, etc.).


```yaml
include:
  - template: 'Python.gitlab-ci.yml'
```
 
4. **Use of External CI/CD Files** :
  - If your pipeline needs configurations from a third-party service or an external system, you can include the configurations from a remote URL.


```yaml
include:
  - remote: 'https://thirdparty-service.com/ci-config.yml'
```


---

Example: Full Pipeline with `include`** Here’s an example of using `include` to organize different pipeline jobs for build, test, and deploy:

```yaml
# .gitlab-ci.yml
include:
  - local: 'ci/build-config.yml'
  - local: 'ci/test-config.yml'
  - local: 'ci/deploy-config.yml'

stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Running build job"
```
 
- The `.gitlab-ci.yml` file includes three external files (`ci/build-config.yml`, `ci/test-config.yml`, and `ci/deploy-config.yml`).
 
- You can now manage your build, test, and deploy pipelines in separate files while keeping the main `.gitlab-ci.yml` clean.


---

**Summary**  
- The `include` keyword allows you to import other YAML files into your pipeline configuration.

- It supports local files, files from other projects, remote URLs, and GitLab templates.

- This feature is useful for modularizing, reusing, and maintaining pipelines across multiple projects, keeping configurations organized and efficient.

You: 

What is image in stage

ChatGPT: 

In GitLab CI, the `image` keyword is used to define the **Docker image**  that will be used to run the pipeline jobs. Each job in a pipeline is executed in its own isolated environment, and the `image` specifies the Docker container to be used for that environment.When you define an `image` in a stage, it determines the environment (i.e., the operating system, language runtime, tools, and dependencies) that will be available for the jobs within that stage.

---

**In GitLab CI, the `image` keyword is used to define the **Docker image**  that will be used to run the pipeline jobs. Each job in a pipeline is executed in its own isolated environment, and the `image` specifies the Docker container to be used for that environment.When you define an `image` in a stage, it determines the environment (i.e., the operating system, language runtime, tools, and dependencies) that will be available for the jobs within that stage.

---

How to Use `image` in GitLab CI** You can define an `image` globally for the entire pipeline or on a per-job or per-stage basis. The `image` keyword tells GitLab Runner to use a specific Docker image for executing the job.
### Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  image: node:16
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  image: python:3.9
  script:
    - pip install -r requirements.txt
    - pytest
```

In this example:
 
- **`build_job`**  uses the `node:16` Docker image, which provides Node.js version 16 to run JavaScript/Node.js commands.
 
- **`test_job`**  uses the `python:3.9` Docker image, which provides Python 3.9 to execute Python commands.


---

**Image in the Context of Stages** While the `image` is typically defined at the job level, you can also think of the `image` in terms of stages by assigning it to all jobs within a stage, either by: 
- Setting the `image` globally.
 
- Defining the `image` per job, where each stage/job can use a different environment.
1. **Global Image Definition** You can define an image at the top level, which applies to **all jobs**  unless overridden at the job level.

```yaml
image: node:16

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  script:
    - echo "Running tests"
```

In this example:
 
- The global `image: node:16` is used for **both**  `build_job` and `test_job`.

- This ensures all jobs run in an environment with Node.js version 16 installed.
2. **Different Image Per Stage (Job-level Image)** 
You can use different Docker images for different jobs (or stages) depending on the environment required for those jobs.


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  image: node:16
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  image: python:3.9
  script:
    - pip install -r requirements.txt
    - pytest
```
 
- In this case, the `build_job` runs in a **Node.js**  environment, and the `test_job` runs in a **Python**  environment.
 
- Each job uses the `image` keyword to specify the Docker image with the necessary tools and dependencies for that job.


---

**In GitLab CI, the `image` keyword is used to define the **Docker image**  that will be used to run the pipeline jobs. Each job in a pipeline is executed in its own isolated environment, and the `image` specifies the Docker container to be used for that environment.When you define an `image` in a stage, it determines the environment (i.e., the operating system, language runtime, tools, and dependencies) that will be available for the jobs within that stage.

---

**In GitLab CI, the `image` keyword is used to define the **Docker image**  that will be used to run the pipeline jobs. Each job in a pipeline is executed in its own isolated environment, and the `image` specifies the Docker container to be used for that environment.When you define an `image` in a stage, it determines the environment (i.e., the operating system, language runtime, tools, and dependencies) that will be available for the jobs within that stage.

---

How to Use `image` in GitLab CI** You can define an `image` globally for the entire pipeline or on a per-job or per-stage basis. The `image` keyword tells GitLab Runner to use a specific Docker image for executing the job.
### Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  image: node:16
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  image: python:3.9
  script:
    - pip install -r requirements.txt
    - pytest
```

In this example:
 
- **`build_job`**  uses the `node:16` Docker image, which provides Node.js version 16 to run JavaScript/Node.js commands.
 
- **`test_job`**  uses the `python:3.9` Docker image, which provides Python 3.9 to execute Python commands.


---

**Image in the Context of Stages** While the `image` is typically defined at the job level, you can also think of the `image` in terms of stages by assigning it to all jobs within a stage, either by: 
- Setting the `image` globally.
 
- Defining the `image` per job, where each stage/job can use a different environment.
1. **Global Image Definition** You can define an image at the top level, which applies to **all jobs**  unless overridden at the job level.

```yaml
image: node:16

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  script:
    - echo "Running tests"
```

In this example:
 
- The global `image: node:16` is used for **both**  `build_job` and `test_job`.

- This ensures all jobs run in an environment with Node.js version 16 installed.
2. **Different Image Per Stage (Job-level Image)** 
You can use different Docker images for different jobs (or stages) depending on the environment required for those jobs.


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  image: node:16
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  image: python:3.9
  script:
    - pip install -r requirements.txt
    - pytest
```
 
- In this case, the `build_job` runs in a **Node.js**  environment, and the `test_job` runs in a **Python**  environment.
 
- Each job uses the `image` keyword to specify the Docker image with the necessary tools and dependencies for that job.


---

Why Use `image` in GitLab CI?**  
1. **Isolated Environments** : Each job runs in its own isolated container, meaning no interference between jobs, ensuring consistency and reliability.
 
2. **Customizable Environments** : By specifying a different Docker image for each job or stage, you can tailor the environment to suit the needs of that specific job.
 
3. **Reproducibility** : Using predefined Docker images ensures that the environment is consistent across different runs, making builds and tests reproducible.
 
4. **Flexibility** : You can use any publicly available Docker image (from Docker Hub or any other registry) or your own custom image that has the tools and dependencies needed for your job.


---

**How to Use Custom Docker Images** 
If the available public Docker images don’t meet your needs, you can create your own custom Docker images and use them in your pipeline.

### Example of using a custom image: 


```yaml
image: registry.gitlab.com/my-namespace/my-custom-image:latest

stages:
  - build

build_job:
  stage: build
  script:
    - ./build_my_project.sh
```

Here:
 
- The `image` is a custom image stored in the GitLab container registry (`registry.gitlab.com/my-namespace/my-custom-image:latest`).

- The custom image may include specific tools, libraries, or configurations necessary for your project.


---

**In GitLab CI, the `image` keyword is used to define the **Docker image**  that will be used to run the pipeline jobs. Each job in a pipeline is executed in its own isolated environment, and the `image` specifies the Docker container to be used for that environment.When you define an `image` in a stage, it determines the environment (i.e., the operating system, language runtime, tools, and dependencies) that will be available for the jobs within that stage.

---

**In GitLab CI, the `image` keyword is used to define the **Docker image**  that will be used to run the pipeline jobs. Each job in a pipeline is executed in its own isolated environment, and the `image` specifies the Docker container to be used for that environment.When you define an `image` in a stage, it determines the environment (i.e., the operating system, language runtime, tools, and dependencies) that will be available for the jobs within that stage.

---

How to Use `image` in GitLab CI** You can define an `image` globally for the entire pipeline or on a per-job or per-stage basis. The `image` keyword tells GitLab Runner to use a specific Docker image for executing the job.
### Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  image: node:16
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  image: python:3.9
  script:
    - pip install -r requirements.txt
    - pytest
```

In this example:
 
- **`build_job`**  uses the `node:16` Docker image, which provides Node.js version 16 to run JavaScript/Node.js commands.
 
- **`test_job`**  uses the `python:3.9` Docker image, which provides Python 3.9 to execute Python commands.


---

**Image in the Context of Stages** While the `image` is typically defined at the job level, you can also think of the `image` in terms of stages by assigning it to all jobs within a stage, either by: 
- Setting the `image` globally.
 
- Defining the `image` per job, where each stage/job can use a different environment.
1. **Global Image Definition** You can define an image at the top level, which applies to **all jobs**  unless overridden at the job level.

```yaml
image: node:16

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  script:
    - echo "Running tests"
```

In this example:
 
- The global `image: node:16` is used for **both**  `build_job` and `test_job`.

- This ensures all jobs run in an environment with Node.js version 16 installed.
2. **Different Image Per Stage (Job-level Image)** 
You can use different Docker images for different jobs (or stages) depending on the environment required for those jobs.


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  image: node:16
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  image: python:3.9
  script:
    - pip install -r requirements.txt
    - pytest
```
 
- In this case, the `build_job` runs in a **Node.js**  environment, and the `test_job` runs in a **Python**  environment.
 
- Each job uses the `image` keyword to specify the Docker image with the necessary tools and dependencies for that job.


---

**In GitLab CI, the `image` keyword is used to define the **Docker image**  that will be used to run the pipeline jobs. Each job in a pipeline is executed in its own isolated environment, and the `image` specifies the Docker container to be used for that environment.When you define an `image` in a stage, it determines the environment (i.e., the operating system, language runtime, tools, and dependencies) that will be available for the jobs within that stage.

---

**In GitLab CI, the `image` keyword is used to define the **Docker image**  that will be used to run the pipeline jobs. Each job in a pipeline is executed in its own isolated environment, and the `image` specifies the Docker container to be used for that environment.When you define an `image` in a stage, it determines the environment (i.e., the operating system, language runtime, tools, and dependencies) that will be available for the jobs within that stage.

---

How to Use `image` in GitLab CI** You can define an `image` globally for the entire pipeline or on a per-job or per-stage basis. The `image` keyword tells GitLab Runner to use a specific Docker image for executing the job.
### Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  image: node:16
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  image: python:3.9
  script:
    - pip install -r requirements.txt
    - pytest
```

In this example:
 
- **`build_job`**  uses the `node:16` Docker image, which provides Node.js version 16 to run JavaScript/Node.js commands.
 
- **`test_job`**  uses the `python:3.9` Docker image, which provides Python 3.9 to execute Python commands.


---

**Image in the Context of Stages** While the `image` is typically defined at the job level, you can also think of the `image` in terms of stages by assigning it to all jobs within a stage, either by: 
- Setting the `image` globally.
 
- Defining the `image` per job, where each stage/job can use a different environment.
1. **Global Image Definition** You can define an image at the top level, which applies to **all jobs**  unless overridden at the job level.

```yaml
image: node:16

stages:
  - build
  - test

build_job:
  stage: build
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  script:
    - echo "Running tests"
```

In this example:
 
- The global `image: node:16` is used for **both**  `build_job` and `test_job`.

- This ensures all jobs run in an environment with Node.js version 16 installed.
2. **Different Image Per Stage (Job-level Image)** 
You can use different Docker images for different jobs (or stages) depending on the environment required for those jobs.


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  image: node:16
  script:
    - npm install
    - npm run build

test_job:
  stage: test
  image: python:3.9
  script:
    - pip install -r requirements.txt
    - pytest
```
 
- In this case, the `build_job` runs in a **Node.js**  environment, and the `test_job` runs in a **Python**  environment.
 
- Each job uses the `image` keyword to specify the Docker image with the necessary tools and dependencies for that job.


---

Why Use `image` in GitLab CI?**  
1. **Isolated Environments** : Each job runs in its own isolated container, meaning no interference between jobs, ensuring consistency and reliability.
 
2. **Customizable Environments** : By specifying a different Docker image for each job or stage, you can tailor the environment to suit the needs of that specific job.
 
3. **Reproducibility** : Using predefined Docker images ensures that the environment is consistent across different runs, making builds and tests reproducible.
 
4. **Flexibility** : You can use any publicly available Docker image (from Docker Hub or any other registry) or your own custom image that has the tools and dependencies needed for your job.


---

**How to Use Custom Docker Images** 
If the available public Docker images don’t meet your needs, you can create your own custom Docker images and use them in your pipeline.

### Example of using a custom image: 


```yaml
image: registry.gitlab.com/my-namespace/my-custom-image:latest

stages:
  - build

build_job:
  stage: build
  script:
    - ./build_my_project.sh
```

Here:
 
- The `image` is a custom image stored in the GitLab container registry (`registry.gitlab.com/my-namespace/my-custom-image:latest`).

- The custom image may include specific tools, libraries, or configurations necessary for your project.


---

Summary of `image` in Stages**  
- The `image` keyword specifies the Docker image for the environment in which a job will run.

- It can be defined globally for all jobs or per-job/stage, allowing different jobs to use different environments.
 
- Using the `image` keyword ensures consistency and isolation across different stages and jobs, preventing dependencies from interfering with each other.
 
- You can use public images (e.g., `node`, `python`, `alpine`) or custom images to create tailored environments for your pipelines.

You: 

explain me this 

Runners
Understanding GitLab Runners, which are used to execute your pipeline jobs.
Difference between shared runners and specific runners.
Learn how jobs are picked up by runners and executed.

ChatGPT: 

**Runners in GitLab CI** A **GitLab Runner**  is an application used to execute your pipeline jobs. It's an essential part of the GitLab CI/CD system that processes jobs in your `.gitlab-ci.yml` file. When you push code or manually trigger a pipeline, GitLab schedules jobs, and runners pick them up to run them in a specific environment.

---

**How GitLab Runners Work**  
1. **Runner Setup** : A GitLab Runner can be installed on any machine—virtual or physical—and can run jobs either in a containerized environment (using Docker, Kubernetes) or directly on the host system.
 
2. **Job Execution** : The runner picks up a job from the GitLab CI/CD pipeline queue, sets up the environment (based on the defined `image` or configuration), and runs the job scripts (defined under the `script` section of `.gitlab-ci.yml`).
 
3. **Job Result** : After executing the job, the runner reports the results (success/failure, logs, artifacts) back to GitLab, which updates the pipeline status accordingly.


---

**Types of GitLab Runners** 
There are two main types of GitLab Runners:
 
1. **Shared Runners**
 
2. **Specific Runners**


---

**1. Shared Runners** **Shared Runners**  are runners that are available to multiple projects or users within a GitLab instance. These are typically provided by GitLab itself (on GitLab.com) or by the organization hosting the GitLab instance.
#### Characteristics of Shared Runners: 
 
- **Shared Across Multiple Projects** : Shared runners can execute jobs for any project within a GitLab instance.
 
- **Managed by Admins** : These runners are typically maintained and configured by GitLab admins or the platform (e.g., GitLab.com).
 
- **Pre-configured Environments** : Shared runners may use default Docker images, have limited customization options, or provide general-purpose environments.
 
- **Scalability** : Shared runners are typically scalable, allowing many jobs to run concurrently across projects.
 
- **Good for General Use** : Ideal for small to medium projects where custom environments or specific setups are not required.

#### Example Usage: 
When you use GitLab.com’s free plan, you are likely using **shared runners**  that GitLab provides for all users.

```yaml
image: ruby:2.6

build_job:
  stage: build
  script:
    - bundle install
```

In this example, the build job will be picked up by a shared runner that GitLab manages and executes in a Ruby 2.6 environment.


---

**2. Specific Runners (Group or Project Runners)** **Specific Runners**  are runners that are dedicated to a particular project or a group of projects. They are typically installed and managed by the organization or individual project administrators.
#### Characteristics of Specific Runners: 
 
- **Tied to Specific Projects** : Specific runners only execute jobs for the projects they are assigned to.
 
- **Customizable** : You can configure specific runners with custom Docker images, system dependencies, and settings required for your project.
 
- **Private and Secure** : Because they are specific to a project or a group, you can configure them to meet security, performance, or compliance requirements.
 
- **Better Control** : They provide more control over the environment and dependencies of the job, such as custom build tools, OS configurations, etc.

#### Example Usage: 
If your project requires a specific build environment that GitLab’s shared runners can’t provide, you might set up a **specific runner**  that runs on a machine you control.

```yaml
image: custom/build-image:latest

build_job:
  stage: build
  script:
    - ./build_project.sh
```

Here, the specific runner could be running on a machine that has access to proprietary software, additional hardware, or other configurations that shared runners don’t offer.


---

**Key Differences Between Shared and Specific Runners** | Feature | Shared Runners | Specific Runners | 
| --- | --- | --- | 
| Scope | Available to all projects in the instance | Dedicated to specific projects or groups | 
| Management | Managed by GitLab admins (or GitLab.com) | Managed by the project or organization | 
| Customization | Limited customization options | Highly customizable (environments, tools, etc.) | 
| Scalability | Scalable and often auto-scaling | Depends on the resources allocated for the specific runner | 
| Security | General-purpose; shared across projects | Secure and private for specific use cases | 
| Best For | General-purpose projects | Custom environments or complex projects | 


---

**How Jobs Are Picked Up by Runners**  
1. **Job Scheduling** :
  - When a pipeline is triggered (e.g., by a code push, merge request, or manually), GitLab creates a queue of jobs to be executed.
 
  - Jobs are scheduled to run based on the `stages` defined in your `.gitlab-ci.yml` file.
 
2. **Runner Registration** :
  - Each runner registers itself with the GitLab instance, indicating whether it is a shared or specific runner.

  - Runners check GitLab for jobs they are eligible to pick up.
 
3. **Job Matching** : 
  - Runners pick up jobs based on tags, project assignment, or availability: 
    - **Tags** : You can assign tags to jobs in `.gitlab-ci.yml` and configure runners to pick up only jobs with specific tags.
 
    - **Specific Runners** : Specific runners only execute jobs for the projects or groups they are assigned to.
 
    - **Shared Runners** : Shared runners can execute any job across the instance if no specific runner is available.

  - If the job matches the runner's configuration (tags, project assignment), the runner pulls the job from the queue.
 
4. **Execution** : 
  - The runner prepares the environment, which may involve pulling a Docker image (if specified), cloning the repository, and executing the job’s `script`.

  - Once the job is complete, the runner sends the result (pass/fail, logs, etc.) back to GitLab.
 
5. **Concurrency** :
  - Runners can run multiple jobs concurrently, depending on their configuration and available resources.

  - Shared runners are often configured to handle a high number of concurrent jobs, while specific runners may be optimized for particular workloads.


---

**Example of Runner Usage with Tags** In the `.gitlab-ci.yml` file, you can specify tags for jobs. Runners can be configured to only pick up jobs with matching tags.

```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - make build
  tags:
    - docker
    - linux

test_job:
  stage: test
  script:
    - make test
  tags:
    - linux
```
 
- The `build_job` and `test_job` are tagged with `linux`, so they will be picked up by any runner that is configured to handle jobs with that tag.
 
- If a runner is configured with `tags: [docker, linux]`, it will pick up jobs with those tags, ensuring the correct environment is used.


---

**Summary**  
- **GitLab Runners**  execute jobs in GitLab CI pipelines. They can be installed on any machine (physical or virtual) and run in isolation.
 
- **Shared Runners**  are available across multiple projects and are managed by the GitLab instance admins.
 
- **Specific Runners**  are dedicated to individual projects or groups, offering more customization and security.

- Jobs are picked up by runners based on tags, project assignment, and runner availability, allowing for flexible and scalable job execution.

You: 

explain artifacts in job

ChatGPT: 

**Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.2. `expire_in`:** 
By default, artifacts are kept indefinitely, but you can set an expiration time after which GitLab automatically removes them.


```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 week
```

In this case, artifacts will expire after 1 week.
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.2. `expire_in`:** 
By default, artifacts are kept indefinitely, but you can set an expiration time after which GitLab automatically removes them.


```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 week
```

In this case, artifacts will expire after 1 week.
3. `when`:** 
You can control when artifacts are uploaded based on the job result:
 
- `on_success`: Uploads artifacts only if the job succeeds (default behavior).
 
- `on_failure`: Uploads artifacts only if the job fails (e.g., useful for saving logs or debugging information).
 
- `always`: Uploads artifacts regardless of whether the job succeeds or fails.


```yaml
artifacts:
  paths:
    - logs/error.log
  when: on_failure
```
In this case, the `error.log` will only be uploaded if the job fails.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.2. `expire_in`:** 
By default, artifacts are kept indefinitely, but you can set an expiration time after which GitLab automatically removes them.


```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 week
```

In this case, artifacts will expire after 1 week.
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.2. `expire_in`:** 
By default, artifacts are kept indefinitely, but you can set an expiration time after which GitLab automatically removes them.


```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 week
```

In this case, artifacts will expire after 1 week.
3. `when`:** 
You can control when artifacts are uploaded based on the job result:
 
- `on_success`: Uploads artifacts only if the job succeeds (default behavior).
 
- `on_failure`: Uploads artifacts only if the job fails (e.g., useful for saving logs or debugging information).
 
- `always`: Uploads artifacts regardless of whether the job succeeds or fails.


```yaml
artifacts:
  paths:
    - logs/error.log
  when: on_failure
```
In this case, the `error.log` will only be uploaded if the job fails.4. `name`:** 
You can give a custom name to the artifact package (by default, it's named after the job).


```yaml
artifacts:
  name: "my-build-artifacts"
  paths:
    - dist/
```
The artifacts will be saved as `my-build-artifacts.zip`.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.2. `expire_in`:** 
By default, artifacts are kept indefinitely, but you can set an expiration time after which GitLab automatically removes them.


```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 week
```

In this case, artifacts will expire after 1 week.
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.2. `expire_in`:** 
By default, artifacts are kept indefinitely, but you can set an expiration time after which GitLab automatically removes them.


```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 week
```

In this case, artifacts will expire after 1 week.
3. `when`:** 
You can control when artifacts are uploaded based on the job result:
 
- `on_success`: Uploads artifacts only if the job succeeds (default behavior).
 
- `on_failure`: Uploads artifacts only if the job fails (e.g., useful for saving logs or debugging information).
 
- `always`: Uploads artifacts regardless of whether the job succeeds or fails.


```yaml
artifacts:
  paths:
    - logs/error.log
  when: on_failure
```
In this case, the `error.log` will only be uploaded if the job fails.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.2. `expire_in`:** 
By default, artifacts are kept indefinitely, but you can set an expiration time after which GitLab automatically removes them.


```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 week
```

In this case, artifacts will expire after 1 week.
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

****Artifacts in GitLab CI Jobs** **Artifacts**  in GitLab CI/CD are the files or data produced during the execution of a job that you want to preserve and share between different jobs in the same pipeline. They allow you to save the outputs of your jobs (such as binaries, test results, logs, or reports) and make them available for download or for use in later stages of the pipeline.

---

**How Artifacts Work in GitLab CI**  
1. **Job Execution** : When a job runs in the pipeline, it may generate files (e.g., compiled binaries, logs, or reports).
 
2. **Artifacts Declaration** : You can declare these files as **artifacts**  in your `.gitlab-ci.yml` file, specifying which files should be preserved after the job completes.
 
3. **Artifact Availability** : These artifacts are stored by GitLab and can be accessed by:
  - Downloading them from the pipeline/job page in GitLab’s UI.

  - Passing them to subsequent jobs in later stages.


---

**Key Use Cases for Artifacts**  
- **Build Outputs** : When a `build` job generates an executable, you can save that executable as an artifact for a later `test` or `deploy` job to use.
 
- **Test Reports** : If a `test` job generates logs or reports (e.g., code coverage or unit test results), you can save them as artifacts for review.
 
- **Intermediate Files** : You can preserve intermediate files or dependencies that need to be used across multiple jobs or stages within a pipeline.
 
- **Deployment Assets** : Artifacts can be used to pass built assets to the deployment stage.


---

Defining Artifacts in `.gitlab-ci.yml`** You define artifacts in a job by using the `artifacts` keyword within the job configuration. You specify which files or directories should be preserved.
### Basic Example: 


```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling the code"
    - mkdir dist
    - touch dist/my_app
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests on the built app"
    - ls dist/
```

#### In this example: 
 
- **`build_job`** : This job compiles code and produces a file in the `dist/` directory. 
  - The `artifacts` keyword specifies that everything in the `dist/` directory should be preserved as an artifact.
 
- **`test_job`** : This job runs in the `test` stage and relies on the output (`dist/my_app`) from the `build_job`. Since the `dist/` directory was saved as an artifact, the `test_job` has access to the files created in the `build_job`.


---

**Artifact Configuration Options** 
You can fine-tune the behavior of artifacts using various options:
1. `paths`:** 
The most common option, it defines the files or directories you want to save as artifacts.


```yaml
artifacts:
  paths:
    - dist/
    - logs/output.log
```
This example saves the `dist/` directory and the `logs/output.log` file as artifacts.2. `expire_in`:** 
By default, artifacts are kept indefinitely, but you can set an expiration time after which GitLab automatically removes them.


```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 week
```

In this case, artifacts will expire after 1 week.
3. `when`:** 
You can control when artifacts are uploaded based on the job result:
 
- `on_success`: Uploads artifacts only if the job succeeds (default behavior).
 
- `on_failure`: Uploads artifacts only if the job fails (e.g., useful for saving logs or debugging information).
 
- `always`: Uploads artifacts regardless of whether the job succeeds or fails.


```yaml
artifacts:
  paths:
    - logs/error.log
  when: on_failure
```
In this case, the `error.log` will only be uploaded if the job fails.4. `name`:** 
You can give a custom name to the artifact package (by default, it's named after the job).


```yaml
artifacts:
  name: "my-build-artifacts"
  paths:
    - dist/
```
The artifacts will be saved as `my-build-artifacts.zip`.5. `public`:** By default, artifacts are only available to users with access to the pipeline. You can make artifacts public by setting this option to `true`.

---

**Types of Artifacts**  
1. **Regular Artifacts** : These are files generated and stored for later stages or manual download.
 
2. **Reports** : Some artifacts, such as test reports, coverage reports, and security scanning reports, are presented in a specific format in GitLab’s UI for easier access.

### Example of Test Reports as Artifacts: 


```yaml
test_job:
  stage: test
  script:
    - pytest --junitxml=report.xml
  artifacts:
    reports:
      junit: report.xml
```
Here, the test report `report.xml` is saved as an artifact and displayed in the GitLab pipeline’s test results section.

---

**Passing Artifacts Between Jobs** Artifacts can be passed between jobs, but this is typically done across different **stages**  (since jobs within the same stage run in parallel). For instance, if you have a `build` job that generates a compiled binary, you can pass that artifact to a later `deploy` job in the `deploy` stage.

```yaml
stages:
  - build
  - deploy

build_job:
  stage: build
  script:
    - make
  artifacts:
    paths:
      - my_app

deploy_job:
  stage: deploy
  script:
    - ./deploy my_app
```

In this example:
 
- **`build_job`**  produces the `my_app` file and saves it as an artifact.
 
- **`deploy_job`**  runs in the next stage and uses the `my_app` file from the previous stage.


---

**Artifact Storage and Downloading**  
- **Storage** : Artifacts are stored by GitLab, either in the instance’s file system or object storage (e.g., AWS S3).
 
- **Downloading** : You can download artifacts from the GitLab web interface: 
  - Go to a pipeline or job’s details page, and you’ll see a section where artifacts can be downloaded as `.zip` files.


---

**When to Use Artifacts?**  
- **Build Artifacts** : Save the output of your build process (e.g., binaries, compiled code).
 
- **Testing Logs** : Save logs or test reports that might be needed for debugging.
 
- **Intermediate Outputs** : Pass outputs from one stage (like build) to another (like deploy).
 
- **Reports and Metrics** : Save and visualize reports (e.g., JUnit test results, code coverage reports).


---

**Artifacts vs. Caches** GitLab CI also offers **caching** , which is similar to artifacts but is used to store dependencies and files that don’t change often (like libraries, packages, or modules). Here’s how they differ: 
- **Artifacts** : Used to save and pass files between jobs (especially across stages). They are typically short-lived and job-specific.
 
- **Caches** : Used to speed up job execution by reusing downloaded dependencies or build results across multiple pipeline runs. Caches are typically used for speeding up build times rather than for passing data between stages.

### Summary 

- Artifacts allow you to save and transfer files generated during the job execution, such as build outputs, logs, and reports.
 
- You can define artifacts in `.gitlab-ci.yml` using the `artifacts` keyword, specifying the files or directories to be preserved.

- Artifacts can be passed between jobs in different stages and can be customized (e.g., when they are uploaded, how long they persist).

- They are particularly useful for sharing data between jobs and reviewing the results of jobs, such as build outputs or test results.
