pipeline {

  environment {
    PROJECT = "	still-smithy-279711"
    APP_NAME = "sample"
    FE_SVC_NAME = "${APP_NAME}"
    CLUSTER = "cluster-1"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:latest"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  
  containers:
  - name: aws
    image: amazon/aws-cli:latest
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/google.com/cloudsdktool/cloud-sdk:latest
    command:
    - cat
    tty: true
  - name: helm
    image: dtzar/helm-kubectl
    command:
    - cat
    tty: true
    
"""
}
  }
  stages {
    stage('Test') {
      steps {
        container('aws') {
          sh """
           aws eks --region us-east-2 update-kubeconfig --name cloudfront
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "#gcloud auth list"
          sh "#PYTHONUNBUFFERED=1 gcloud builds submit -t  us.gcr.io/still-smithy-279711/go . "
        }
      }
    }
    stage('Deploy ') {
      steps {
        container('helm') {
          sh """
          #helm ls
          helm repo add stable https://kubernetes-charts.storage.googleapis.com/ 
          helm repo update 
          helm install sample sampleapp/ --namespace test
          helm ls
          kubectl get pods --namespace default
          """ 
        }
      }
    }
  }
}
