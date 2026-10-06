# Java Web App Deployment with AWS CI/CD

Welcome! This repository contains a Java web app built as part of NextWork's 6 Day DevOps Challenge. Every push to the `master` branch is automatically built and deployed to production on AWS.

## Table of Contents
- [Introduction](#introduction)
- [Architecture](#architecture)
- [Technologies](#technologies)
- [Project Structure](#project-structure)
- [How the Pipeline Works](#how-the-pipeline-works)
- [Setup](#setup)
- [Deployment](#deployment)
- [Contact](#contact)
- [Acknowledgments](#acknowledgments)

## Introduction
This project shows how to build and deploy a Java web app on AWS with a fully automated CI/CD pipeline. A single `git push` triggers the whole release process: fetching the source code, building the app with secure dependencies, and deploying it to a production server, with automatic rollback if the deployment fails.

## Architecture

<img width="1474" height="476" alt="architecture-complete" src="https://github.com/user-attachments/assets/32d06a3a-49fc-4650-a062-9dcd1453968c" />


## Technologies
- **Amazon EC2**: one instance for development, one for production.
- **VS Code (Remote - SSH)**: used to edit code directly on the development instance.
- **Git & GitHub**: code is stored and versioned in this repository.
- **AWS CodeArtifact**: stores the project's dependencies in a private repository, with Maven Central as upstream. This keeps package versions consistent, secure and available even if Maven Central goes down.
- **AWS IAM**: roles give each service only the permissions it needs, without storing any credentials on servers.
- **AWS CodeBuild**: compiles and packages the app into a WAR file, following `buildspec.yml`.
- **Amazon S3**: stores the build artifacts.
- **AWS CloudFormation**: creates the production infrastructure (VPC, subnet, security group, EC2 instance) as code.
- **AWS CodeDeploy**: installs Tomcat and Apache, deploys the WAR file and starts the app, following `appspec.yml`.
- **AWS CodePipeline**: orchestrates the Source, Build and Deploy stages automatically on every push.
- **Apache Tomcat & Apache HTTP Server**: Tomcat runs the Java app; Apache acts as a reverse proxy on port 80.

## Project Structure
```
nextwork-web-project/
├── appspec.yml                 # CodeDeploy instructions
├── buildspec.yml               # CodeBuild instructions
├── pom.xml                     # Maven configuration and dependencies
├── settings.xml                # Maven settings to use CodeArtifact
├── scripts/
│   ├── install_dependencies.sh # Installs Tomcat and Apache, configures the reverse proxy
│   ├── start_server.sh         # Starts and enables Tomcat and Apache
│   └── stop_server.sh          # Stops the services if they are running
└── src/
    └── main/
        └── webapp/
            └── index.jsp       # The web app's home page
```

## How the Pipeline Works
1. **Source**: a push to `master` sends a webhook from GitHub to CodePipeline, which fetches the code as a ZIP file.
2. **Build**: CodeBuild gets a CodeArtifact token, downloads the dependencies, compiles the app and packages it into `nextwork-web-project.war`, together with `appspec.yml` and the deployment scripts.
3. **Deploy**: CodeDeploy copies the WAR file to the production instance and runs the lifecycle hooks:
   - `ApplicationStop`: `scripts/stop_server.sh`
   - `BeforeInstall`: `scripts/install_dependencies.sh`
   - `ApplicationStart`: `scripts/start_server.sh`

The pipeline runs in **Superseded** mode, so only the latest change is processed. If the Deploy stage fails, it automatically rolls back to the last successful deployment.

## Setup
```bash
git clone https://github.com/VOTRE_USERNAME/nextwork-web-project.git
cd nextwork-web-project
```

To build locally with CodeArtifact, export an authorization token first (valid for 12 hours), then compile:
```bash
export CODEARTIFACT_AUTH_TOKEN=$(aws codeartifact get-authorization-token --domain nextwork --domain-owner YOUR_ACCOUNT_ID --region eu-west-3 --query authorizationToken --output text)
mvn -s settings.xml compile
```

## Deployment
Deployment is fully automated. To release a change:
```bash
git add .
git commit -m "Describe your change"
git push origin master
```

Then follow the execution in the CodePipeline console. Once all three stages are green, the new version is live at the production instance's public DNS (over `http://`).

## Contact
Taib Amjoud – [LinkedIn](www.linkedin.com/in/taib-amjoud)

## Acknowledgments
Thanks to NextWork for the project guide.
