pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID     = credentials('enkins-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')
        AWS_DEFAULT_REGION = 'us-east-1'
        EKS_CLUSTER_NAME = 'demo'
    }
    
    
    stages {
        stage('get code from scm') {
            steps {
                script {
                    sh 'git --version'
                }
            }
        }
        stage('mvn build') {
            steps {
                script{
                    sh 'mvn --version'
                }
            }
        }
        stage('Read DockerHub credentials from Vault') {
            steps {
                script {
                    withVault(configuration: [timeout: 60, vaultCredentialId: 'vault-cred', vaultUrl: 'http://184.73.135.200:8200'], vaultSecrets: [[path: 'secret/dockerhub', secretValues: [[vaultKey: 'username'], [vaultKey: 'password']]]]) {
              sh 'docker login -u $username -p $password'
             }
                }
            }
        
        }
        stage('Deploy to EKS') {     
      steps {
        
            sh 'aws eks --region $AWS_DEFAULT_REGION update-kubeconfig --name $EKS_CLUSTER_NAME'
            sh 'kubectl get nodes'
            sh 'kubectl apply -f kubernetes/development/nginx.yml'
          }
      }
        }
        }
