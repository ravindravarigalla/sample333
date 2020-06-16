pipeline {

  environment {
    PROJECT = "gcp-shared-host-nonprod-276203"
    APP_NAME = "sampleapp"
    FE_SVC_NAME = "${APP_NAME}"
    CLUSTER = "gke-apps-nonprod"
    CLUSTER_ZONE = "asia-southeast2-a"
    IMAGE_TAG = "us.gcr.io/${PROJECT}/${APP_NAME}:latest"
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
  - name: golang
    image: golang:1.10
    command:
    - cat
    tty: true
  - name: gcloud
    image: us.gcr.io/gcp-shared-host-nonprod-276203/gcloud
    command:
    - cat
    tty: true
  - name: helm
    image: us.gcr.io/gcp-shared-host-nonprod-276203/helm3
    command:
    - cat
    tty: true
"""
}
  }
  stages {
    stage('Test') {
      steps {
        container('golang') {
          sh """
            ln -s `pwd` /go/src/sample-app
            cd /go/src/sample-app
            go test
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "gcloud auth list"
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} ."
        }
      }
    }
    stage('Deploy ') {
      steps {
        container('helm') {
          sh """
          #helm ls
          gcloud container clusters get-credentials gke-apps-nonprod --zone asia-southeast2-a --project gcp-shared-host-nonprod-276203
          kubectl get pods --namespace default
          helm repo add stable https://kubernetes-charts.storage.googleapis.com/ 
          helm repo update 
          helm install sample-app2 sampleapp/ --namespace default
          helm ls
          kubectl get pods --namespace default
          """ 
        }
      }
    }
  }
}
