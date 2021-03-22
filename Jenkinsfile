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
  annotations:
    container.apparmor.security.beta.kubernetes.io/dind: unconfined
    container.seccomp.security.alpha.kubernetes.io/dind: unconfined
  labels:
    some-label: label1
spec:
  serviceAccountName: example
  securityContext:
    runAsUser: 10000
    runAsGroup: 10000
  containers: 
    - name: jnlp
      image: 'jenkins/jnlp-slave:4.3-4-alpine'
      args: ['\$(JENKINS_SECRET)', '\$(JENKINS_NAME)']
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
    - name: example-test
      image: pranavbhatia/example-test:0.1
      securityContext:
          runAsUser: 0
          runAsGroup: 0
          fsGroup: 0
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
    stage('helm') {
      steps {
        container('helm') {
          withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
          sh """
            kubectl config set-context 265265976143684875-cc6efa8a194934959bf8b4aa3891e9ae8 --cluster=kubernetes --user=265265976143684875
            kubectl config use-context 265265976143684875-cc6efa8a194934959bf8b4aa3891e9ae8
            kubectl get nodes
            #kubectl create deployment test --image adira-ent-registry-registry-vpc.ap-southeast-5.cr.aliyuncs.com/sales/test:latest -n default
            kubectl create secret docker-registry regcred1 --docker-server=adira-ent-registry-registry-vpc.ap-southeast-5.cr.aliyuncs.com --docker-username=itadira@adira --docker-password=4dminR3g2021 --docker-email=af.it.appl@adira.co.id -n partner
             """
         }
        }
      }
    }
  }
}
