# Jenkins and SonarQube Docker Setup

This project sets up a Jenkins container and a SonarQube container using Docker Compose. The containers are configured to work together, allowing Jenkins to utilize SonarQube for code quality analysis.

## Project Structure

```
jenkins-sonarqube-docker
├── jenkins
│   └── docker-compose.yaml
├── sonarqube
│   └── docker-compose.yaml
├── docker-compose.yaml
└── README.md
```

## Services

### Jenkins

- **Image**: The Jenkins service uses the official Jenkins image.
- **Ports**: Exposes port 8080 for accessing the Jenkins web interface.
- **Environment Variables**: Configured to connect with SonarQube for code analysis.

### SonarQube

- **Image**: The SonarQube service uses the official SonarQube community image.
- **Database**: Utilizes PostgreSQL for storing analysis data.
- **Health Checks**: Ensures the database is ready before starting SonarQube.

## Setup Instructions

1. **Clone the Repository**: 
   ```bash
   git clone <repository-url>
   cd jenkins-sonarqube-docker
   ```

2. **Start the Services**: 
   Run the following command to start both Jenkins and SonarQube:
   ```bash
   docker-compose up -d
   ```

3. **Access Jenkins**: 
   Open your browser and navigate to `http://localhost:8080` to access the Jenkins interface.

4. **Access SonarQube**: 
   Open your browser and navigate to `http://localhost:9000` to access the SonarQube interface.

## Usage Guidelines

- Configure Jenkins jobs to trigger SonarQube analysis as part of your CI/CD pipeline.
- Ensure that the necessary plugins for SonarQube integration are installed in Jenkins.

## Additional Information

Refer to the individual `docker-compose.yaml` files in the `jenkins` and `sonarqube` directories for specific configurations and options.