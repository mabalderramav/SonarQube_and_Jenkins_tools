# SonarQube_and_Jenkins_tools

Docker Compose setups for:

- Microsoft SQL Server
- SonarQube with PostgreSQL
- Jenkins controller
- Jenkins inbound agents

## Project structure

```text
.
├── jenkins/
│   ├── docker-compose.yaml
│   └── jenkins-agent/
│       ├── docker-compose.yaml
│       ├── docker-compose-3-agents.yaml
│       ├── .env.example
│       └── .env.3-agents.example
├── mssql-server/
│   └── docker-compose.yaml
└── sonarqube/
    └── docker-compose.yaml
```

## Requirements

- Docker
- Docker Compose
- An external Docker network named `miasoftnanus-network`

Create the shared network if it does not exist:

```bash
docker network create miasoftnanus-network
```

## Services

### MSSQL Server

Compose file: `mssql-server/docker-compose.yaml`

- Image: `mcr.microsoft.com/mssql/server:2025-latest`
- Container name: `sqlserver2025`
- Port: `1433`
- Volume: `sqlserver_data`
- Network: `miasoftnanus-network`

Start MSSQL Server:

```bash
docker compose -f mssql-server/docker-compose.yaml up -d
```

The current Compose file sets:

- `ACCEPT_EULA=1`
- `MSSQL_PID=Developer`
- `MSSQL_SA_PASSWORD=Aldo1235`

Change the SA password before using this outside a local development environment.

### SonarQube

Compose file: `sonarqube/docker-compose.yaml`

- SonarQube image: `sonarqube:community`
- Database image: `postgres:15`
- SonarQube port: `9000`
- Database container: `postgres_sonar`
- Network: `miasoftnanus-network`

Start SonarQube and PostgreSQL:

```bash
docker compose -f sonarqube/docker-compose.yaml up -d
```

Default database settings in the Compose file:

- Database: `sonar`
- Username: `sonar`
- Password: `sonar`

Open SonarQube at `http://localhost:9000`.

### Jenkins Controller

Compose file: `jenkins/docker-compose.yaml`

- Image: `jenkins/jenkins:lts`
- Container name: `jenkins`
- HTTP port: `8080`
- Inbound agent port: `50000`
- Volume: `jenkins_home`
- Docker socket mounted for Docker-based jobs
- Network: `miasoftnanus-network`

Start Jenkins:

```bash
docker compose -f jenkins/docker-compose.yaml up -d
```

Open Jenkins at `http://localhost:8080`.

### Jenkins Agent

Single-agent Compose file: `jenkins/jenkins-agent/docker-compose.yaml`

- Image: `jenkins/inbound-agent:latest-jdk17`
- Connects to Jenkins using `JENKINS_URL`
- Uses WebSocket by default
- Requires a Jenkins node secret

Setup:

1. Copy `jenkins/jenkins-agent/.env.example` to `jenkins/jenkins-agent/.env`.
2. Set `JENKINS_SECRET` from the Jenkins node configuration.
3. Adjust `JENKINS_URL` and `JENKINS_DOCKER_NETWORK` if needed.
4. Start the agent.

Run:

```bash
docker compose -f jenkins/jenkins-agent/docker-compose.yaml --env-file jenkins/jenkins-agent/.env up -d
```

Important:

- `JENKINS_URL` in `.env.example` is currently `http://localhost:8080`.
- For the current Jenkins controller in this repository, `JENKINS_DOCKER_NETWORK` should normally be `miasoftnanus-network`.
- If the agent must reach Jenkins through the shared Docker network, use the Jenkins container hostname instead, for example `http://jenkins:8080`.

### Jenkins Agents (3 nodes)

Three-agent Compose file: `jenkins/jenkins-agent/docker-compose-3-agents.yaml`

This stack starts:

- `jenkins-agent-01`
- `jenkins-agent-02`
- `jenkins-agent-03`

Each agent has:

- Its own container name
- Its own Jenkins node name
- Its own Jenkins secret
- Its own persistent work volume

Setup:

1. Copy `jenkins/jenkins-agent/.env.3-agents.example` to `jenkins/jenkins-agent/.env.3-agents`.
2. Configure `JENKINS_AGENT_01_SECRET`, `JENKINS_AGENT_02_SECRET`, and `JENKINS_AGENT_03_SECRET`.
3. Verify `JENKINS_URL` and `JENKINS_DOCKER_NETWORK`. For the current Jenkins controller in this repository, the network is typically `miasoftnanus-network`.
4. Create matching nodes in Jenkins.
5. Start the stack.

Run:

```bash
docker compose -f jenkins/jenkins-agent/docker-compose-3-agents.yaml --env-file jenkins/jenkins-agent/.env.3-agents up -d
```

## Suggested startup order

Start the stacks in this order:

1. MSSQL Server
2. SonarQube
3. Jenkins controller
4. Jenkins agent or Jenkins 3-agent stack

## Useful commands

Show running containers:

```bash
docker ps
```

Stop a stack:

```bash
docker compose -f <compose-file> down
```

View logs:

```bash
docker compose -f <compose-file> logs -f
```
