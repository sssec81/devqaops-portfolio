# DevQAOps Portfolio

A static DevOps portfolio website built to demonstrate practical coursework skills in cloud deployment, containerization, CI/CD, and infrastructure automation.

The site presents a personal portfolio for Shaman Jung Shah and is designed to be deployed with Docker on AWS EC2, with GitHub Actions used for build validation and deployment automation.

## Overview

This project is intentionally lightweight on the frontend and focused on deployment workflow. The website itself is a static HTML and CSS portfolio, while the surrounding project structure demonstrates:

- containerized delivery with Docker
- infrastructure provisioning with AWS CloudFormation
- CI/CD automation with GitHub Actions
- optional code quality scanning with SonarCloud
- deployment to an EC2 instance over SSH

## Tech Stack

- `HTML5`
- `CSS3`
- `Nginx (alpine)`
- `Docker`
- `GitHub Actions`
- `AWS EC2`
- `AWS CloudFormation`
- `SonarCloud`

## Project Structure

```text
.
|-- .github/
|   `-- workflows/
|       `-- pipeline.yaml
|-- cloudformation.yaml
|-- Dockerfile
|-- favicon.svg
|-- index.html
|-- sonar-project.properties
`-- styles.css
```

## Features

- Premium-style single-page portfolio layout
- Separate stylesheet for cleaner structure and maintainability
- Custom SVG favicon
- Dockerized static site served through Nginx
- GitHub Actions pipeline for build verification and deployment
- CloudFormation template for EC2-based hosting

## Local Development

Because this is a static website, you can preview it directly by opening `index.html` in a browser. If you want to test the production-style container locally, use Docker.

### Run with Docker

```bash
docker build -t devqaops-portfolio .
docker run -d -p 8080:80 --name devqaops-portfolio devqaops-portfolio
```

Then open:

```text
http://localhost:8080
```

To stop and remove the container:

```bash
docker stop devqaops-portfolio
docker rm devqaops-portfolio
```

## Docker Setup

The `Dockerfile` uses the `nginx:alpine` image, removes the default Nginx site, and copies the portfolio assets into `/usr/share/nginx/html/`.

Included assets:

- `index.html`
- `styles.css`
- `favicon.svg`

## GitHub Actions Pipeline

The workflow file is located at [`.github/workflows/pipeline.yaml`](.github/workflows/pipeline.yaml).

It currently performs two main jobs:

1. `build-and-verify`
2. `deploy`

### Build And Verify

This job:

- checks out the repository
- builds the Docker image
- optionally runs SonarCloud scanning if a `SONAR_TOKEN` secret is configured

### Deploy

This job runs only when code is pushed to the `main` branch.

It:

- connects to the EC2 instance using SSH
- moves into the project directory on the server
- fetches the latest code from `origin/main`
- rebuilds the Docker image with `--no-cache`
- replaces the running container

## Required GitHub Secrets

To use the deployment workflow, set these repository secrets in GitHub:

- `EC2_HOST`
- `EC2_USER`
- `EC2_SSH_KEY`

Optional:

- `SONAR_TOKEN`

## AWS Deployment

The `cloudformation.yaml` file provisions:

- an EC2 instance
- a security group allowing HTTP on port `80`
- SSH access on port `22`

The EC2 user data script:

- installs Docker and Git
- clones this repository
- builds the Docker image
- starts the portfolio container

## SonarCloud

SonarCloud configuration is defined in `sonar-project.properties`.

The current setup includes:

- project key
- organization
- project name
- source path
- exclusions for `.git` and `.github`

If `SONAR_TOKEN` is not configured, the workflow skips the SonarCloud scan step.

## Notes

- The repository ignores local `.pem` key files through `.gitignore`
- Deployment assumes the repository already exists on the EC2 instance at `/home/ec2-user/devqaops-portfolio`
- The workflow rebuilds the container on each deployment to keep the server aligned with the latest `main` branch state

## Future Improvements

- add automated HTML validation or linting
- add a custom Nginx configuration for caching and headers
- serve the site behind HTTPS with a reverse proxy or load balancer
- add a custom domain and DNS configuration
- expand the portfolio with project screenshots or live links

## Author

Shaman Jung Shah

DevOps and cloud engineering coursework portfolio.
