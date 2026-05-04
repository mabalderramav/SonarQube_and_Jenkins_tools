# SonarQube_and_Jenkins_tools
SonarQube_and_Jenkins_tools

## Jenkins Agent (Docker)

A standalone Jenkins inbound agent Compose stack is available in `jenkins-agent/docker-compose.yaml`.

### Quick start

1. Copy `jenkins-agent/.env.example` to `jenkins-agent/.env`.
2. Replace `JENKINS_SECRET` with the secret from your Jenkins node configuration.
3. Start the agent:

```bash
docker compose -f jenkins-agent/docker-compose.yaml --env-file jenkins-agent/.env up -d
```

### Notes

- `JENKINS_DOCKER_NETWORK` must match the Docker network where the main Jenkins container is attached.
- `JENKINS_URL` defaults to `http://jenkins:8080` and assumes the agent shares the same Docker network as Jenkins.
