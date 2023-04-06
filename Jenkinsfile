pipeline {
    agent any
    tools {nodejs "nodejs"}
    stages {

    // Tests
    stage('Unit Tests') {
      steps{
        sh 'npm install'
	sh 'npm test -- --watchAll=false'
      }
    }
        
    // Building Docker images
    stage('Building image') {
      steps{
        sh 'docker build -t demo .'
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Push Docker Image to ECR') {
      steps{
        withAWS(credentials: 'demo-admin-user', region: 'us-east-1') {
          sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 782983671572.dkr.ecr.us-east-1.amazonaws.com'
          sh 'docker tag demo:latest 782983671572.dkr.ecr.us-east-1.amazonaws.com/demo:latest'
          sh 'docker push 782983671572.dkr.ecr.us-east-1.amazonaws.com/demo:latest'
        }
      }
    }
      
    stage('Integrate Jenkins with EKS Cluster and Deploy App') {
      steps{
        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kube-config', namespace: '', serverUrl: '') {
          sh "kubectl apply -f eks-deploy-k8s.yaml"
        }
      }
    }
    }
}
