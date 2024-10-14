# Trivy
- Trivy is an open-source vulnerability scanner that detects security issues in container images, file systems, repositories, and even Kubernetes clusters for vulnerabilities, including misconfigurations and secrets. 
- Trivy also scans misconfigurations in configuration files such as Terraform, Kubernetes manifests, and Helm charts.

## Key Features of Trivy:
- Container Image Scanning: Scans for known vulnerabilities in container images.
- Infrastructure as Code (IaC) Scanning: Detects misconfigurations in IaC files.
- File System Scanning: Scans the entire filesystem for vulnerabilities or misconfigurations.
- Kubernetes Cluster Scanning: Analyzes running Kubernetes clusters for security risks.
- Secret Detection: Finds hardcoded secrets in source code repositories.

## Installation 
1. Linux (Ubuntu/Debian)
    ```
    sudo apt-get update
    sudo apt-get install wget apt-transport-https gnupg lsb-release -y
    wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
    echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
    sudo apt-get update
    sudo apt-get install trivy
    ```

2. Using Trivy Docker Image
If Docker is installed, you can use Trivy without installing it directly on the host machine:

    ```
    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image <image-name>
    ```

3. Installation via Binary
Download the binary (replace v0.44.0 with the latest version)
    ```
    wget https://github.com/aquasecurity/trivy/releases/download/v0.44.0/trivy_0.44.0_Linux-64bit.deb
    ```
Install the binary
    ```
    sudo dpkg -i trivy_0.44.0_Linux-64bit.deb
    ```

## Basic Trivy Commands

1. Scanning Docker Images
Trivy can scan Docker images for vulnerabilities.
    ```
    trivy image <image-name>
    trivy image nginx:latest
    ```

2. Scanning File Systems
You can scan local file systems for vulnerabilities, for example, a specific directory:
    ```
    trivy fs /path/to/scan
    trivy fs --format table -o trivy-fs-report.html /path/to/scan   # o = Output
    ```

3. Scanning Git Repositories
You can also scan a Git repository for vulnerabilities and misconfigurations:
    ```
    trivy repo https://github.com/aquasecurity/trivy
    ```

4. Scanning Kubernetes Clusters
Trivy can also be used to scan running Kubernetes clusters:
    ```
    trivy k8s cluster
    ```

5. Scanning Infrastructure as Code (IaC)
You can scan configuration files like Terraform, CloudFormation, and Kubernetes manifests for security issues:
    ```
    trivy config /path/to/config
    trivy config ./terraform
    ```

6. Scanning for Secrets
Trivy can scan for hardcoded secrets in a directory or code repository:
    ```
    trivy fs --security-checks secret /path/to/code
    ```

7. Output Formats
Trivy supports different output formats, such as JSON and table format:
    ```
    # Output in JSON format
    trivy image --format json -o result.json <image-name>

    # Output in a simple table format
    trivy image <image-name>
    ```

8. Excluding Unwanted Vulnerabilities
You can exclude certain vulnerabilities using an ignore file:
    ```
    trivy image --ignorefile .trivyignore <image-name>
    ```

### Advanced Trivy Commands
1. Ignoring Specific Vulnerabilities
Sometimes, you might want to ignore certain vulnerabilities. Trivy allows you to do this using the --ignore-unfixed and --ignorefile options.
    ```
    # Ignore all vulnerabilities that are not fixed
    trivy image --ignore-unfixed nginx:latest

    # Use an ignore file
    trivy image --ignorefile .trivyignore nginx:latest
    ```
2. Generating Reports
Trivy can generate reports in various formats like JSON, HTML, and SARIF for integration with other tools.
    ```
    # Generate a JSON report
    trivy image --format json -o report.json nginx:latest

    # Generate an HTML report
    trivy image --format html -o report.html nginx:latest

    # Generate a SARIF report
    trivy image --format sarif -o report.sarif nginx:latest
    ```
3. Automating Scans in CI/CD Pipelines
Trivy is commonly integrated into CI/CD pipelines to automate security scanning. Here a basic example using a Jenkins pipeline:
    ```
    stage('Security Scan') {
        steps {
            // Scan the Docker image
            sh 'trivy image nginx:latest --exit-code 1 --severity HIGH,CRITICAL'
        }
    }
    ```
4. Integrating Trivy with Jenkins
- Trivy Installation: Ensure Trivy is installed on your Jenkins server or the agents executing the pipeline.
- Jenkins Plugins: Ensure necessary plugins like Pipeline, GitHub Integration, etc., are installed.

Trivy Image Scan:
-- Scans the built Docker image for high and critical vulnerabilities.
-- The **--exit-code 1** option ensures the build fails if vulnerabilities of specified severity are found.
    ```
    stage('Trivy Image Scan') {
        steps {
            // Scan Docker image with Trivy
            sh "trivy image --exit-code 1 --severity HIGH,CRITICAL ${IMAGE_NAME}"
        }
    }
    ```
