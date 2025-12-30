# CICD_AWS_ECR_DOCKERHUB

A minimal shortcut repo to build Docker images with GitHub Actions and push to AWS ECR and Docker Hub. To get CI/CD running on your own server you only need to register a self-hosted runner and install Docker — then workflows will build and push images automatically.

## Quick summary
- Purpose: Build Docker image via GitHub Actions and push to AWS ECR + Docker Hub.
- Shortcut: On a server, run the runner commands, install Docker, add runner user to docker group — done.
- Requires: GitHub repo access, AWS & Docker Hub credentials configured as GitHub Secrets.

## Prerequisites
- Linux server (Ubuntu/Debian recommended) with sudo.
- GitHub repo admin to create a self-hosted runner.
- AWS IAM user with ECR push rights.
- Docker Hub account and token.

## Minimal server setup
1. Register and start self-hosted runner (get token from GitHub → Settings → Actions → Runners):
```bash
mkdir actions-runner && cd actions-runner
curl -o actions-runner.tar.gz -L https://github.com/actions/runner/releases/latest/download/actions-runner-linux-x64.tar.gz
tar xzf actions-runner.tar.gz
./config.sh --url https://github.com/soudeep-cse/CICD_AWS_ECR_DOCKERHUB --token YOUR_REGISTRATION_TOKEN
sudo ./svc.sh install
sudo ./svc.sh start
# or run interactively: ./run.sh
```

2. Install Docker (quick):
```bash
sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker
docker run --rm hello-world
```

After these steps the self-hosted runner can build and push Docker images when workflows run.

## Required GitHub Secrets
- AWS_ACCESS_KEY_ID
- AWS_SECRET_ACCESS_KEY
- AWS_REGION
- AWS_ACCOUNT_ID (optional, used for ECR URL)
- ECR_REPOSITORY
- DOCKERHUB_USERNAME
- DOCKERHUB_PASSWORD or DOCKERHUB_TOKEN

## How it works
On push (or manual dispatch) Actions builds the Docker image from the repo, tags it, then logs into AWS ECR and Docker Hub using the repository secrets and pushes the image.

## Troubleshooting (short)
- Runner fails to register: check token and repo URL.
- Permission errors running Docker: ensure runner user is in `docker` group and re-login.
- ECR push fails: verify IAM permissions and region/account ID.

## Security
Never store credentials in code. Use GitHub Secrets and least-privilege IAM policies.

License: MIT