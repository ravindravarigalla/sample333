pipeline {

  environment {
    IMAGE_TAG = "trainingad1/adservice"
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
  - name: dind
    image: docker:dind
    securityContext:
        runAsUser: 0
        runAsGroup: 0
        fsGroup: 0        
        privileged: true
     tty: true
    volumeMounts:
      - name: docker-config
        mountPath: /var/run
  volumes:
  - emptyDir: {} 
    name: docker-config
    
    
"""
}
  }
  stages {
    stage('kaniko') {
      steps {
        container('dind') {
          sh """
             docker build -t test .
            """
        }
      }
    }
  }
}
