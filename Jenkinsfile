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
            /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=trainingad1/test
            """
        }
      }
    }
    stage('helm') {
      steps {
        container('helm') {
          sh """
            kubectl config view
            kubectl config set-context 265265976143684875-cc6efa8a194934955bf8b4aa3891e9ae8  --user=v.FISClouds.rizwan --namespace=bootcamp \
              && kubectl config use-context 265265976143684875-cc6efa8a194934955bf8b4aa3891e9ae8
              kubectl get nodes
              kubectl get pods -n bootcamp
            """
        }
      }
    }
  }
}
