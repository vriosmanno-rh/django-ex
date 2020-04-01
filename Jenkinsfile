pipeline {
  agent any

  stages {
    stage('SonarQube Analysis') {
      agent any

      environment {
        SONAR_SCANNER_PATH="${HOME}/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube"
        SONAR_PROJECT_KEY="mytestproject"
        SONAR_PROJECT_NAME="mytestproject"
        SONAR_PROJECT_SETTING="."
        SONAR_SOURCES="."
      }

      steps {
        withSonarQubeEnv('SonarQube') {
          // sh 'pylint --load-plugins pylint-django ./project ./welcome -r n --msg-template="{path}:{line}: [{msg_id}({symbol}), {obj}] {msg}" > ./pylint-report'
          // sh '${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=${SONAR_TOKEN} -Dsonar.host.url=${SONAR_URL} -Dsonar.python.pylint.reportPath=./pylint-report'
          // sh "${HOME}/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube/bin/sonar-scanner -X -Dsonar.projectKey=mytestproject -Dproject.settings=. -Dsonar.projectName=mytestproject"

          sh "env"

          dir ("${HOME}") {
            sh "pwd && ls"
          }

          sh "${SONAR_SCANNER_PATH}/bin/sonar-scanner -X -Dsonar.projectKey=${env.SONAR_AUTH_TOKEN} -Dsonar.host.url=${env.SONAR_HOST_URL}"
        }
      }
    }
  }
}