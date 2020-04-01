pipeline {
  agent any

  stages {
    stage('SonarQube Analysis') {
      agent any

      steps {
        withSonarQubeEnv('SonarQube') {
          // sh 'pylint --load-plugins pylint-django ./project ./welcome -r n --msg-template="{path}:{line}: [{msg_id}({symbol}), {obj}] {msg}" > ./pylint-report'
          // sh '${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=${SONAR_TOKEN} -Dsonar.host.url=${SONAR_URL} -Dsonar.python.pylint.reportPath=./pylint-report'
          // sh "${HOME}/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube/bin/sonar-scanner -X -Dsonar.projectKey=mytestproject -Dproject.settings=. -Dsonar.projectName=mytestproject"

          sh "echo env"
          sh "sonar-scanner -X -Dsonar.projectKey=${env.SONAR_TOKEN} -Dsonar.host.url=${env.SONAR_URL}"
        }
      }
    }
  }
}