# Java Web App Deployment with AWS CI/CD

Welcome! This repository contains a Java web app built as part of NextWork's 6 Day DevOps Challenge.

## Table of Contents
- [Introduction](#introduction)
- [Technologies](#technologies)
- [Setup](#setup)
- [Contact](#contact)

## Introduction
This project is an introduction to building and deploying a Java web app on AWS, with a CI/CD pipeline that automates the release process.

## Technologies
- **Amazon EC2**: the web app is developed on a cloud virtual server.
- **VS Code (Remote - SSH)**: used to edit code directly on the EC2 instance.
- **Git & GitHub**: code is stored and versioned in this repository.
- **AWS CodeArtifact**: stores the project's dependencies in a private repository, with Maven Central as upstream. This keeps package versions consistent, secure and available even if Maven Central goes down.
- **AWS IAM**: an IAM role gives the EC2 instance read access to CodeArtifact, without storing any credentials on the server.
- **Coming soon**: AWS CodeBuild, CodeDeploy and CodePipeline.

## Setup
```bash
git clone https://github.com/VOTRE_USERNAME/nextwork-web-project.git
cd nextwork-web-project
```

To build with CodeArtifact, export an authorization token first, then compile:
```bash
export CODEARTIFACT_AUTH_TOKEN=$(aws codeartifact get-authorization-token --domain nextwork --domain-owner YOUR_ACCOUNT_ID --region eu-west-3 --query authorizationToken --output text)
mvn -s settings.xml compile
```

## Contact
Taib Amjoud – [LinkedIn](https://www.linkedin.com/in/VOTRE_PROFIL)

## Acknowledgments
Thanks to NextWork for the project guide.
