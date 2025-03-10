pipeline {
    agent any
        environment {
            DOCKER_IMAGE = "ephrash1/mavenwebapp01"
            DOCKER_TAG = "latest"
            EKS_CLUSTER_NAME = "ephrash-cluster"
            KUBECONFIG = credentials('K8s-credentials')
        
        //KUBE_CONFIG = "/home/ubuntu/.kube/config"
    }
    stages {
        stage('Source Code Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Muthaiyyan/maven-web-app.git'
                sh 'rm -f k8s-deploy.yml'  // Remove these if present in Git
                }
            }
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
                }
            }
        stage('Build Docker Image (Using Local Dockerfile)') {
            steps {
                sh 'docker system prune -a -f'
                sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG -f /var/lib/jenkins/workspace/Project2/Dockerfile .'
                }
            }
        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: 'Docker-Credential', url: '']) {
                    sh 'docker push $DOCKER_IMAGE:$DOCKER_TAG'
                }
            }
         }
        stage('Deploy to Kubernetes (Using Local Deployment YAML)') {
            steps {
                sh 'aws eks --region us-east-1 update-kubeconfig --name ephrash-cluster'
                sh 'kubectl apply -f /var/lib/jenkins/workspace/Project2/deploy-k8s.yaml'
                sh 'kubectl get svc'
            }
         }
        }
    }
