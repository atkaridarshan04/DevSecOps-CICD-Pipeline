# Implementing GitLab CI/CD Pipeline

This guide covers the implementation of a GitLab CI/CD pipeline. The pipeline includes the following configurations and tools:

- **Maven** for build automation
- **Trivy** for file scanning
- **SonarQube** for code quality checks
- **Docker** for containerization
- **Kubernetes (K8s)** for deployment
- A **self-hosted GitLab Runner**

## Project Architecture
![Project Architecture](./images/project_architecture.png)

---

## Steps to Implement

### 1. Import the Project
Start by importing the project into GitLab.
```bash
https://github.com/atkaridarshan04/GitLab-CICD.git
```

### 2. Configure the GitLab Runner

#### Create a VM
1. Update the system packages:
   ```bash
   sudo apt update
   ```
2. Log in to GitLab and navigate to **Settings > CI/CD > Runners**.
   ![Navigate to Runners](./images/runner_1.png)
3. Register a new runner:
     ![Provide Runner Tag](./images/runner_2.png)
     ![Runner Installation Step 1](./images/runner_3.png)
     ![Runner Installation Step 2](./images/runner_4.png)
     ![Runner Installation Step 3](./images/runner_5.png)

---

### 3. Set Up SonarQube
#### Using Docker
1. Deploy SonarQube using Docker:  
   ```bash
   docker run -d --name sonarqube -p 9000:9000 sonarqube:lts-community
   ```
   > **Note:** Username and password are both `admin`.

   ![SonarQube Deployment](./images/sonar_1.png)

#### Configure SonarQube in GitLab
1. Create a Personal Access Token (PAT) in GitLab with the required permissions:
   ![Create PAT Step 1](./images/sonar_2.png)
   ![Create PAT Step 2](./images/sonar_3.png)
   ![Create PAT Step 3](./images/sonar_4.png)
   ![Create PAT Step 4](./images/sonar_5.png)
   ![Create PAT Step 5](./images/sonar_6.png)
   ![Create PAT Step 6](./images/sonar_7.png)

2. Create a file named `sonar-project.properties` in your project with the following content:
   ![Edit sonar-project.properties](./images/sonar_08.png)
   ![sonar-project.properties Example](./images/sonar_8.png)

3. Add the PAT to GitLab CI/CD variables:
   ![Add PAT Step 1](./images/sonar_09.png)
   ![Add PAT Step 2](./images/sonar_9.png)
   ![Add PAT Step 3](./images/sonar_10.png)
   ![Add PAT Step 4](./images/sonar_11.png)
   ![Add PAT Step 5](./images/sonar_12.png)
   ![Add PAT Step 6](./images/sonar_13.png)

4. Paste this stage in the pipeline file:
   ![Pipeline Stage Example](./images/sonar_14.png)

   > **Note:** Add the stage and remove the entrypoint for the container.

   ![Entrypoint Removal Example](./images/sonar_15.png)

---

### 4. Set Up Kubernetes Configuration
1. Create a kind cluster 
   ```bash
   kind create cluster --config kind-config.yml
   ```
   kind-config.yml
   ```yaml
   kind: Cluster
   apiVersion: kind.x-k8s.io/v1alpha4
   nodes:
   - role: control-plane
     extraPortMappings:
       - containerPort: 30080  
         hostPort: 30080
         protocol: TCP
   ```

2. Access your Kubernetes cluster configuration:
   ```bash
   cd $HOME/.kube
   ```
3. Copy the `config` file contents and encode it.
   ```bash
   echo -n "copied_content" | base64
   ```
4. In GitLab, create a CI/CD variable named `KUBECONFIG_CONTENT` and paste the 'encoded_config' contents.
   ![Encoded Variable paste](./images/k8s_1.png)
   ![All variables](./images/all_variables.png)

---

### 5. Run the Pipeline
1. Run the pipeline file [.gitlab-ci.yml](.gitlab-ci.yml).
2. Monitor the pipeline stages for successful completion.
   ![Pipeline Status](./images/pipeline_status.png)

---

