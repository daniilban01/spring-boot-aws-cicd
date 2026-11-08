# Spring Boot CI/CD Pipeline on AWS

CI/CD pipeline built with **GitHub Actions** that automatically tests, packages and deploys a Spring Boot application to **AWS Elastic Beanstalk**, plus a scheduled backup job to **Amazon S3**.

> The Spring Boot application was provided as a starter project in the SkillBrain DevOps Engineer course. The CI/CD pipeline, deployment configuration and backup workflow are my own work.

## Tech Stack
- **CI/CD:** GitHub Actions
- **Cloud:** AWS Elastic Beanstalk, Amazon S3
- **Application:** Java 17, Spring Boot 3, Maven, JUnit 5
- **Containerization:** Docker

## Pipelines

### `ci-cd-eb.yml`: build, test and deploy
Runs on every push to `main` (or manually):
1. **Build & test:** sets up JDK 17 with Maven caching and runs `mvn clean verify` (including JUnit tests).
2. **Package:** builds the JAR and bundles it with a `Procfile` into a deployment package, uploaded as a workflow artifact.
3. **Deploy:** authenticates to AWS and deploys the package to Elastic Beanstalk, using the commit SHA as the version label.

### `backup-to-s3.yml`: scheduled backup
Runs every night at 02:00 UTC (or manually): archives the repository and uploads it to an S3 bucket.

All credentials (AWS keys, region, bucket name) are stored as **GitHub Secrets** and never committed to the repository.

## Application Endpoints
- `GET /` - web UI
- `GET /api/health` - application status
- `GET /api/info` - status message
- `GET /api/pipeline` - pipeline steps

## Run Locally
```bash
mvn clean test
mvn spring-boot:run
```
The app starts on `http://localhost:8080`.

To run it with Docker:
```bash
mvn clean package
docker build -t spring-boot-app .
docker run -p 8080:8080 spring-boot-app
```
