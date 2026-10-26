# Jenkins CI/CD Pipeline with Docker

## Project Overview

This project demonstrates a simple Continuous Integration and Continuous Deployment (CI/CD) pipeline using **Jenkins and Docker**. The pipeline automates application checkout, Docker image building, basic application testing, and deployment whenever code changes are pushed to GitHub.

The application is a static HTML webpage served using the Nginx web server inside a Docker container.

## Objectives

- Automate application build and deployment using Jenkins.
- Build Docker images using a Dockerfile.
- Test the application before deployment.
- Configure GitHub integration to trigger Jenkins pipelines automatically.
- Understand the basic CI/CD workflow.

## Technologies Used

- **Jenkins** – CI/CD automation
- **Docker** – Containerization
- **Git & GitHub** – Version control and source code management
- **Nginx** – Web server
- **Ubuntu Linux** – Operating system
- **Shell scripting** – Pipeline automation

## Project Structure

```text
jenkins-cicd-pipeline/
├── Jenkinsfile
├── Dockerfile
├── index.html
└── README.md
```

## How It Works

The pipeline follows these stages:

1. **Checkout:** Retrieves the latest application code from GitHub.
2. **Build:** Builds a Docker image using the Dockerfile.
3. **Test:** Starts a temporary container and verifies the application's HTTP response.
4. **Deploy:** Replaces the existing application container with the newly built version.

### CI/CD Workflow

```text
Developer
    |
    | Git Push
    v
GitHub Repository
    |
    | Webhook / SCM Polling
    v
Jenkins Pipeline
    |
    v
Checkout Source Code
    |
    v
Build Docker Image
    |
    v
Test Application
    |
    v
Deploy Docker Container
    |
    v
Application Available on Port 8080
```

## Prerequisites

Before running the project, ensure the following are installed and configured:

- Jenkins
- Docker Engine
- Git
- Ubuntu Linux or another supported Linux environment
- A GitHub repository containing the project files

The Jenkins agent must have access to the Docker CLI and Docker daemon. The deployment port must be available.

## Installation and Setup

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/jenkins-cicd-pipeline.git

cd jenkins-cicd-pipeline
```

Replace `YOUR_USERNAME` with your GitHub username.

### 2. Install Docker

On Ubuntu, run:

```bash
sudo apt update
sudo apt install -y docker.io git curl

sudo systemctl enable --now docker
```

Verify Docker:

```bash
docker --version
docker ps
```

Configure Docker access for the Jenkins agent according to your environment. Membership in the Docker group grants highly privileged access, so use it only on a trusted system.

### 3. Build the Docker Image

```bash
docker build -t jenkins-cicd-app:manual .
```

Verify the image:

```bash
docker images
```

### 4. Run the Application Manually

```bash
docker run -d \
  --name jenkins-cicd-manual \
  -p 8080:80 \
  jenkins-cicd-app:manual
```

Open the application in your browser:

**http://localhost:8080**

To stop and remove the test container:

```bash
docker rm -f jenkins-cicd-manual
```

## Jenkins Configuration

### 1. Create a Pipeline Job

1. Open the Jenkins dashboard.
2. Click **New Item**.
3. Enter `jenkins-cicd-pipeline` as the job name.
4. Select **Pipeline** and click **OK**.

### 2. Configure Source Code Management

In the Pipeline configuration, select **Pipeline script from SCM**.

| Setting        | Value                      |
| -------------- | -------------------------- |
| SCM            | Git                        |
| Repository URL | Your GitHub repository URL |
| Branch         | `*/main`                   |
| Script Path    | `Jenkinsfile`              |

Save the configuration.

### 3. Configure Automatic Triggers

For webhook-based triggering:

1. Enable **GitHub hook trigger for GITScm polling** in the Jenkins job.
2. Open your GitHub repository settings.
3. Navigate to **Webhooks** and add a webhook.
4. Set the payload URL to `https://YOUR_JENKINS_URL/github-webhook/`.
5. Select `application/json` as the content type.
6. Save and verify webhook delivery.

The Jenkins URL must be reachable by GitHub.

Alternatively, configure Poll SCM with this schedule:

```text
H/2 * * * *
```

This checks for changes approximately every two minutes.

## Running the Pipeline

1. Open the Jenkins job.
2. Click **Build Now**.
3. Open the latest build.
4. Select **Console Output** to review execution logs.
5. Verify that all four stages complete successfully.

## Verify the Deployment

Check the running containers:

```bash
docker ps
```

Check the Docker images:

```bash
docker images
```

Test the deployed application:

```bash
curl --fail http://localhost:8080/
```

Expected HTML output includes:

```html
<h1>Welcome to Jenkins CI/CD Pipeline!</h1>
```

You can also open `http://localhost:8080` in your browser.

For remote deployment, access the application through the server's IP address or hostname, provided network access is configured.

## Test Automatic Deployment

Make a change to `index.html`, then commit and push it:

```bash
git add index.html
git commit -m "Update application homepage"
git push origin main
```

Jenkins should start a new pipeline run if the webhook or SCM polling configuration is working.

After the pipeline succeeds, refresh the webpage to verify the updated content.

## Troubleshooting

| Issue                                 | Possible Solution                                                          |
| ------------------------------------- | -------------------------------------------------------------------------- |
| Docker permission denied              | Verify the Jenkins agent's Docker daemon access and socket permissions.    |
| Port 8080 already in use              | Stop the conflicting service or change `HOST_PORT` in the Jenkinsfile.     |
| GitHub changes do not trigger Jenkins | Check webhook delivery, repository URL, branch, and trigger configuration. |
| Docker build fails                    | Review the Dockerfile and Jenkins Console Output.                          |
| Application does not respond          | Check `docker ps`, container logs, and firewall rules.                     |
| Pipeline test fails                   | Verify that curl is installed and port 18080 is available on the agent.    |

View application logs:

```bash
docker logs jenkins-cicd-container
```

## Key Learnings

- Creating declarative Jenkins pipelines.
- Automating Docker image builds.
- Testing applications before deployment.
- Integrating Jenkins with GitHub.
- Configuring webhook-based CI/CD triggers.
- Deploying and verifying containerized applications.
- Troubleshooting common pipeline and container issues.

## Future Improvements

- Push Docker images to Docker Hub or Amazon ECR.
- Add automated unit and integration tests.
- Introduce image vulnerability scanning with Trivy.
- Store credentials securely using Jenkins Credentials.
- Deploy to AWS EC2, Amazon ECS, or Amazon EKS.
- Add rollback and health-check mechanisms.
- Implement notifications for build failures and successful deployments.

## Author

**Ratnesh Vansh Saxena**

GitHub: [ratneshvansh13](https://github.com/ratneshvansh13)

---

_This project was created for learning and demonstrating the fundamentals of CI/CD automation using Jenkins and Docker._
