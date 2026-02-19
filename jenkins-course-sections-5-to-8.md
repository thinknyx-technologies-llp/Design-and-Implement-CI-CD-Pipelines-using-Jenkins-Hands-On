
# Design and Implement CI/CD Pipelines using Jenkins
## Sections 5–8 (Hands-On Scripts)

---

# Section 5 – Jenkins Administration & Integrations

## 5.6 Docker Integration

### Dockerfile
```dockerfile
FROM alpine:latest
WORKDIR /app
COPY . .
CMD ["echo", "Docker Image Built Successfully from Jenkins Workspace"]
```

---

## 5.7 Ansible Integration

### Ansible Playbook
```yaml
---
- name: Jenkins Ansible Demo
  hosts: web
  become: yes

  tasks:
    - name: Create demo file
      file:
        path: /tmp/jenkins-ansible-demo.txt
        state: touch

    - name: Write content to file
      copy:
        content: "Deployed by Jenkins using Ansible"
        dest: /tmp/jenkins-ansible-demo.txt
```

---

## 5.9 Teams Notification Script
```bash
curl -X POST "$TEAMS_WEBHOOK_URL" -H "Content-Type: application/json" -d '{"text": "Jenkins Teams-Notify-Demo Freestyle Job Build Complete!"}'
```

---

# Section 6 – Jenkins Pipeline Fundamentals

## 6.5 Git Checkout Pipeline
```groovy
pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/madhuri75jha/simple-order-processing-app.git'
                echo "Git repository successfully checked out"
            }
        }
    }
}
```

---

## 6.8 Java CI Pipeline
```groovy
pipeline {
    agent any
    environment {
        APP_NAME = "simple-order-app"
        BUILD_VERSION = "1.0.${BUILD_NUMBER}"
        MAVEN_HOME = "/opt/maven"
        PATH = "/opt/maven/bin:${env.PATH}"
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/madhuri75jha/simple-order-processing-app.git'
            }
        }
        stage('Run Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build Java Application') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar'
            }
        }
    }
}
```

---

# Section 7 – Advanced Jenkins Pipelines

## 7.2 Parameterized Pipeline
```groovy
pipeline {
    agent any
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev','qa','prod'], description: 'Target environment')
        string(name: 'APP_VERSION', defaultValue: '1.0.0', description: 'Application version')
    }
    stages {
        stage('Build') {
            steps {
                echo "Building version ${params.APP_VERSION}"
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploying to ${params.ENVIRONMENT}"
            }
        }
    }
}
```

---

## 7.8 Parallel Stages
```groovy
pipeline {
    agent any
    stages {
        stage('Parallel Tasks') {
            parallel {
                stage('Build') {
                    steps { echo 'Building...' }
                }
                stage('Unit Tests') {
                    steps { echo 'Running tests...' }
                }
            }
        }
    }
}
```

---

# Section 8 – Pipeline Integrations & Deployment

## 8.2 Application Deployment Pipeline
```groovy
pipeline {
    agent any
    tools { maven 'maven_3' }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/madhuri75jha/simple-order-processing-app.git'
            }
        }
        stage('Build Application') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Deploy Application') {
            steps {
                sh '''
                mkdir -p /tmp/orderapp-deploy
                cp target/*.jar /tmp/orderapp-deploy/
                ls -l /tmp/orderapp-deploy
                '''
            }
        }
    }
}
```

---

## 8.3 Ansible Deployment Pipeline
```groovy
pipeline {
    agent any
    stages {
        stage('Deploy Apache using Ansible') {
            steps {
                sh 'ansible-playbook -i /var/lib/jenkins/ansible-demo1/hosts /var/lib/jenkins/ansible-demo1/deployApache.yml'
            }
        }
    }
}
```
