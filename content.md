# Continuous Integration / Continuous Deployment (CI/CD) With GitHub Actions

## Introduction
Continuous Integration (CI) and Continuous Deployment (CD) are crucial practices in modern software development, particularly for agile teams. In this lesson, we will guide you through setting up a simple CI/CD pipeline using GitHub Actions for a Ruby on Rails application. This pipeline will primarily focus on running the test suite whenever changes are pushed to the repository.

## Setting Up Your GitHub Repository
Before we begin, ensure your Rails application is hosted on GitHub. If not, you can create a new repository and push your Rails application code to it.

## Creating the GitHub Actions Workflow
GitHub Actions allows you to automate, customize, and execute your software development workflows right in your repository. Here, we'll create a workflow to run the Rails test suite.

1. Create the Workflow File
In your repository, navigate to the `Actions` tab. Click `New workflow`.

![](assets/actions.png)


You can choose a suggested workflow for Ruby on Rails or skip the suggested workflows and click set up a workflow yourself. You'll be presented with an editor to write your workflow file. Name the file something like `rails_ci.yml`.

![](assets/new-workflow.png)

2. Define Workflow Configuration
Insert the following YAML configuration into the `rails_ci.yml` file:

```yaml
# This workflow will install a prebuilt Ruby version, install dependencies, and
# run tests and linters.
name: "Ruby on Rails CI"
on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]
jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:11-alpine
        ports:
          - "5432:5432"
        env:
          POSTGRES_DB: rails_test
          POSTGRES_USER: rails
          POSTGRES_PASSWORD: password
    env:
      RAILS_ENV: test
      DATABASE_URL: "postgres://rails:password@localhost:5432/rails_test"
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      # Add or replace dependency steps here
      - name: Install Ruby and gems
        uses: ruby/setup-ruby@55283cc23133118229fd3f97f9336ee23a179fcf # v1.146.0
        with:
          bundler-cache: true
      # Add or replace database setup steps here
      - name: Set up database schema
        run: bin/rails db:schema:load
      # Add or replace test runners here
      - name: Run tests
        run: bundle exec rails test

  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Install Ruby and gems
        uses: ruby/setup-ruby@55283cc23133118229fd3f97f9336ee23a179fcf # v1.146.0
        with:
          bundler-cache: true
      # Add or replace any other lints here
      # TODO: add later
      # - name: Security audit dependencies
      #   run: bin/bundler-audit --update
      # - name: Security audit application code
      #   run: bin/brakeman -q -w2
      - name: Lint Ruby files
        run: bundle exec rubocop --parallel
```

## Explanation of Key Sections
- **on**: Specifies the events that trigger the workflow. Here, it triggers on pushes and pull requests to the main branch.
- **jobs**: Defines the jobs that will be run. Here, we have one job called build.
- **runs-on**: Indicates the type of machine to run the job on. We use the latest Ubuntu.
- **services**: Sets up a PostgreSQL database, which is required for the Rails test environment.
- **steps**: Lists the steps of the job. This includes checking out the code, setting up Ruby, installing dependencies, setting up the database, and running the tests.

3. Commit and Push the `rails_ci.yml` Workflow File

4. Verify the Workflow
After pushing, go back to the Actions tab in your GitHub repository.
You should see the workflow running. If everything is set up correctly, the workflow will execute and show a success status if your tests pass. Now anytime you create a pull request into the `main` branch it will trigger this workflow.

![](assets/pull-request.png)

## Additional Considerations
- **Caching Dependencies**: To speed up the workflow, you can cache your dependencies using the actions/cache step. This reduces the time spent in the installation step on subsequent runs.
- **Notifications**: You can set up notifications (e.g., email, Slack) to alert you when the CI process fails. This helps keep the team informed about the health of the application.
- **Deployment**: Once your CI setup is stable, consider adding deployment steps to automate the deployment of your application to your hosting service.

## Conclusion
Setting up CI/CD with GitHub Actions empowers your team to catch issues early and deploy confidently. This lesson provided a basic setup, but GitHub Actions is very flexible, allowing you to tailor your workflows to match your development and operational needs perfectly. Always explore further optimizations and tools to integrate into your pipeline for more robust automation.
