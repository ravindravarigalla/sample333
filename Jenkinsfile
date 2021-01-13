pipeline {

  environment {
    PROJECT = "	dazzling-scheme-281712"
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
  - name: kaniko
    image: gcr.io/kaniko-project/executor:v1.0.0
    imagePullPolicy: Always
    args: ["--dockerfile=Dockerfile",
            "--context=",
            "--destination=${DOCKER_USERNAME}/kaniko-demo"]
    volumeMounts:
      - name: docker-config
        mountPath: /kaniko/.docker
    resources:
      limits:
        cpu: 1
        memory: 1Gi
  volumes:
  - name: docker-config
    projected:
      sources:
      - secret:
          name: regcred
          items:
            - key: .dockerconfigjson
              path: config.json
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
    image: gcr.io/dazzling-scheme-281712/helm3-test
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
          #aws eks --region us-east-2 update-kubeconfig --name cloudfront
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "gcloud auth list"
          sh "#PYTHONUNBUFFERED=1 gcloud builds submit -t  gcr.io/dazzling-scheme-281712/go . "
          sh "#gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project still-smithy-279711"
        }
      }
    }
    stage('Deploy ') {
      steps {
        container('helm') {
          sh """
          #helm ls
          gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project dazzling-scheme-281712
          helm repo add stable https://charts.helm.sh/stable
          helm install sample sampleapp -n default
          #kubectl create deployment nodejs --image=gcr.io/still-smithy-279711/node11
          #kubectl get pods --namespace default
          """ 
        }
      }
    }
  }
}
