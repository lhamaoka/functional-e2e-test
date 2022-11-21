pipeline{

  agent {
    node {
        label 'pring-boot-app'
    }
  }

  stages {

    stage('Deploy to K8s') {

      steps{
        script {
          if(fileExists("configuracion")){
            sh 'rm -r configuracion'
          }
        }
        sh 'git clone https://github.com/lhamaoka/kubernetes-helm-docker-config.git configuracion --branch test-implementation'
        sh 'kubectl apply -f configuracion/kubernetes-deployment/standalone-chrome/manifest.yml -n default --kubeconfig=configuracion/kubernetes-config/config'
      }

    }

    stage('Run function testing E2E') {
      steps {
        sh 'mvn clean verify -Dwebdriver.remote.url=https://9f17-148-3-112-184.eu.ngrok.io/wd/hub -Dwebdriver.remote.driver=chrome -Dchrome.switches="--no-sandbox,--ignore-certificate-errors,--homepage=about:blank,--no-first-run,--headless"'
      }
    }

    stage('Generate Cucumber Report') {
      steps {
        sh 'mvn serenity:aggregate'

        publishHTML(target: [
            reportName : 'Serenity',
            reportDir:   'target/site/serenity',
            reportFiles: 'index.html',
            keepAll:     true,
            alwaysLinkToLastBuild: true,
            allowMissing: false
        ])

      }
    }

  }

  post {
    always {
        sh 'docker logout'
    }
  }

}