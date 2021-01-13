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
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: docker-config
        mountPath: /kaniko/.docker
  volumes:
  - name: docker-config
    projected:
      sources:
      - secret:
          name: regcred
          items:
            - key: .dockerconfigjson
              path: config.json
    
"""
}
  }
  stages {
    stage('Test') {
      steps {
        container('kaniko') {
          sh """
             /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=ravindra777/dockertest
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "#gcloud auth list"
          sh "#PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} ."
        }
      }
    }
    stage('Deploy ') {
      steps {
        container('helm') {
          sh """
          #helm ls
          #gcloud container clusters get-credentials gke-apps-nonprod --zone asia-southeast2-a --project gcp-shared-host-nonprod-276203
          #ubectl get pods --namespace default
          helm repo add stable https://kubernetes-charts.storage.googleapis.com/ 
          helm repo update 
          helm install sampleapp sampleapp/ --namespace default
          helm ls
          kubectl get pods --namespace default
          """ 
        }
      }
    }
  }
}
