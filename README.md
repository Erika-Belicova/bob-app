
# BobApp

**BobApp** is a web application where users can read daily jokes shared by Bob. This project implements a **CI/CD workflow using GitHub Actions** to automate testing, code quality checks via SonarCloud, building, and deployment via Docker Hub.

---

## Table of Contents

1. [Prerequisites](#1-prerequisites)
2. [Clone the Project](#2-clone-the-project)
3. [Back-End Setup](#3-back-end-setup)
4. [Front-End Setup](#4-front-end-setup)
5. [Docker Containers](#5-docker-containers)
6. [CI/CD Workflow Overview](#6-cicd-workflow-overview)
7. [KPIs and Quality Gates](#7-kpis-and-quality-gates)
8. [Troubleshooting](#8-troubleshooting)

---

## 1. Prerequisites

Make sure the following tools are installed:

- **Node.js** (v16)
- **Angular CLI** – `npm install -g @angular/cli`  
- **Java 11**
- **Maven**
- **Docker**  
- **Docker Hub account** for pushing images  

---

## 2. Clone the Project

Clone your the repository:
```
git clone https://github.com/Erika-Belicova/bob-app.git
cd bob-app
```

The repository contains two main folders:

- **back** – Spring Boot back-end  
- **front** – Angular front-end  

---

## 3. Back-End Setup

Navigate to the back-end folder:
```
cd back
```

Install dependencies:
```
mvn clean install
```

Run the back-end:
```
mvn spring-boot:run
```

The API will be available at `http://localhost:8080` by default.  

### Run Back-End Tests and Generate Coverage
```
mvn clean verify
```

- Coverage reports are generated in: `back/target/site/jacoco`  
- **CI/CD:** These reports are automatically uploaded as artifacts in the GitHub Actions workflow after successful test runs. 

---

## 4. Front-End Setup

Navigate to the front-end folder:
```
cd front
```

Install dependencies:
```
npm ci
```

Run the front-end application:
```
npm run start
```

The Angular app will be available at `http://localhost:4200`.  

### Run Front-End Tests and Generate Coverage
```
npm run test:prod
```

- Coverage reports are generated in: `front/coverage/bobapp`  
- **CI/CD:** These reports are automatically uploaded as artifacts in the GitHub Actions workflow after successful test runs. 

---

## 5. Docker Containers

Both back-end and front-end can be built and run as Docker containers.

### Back-End Docker
```
cd back
docker build -t bobapp-back:latest .
docker run -p 8080:8080 --name bobapp-back -d bobapp-back:latest
```

### Front-End Docker
```
cd front
docker build -t bobapp-front:latest .
docker run -p 4200:4200 --name bobapp-front -d bobapp-front:latest
```

**Note:** The CI/CD workflow builds and pushes these images automatically to Docker Hub with both the SHA tag and `latest` tag.  

---

## 6. CI/CD Workflow Overview

The GitHub Actions workflow (`.github/workflows/ci-cd-workflow.yml`) performs the following steps:

1. **On Pull Request / Push to main**:
   - Back-end tests, coverage generation, SonarCloud analysis  
   - Front-end tests, coverage generation, SonarCloud analysis  

2. **Docker Build & Push** (only if tests and Sonar quality gates pass):
   - Build and push back-end Docker image  
   - Build and push front-end Docker image  

**Secrets required in GitHub repository**:

- `SONAR_TOKEN` – for SonarCloud  
- `DOCKER_HUB_USERNAME` – Docker Hub username  
- `DOCKER_HUB_TOKEN` – Docker Hub access token  

---

## 7. KPIs and Quality Gates

The workflow uses **SonarCloud** to assess code quality metrics. Recommended thresholds:

- **Minimum code coverage:** 80% (back-end + front-end)  
- **New blocker issues:** 0  
- **Maintainability rating:** A  

These thresholds help assess the quality of the code being merged and deployed. 

---

## 8. Troubleshooting

- **Back-End fails to start (running from source):**  
  Ensure Java 11 and Maven are installed, dependencies are installed, and no other service is using port `8080`.

- **Front-End fails to start (running from source):**  
  Ensure Node.js 16 is installed and dependencies are installed via `npm ci`.

- **Docker Hub issues:**  
  Ensure credentials for Docker Hub are correct in GitHub secrets.

- **Coverage reports missing in workflow:**  
  Ensure tests pass and `actions/upload-artifact` step is successful in GitHub Actions.

---
