## DevOps Setup

This project is deployed using Docker, Docker Compose, Nginx, and GitHub Actions. The application has a React + Vite frontend, a .NET backend API, and an Nginx reverse proxy that connects the two services.

### Services

- **frontend**: The React + Vite frontend application. It is built using a multi-stage Dockerfile. The first stage uses Node to install dependencies and run the production build. The final stage uses Nginx to serve the built static files.
- **backend**: The .NET backend API. It is built using a multi-stage Dockerfile. The first stage uses the .NET SDK to restore, build, and publish the app. The final stage uses the .NET ASP.NET runtime image, so the final container does not include the full SDK.
- **nginx**: The reverse proxy for the application. Nginx is the only service exposed to the host machine on port 80. It routes frontend traffic to the frontend container and API traffic to the backend container.

### How to run the Stack Locally

From the root of the repository, run: docker compose up --build -d

This starts the frontend, backend, and nginx services, and the app runs on port 80.

Then test the frontend at: http://localhost

And test the backend API at: http://localhost/api/ping

To stop the containers, run: docker compose down

### Nginx Routing

The Nginx reverse proxy handles routing between the frontend and backend services.

Requests to the frontend are routed through:

http://localhost

Requests to the backend API are routed through:

http://localhost/api/

The frontend and backend containers do not expose their ports directly to the host. Only the nginx container exposes port 80.

### CI/CD Pipeline

The GitHub Actions workflow is located at: .github/workflows/deploy-qa.yml

The workflow runs automatically on every push to the main branch. The pipeline builds Docker images for both the frontend and backend, pushes those images to GitHub Container Registry, then connects to the QA virtual machine using SSH.

After connecting to the QA server, the workflow pulls the latest images and runs: docker compose -f docker-compose.qa.yml up -d

This updates the QA environment with the newest version of the application.

All credentials are stored in GitHub Secrets. The SSH key, QA server IP address, and deployment user are not hardcoded in the repository.
