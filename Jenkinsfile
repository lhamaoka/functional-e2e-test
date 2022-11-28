pipeline{

  agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: shell
    image: lhamaoka/nodo-java-practica-final:1.0
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-socket-volume
    securityContext:
      privileged: true
  volumes:
  - name: docker-socket-volume
    hostPath:
      path: /var/run/docker.sock
      type: Socket
    command:
    - sleep
    args:
    - infinity
'''
            defaultContainer 'shell'
        }
    }

  stages {

    stage('Deploy to K8s') {

      steps{
        script {
          if(fileExists("launcher")){
            sh 'rm -r launcher'
          }
        }
        sh 'git clone https://github.com/lhamaoka/manifest_launcher.git launcher'
        sh 'kubectl apply -f launcher/deploys/standalone-chrome/manifest.yaml -n default --kubeconfig=launcher/config/config'
      }

    }
  }

  post {
    always {
        sh 'docker logout'
    }
  }

}