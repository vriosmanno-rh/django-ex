pipeline {
  agent any
  stages {
    stage('SonarQube Analysis') {
      agent { label 'SonarQube' }
      withSonarQubeEnv() {
        sh 'pylint --load-plugins pylint-django ./project ./welcome -r n --msg-template="{path}:{line}: [{msg_id}({symbol}), {obj}] {msg}" > ./pylint-report'
        sh '${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=${SONAR_TOKEN} -Dsonar.host.url=${SONAR_URL} -Dsonar.python.pylint.reportPath=./pylint-report'
      }
    }
  }
}