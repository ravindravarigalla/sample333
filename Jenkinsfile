pipeline {

  environment {
    IMAGE_TAG = "adira-ent-registry-registry-vpc.ap-southeast-5.cr.aliyuncs.com/sales/test:latest"
    JENKINS_CRED = "${PROJECT}"
  }

   agent {
        kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  name: example-pb
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
      - name: var-run
        mountPath: /var/run
  volumes:
  - emptyDir: {}
    name: var-run
"""
        }
    }
  stages {
    stage('dind') {
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
