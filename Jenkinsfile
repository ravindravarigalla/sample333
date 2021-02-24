pipeline {

  environment {
    IMAGE_TAG = "adira-ent-registry-registry-vpc.ap-southeast-5.cr.aliyuncs.com/sales/test:latest"
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
    image: trainingad1/helm3
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
          name: regcred1
          items:
            - key: .dockerconfigjson
              path: config.json
    
"""
}
  }
  stages {
    stage('kaniko') {
      steps {
        container('kaniko') {
          sh """
            /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination="${IMAGE_TAG}"
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
            #kubectl create secret docker-registry regcred1 --docker-server=adira-ent-registry-registry-vpc.ap-southeast-5.cr.aliyuncs.com --docker-username=itadira@adira --docker-password=4dminR3g2021 --docker-email=af.it.appl@adira.co.id -n jenkins
             """
         }
        }
      }
    }
  }
}
