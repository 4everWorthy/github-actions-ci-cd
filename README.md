# GitHub Actions CI/CD Workflow for Java App Deployment on AWS Elastic Beanstalk

## Project Overview
This project demonstrates the use of GitHub Actions to implement Continuous Integration (CI) and Continuous Deployment (CD) for a Java application. The application is deployed to AWS Elastic Beanstalk, and the CI/CD workflows are fully automated. 

The workflow is triggered on every push to the `main` branch, ensuring that code changes are tested and deployed automatically. This project uses **Java 17** and **Node.js** to handle the build and deployment tasks.

## Workflows Overview

### CI Workflow
The CI workflow is responsible for automating the build, test, and packaging phases of the application lifecycle.

**Key Steps:**
1. **Checkout Code:** Retrieves the latest version of the code from the `main` branch.
2. **Set up Java:** Ensures that Java 17 is installed on the runner.
3. **Install Dependencies:** Installs the required dependencies using Maven.
4. **Run Tests:** Executes unit tests to validate the code.
5. **Package Build Artifacts:** Packages the application into a `.jar` file.

### CD Workflow
The CD workflow is responsible for deploying the application to AWS Elastic Beanstalk after the successful completion of the CI workflow.

**Key Steps:**
1. **Create a ZIP Artifact:** Compresses the application into a `.zip` file for deployment.
2. **Deploy to AWS Elastic Beanstalk:** Automatically deploys the `.zip` file to the specified AWS Elastic Beanstalk environment using the AWS CLI.

## Workflow Configuration
The GitHub Actions workflow file `.github/workflows/main.yml` contains the configuration for the CI/CD processes.

```yaml
name: CI/CD Workflow

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '16'

      - name: Set execute permission for Maven Wrapper
        run: chmod +x ./mvnw

      - name: Set up Java
        uses: actions/setup-java@v2
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Install dependencies
        run: ./mvnw install

      - name: Run tests
        run: ./mvnw test

      - name: Generate build artifacts
        run: ./mvnw package

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Deploy to AWS Elastic Beanstalk
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: 'us-east-1'
        run: |
          zip -r app.zip .
          aws elasticbeanstalk create-application-version \
            --application-name github-actions-ci-cd \
            --environment-name my-env \
            --version-label v1 \
            --source-bundle S3Bucket=elasticbeanstalk-us-east-1-888577045495,S3Key=app.zip
```

## Configuration & Dependencies

### Tools & Technologies Used:
- **GitHub Actions**: To automate the CI/CD pipeline.
- **Java 17**: As the language of the application.
- **Node.js**: Required for handling certain build steps.
- **Maven**: For dependency management and project packaging.
- **AWS Elastic Beanstalk**: For hosting and deploying the application.
- **AWS CLI**: To interact with AWS services from the GitHub workflow.

### AWS Setup
- **AWS Access Key & Secret Key**: Stored as GitHub Secrets (`AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`) to securely authenticate with AWS.
- **Elastic Beanstalk Environment**: Deployed to `my-env` in the `us-east-1` region.

## How It Works

1. **Push Changes**: Whenever changes are pushed to the `main` branch, the CI workflow kicks off.
2. **Build and Test**: GitHub Actions builds the app, runs tests, and packages the app.
3. **Deploy**: After a successful build, the app is automatically deployed to AWS Elastic Beanstalk.
4. **Logs & Notifications**: View workflow logs in the GitHub Actions tab and monitor deployment status in the AWS Elastic Beanstalk console.

## How to Run Locally
If you want to run the application locally for development purposes:

1. Clone the repository:
    ```bash
    git clone https://github.com/yourusername/your-repo.git
    ```
2. Navigate to the project directory:
    ```bash
    cd your-repo
    ```
3. Build the project:
    ```bash
    ./mvnw package
    ```
4. Run the application:
    ```bash
    java -jar target/app.jar
    ```

## Screenshots & Example Output

### GitHub Actions Workflow Run
![Workflow Success](C:\Users\kjaso\OneDrive\Desktop\CTACT\JAVA_FS103\github-actions-ci-cd\screenshots\AWS Environment overview.png)

### AWS Elastic Beanstalk Application
![AWS Elastic Beanstalk](C:\Users\kjaso\OneDrive\Desktop\CTACT\JAVA_FS103\github-actions-ci-cd\screenshots\GitHub Actions Workflow.png)

## Troubleshooting
If you encounter any issues with deployment, check the following:

- Ensure your AWS Access Key and Secret Key are correctly stored in GitHub Secrets.
- Verify that the Elastic Beanstalk environment name and S3 bucket details are correct in the `main.yml` file.
- Check the logs in the GitHub Actions tab for any specific errors.

## Conclusion
This project demonstrates how to automate the CI/CD process using GitHub Actions and AWS Elastic Beanstalk. By automating these tasks, developers can ensure that code changes are rigorously tested and deployed seamlessly, streamlining the development lifecycle.

```
