pipeline{

  agent {
    node {
        label 'nodo-java'
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
        sh 'git clone https://github.com/lhamaoka/manifest_launcher.git launcher --branch test-implementation'
        sh 'kubectl apply -f launcher/deploys/standalone-chrome/manifest.yaml -n default --kubeconfig=launcher/config/config'
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