pipeline {
  agent any

  stages {
    stage('SonarQube Analysis') {
      agent any

      environment {
        SONAR_SCANNER_PATH="${HOME}/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube"
        SONAR_PROJECT_KEY="rhel"
        SONAR_PROJECT_NAME="rhel"
        SONAR_PROJECT_SETTING="."
        SONAR_SOURCES="."
      }

      steps {
        withSonarQubeEnv('SonarQube') {
          // sh 'pylint --load-plugins pylint-django ./project ./welcome -r n --msg-template="{path}:{line}: [{msg_id}({symbol}), {obj}] {msg}" > ./pylint-report'
          // sh '${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=${SONAR_TOKEN} -Dsonar.host.url=${SONAR_URL} -Dsonar.python.pylint.reportPath=./pylint-report'
          // sh "${SONAR_SCANNER_PATH}/bin/sonar-scanner -Dsonar.projectKey=${SONAR_PROJECT_KEY} -Dsonar.settings=${SONAR_PROJECT_SETTING} -Dsonar.projectName=${SONAR_PROJECT_NAME} -Dsonar.sources=${SONAR_SOURCES} -Dsonar.SourceEncoding=UTF-8"
          // sh "${HOME}/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube/bin/sonar-scanner -X -Dsonar.projectKey=mytestproject -Dproject.settings=. -Dsonar.projectName=mytestproject"

          sh "env"
          sh "${SONAR_SCANNER_PATH}/bin/sonar-scanner -X -Dsonar.projectKey=${SONAR_PROJECT_KEY} -Dsonar.host.url=${env.SONAR_HOST_URL} -Dsonar.projectName=${SONAR_PROJECT_NAME} -Dsonar.settings=${SONAR_PROJECT_SETTING} -Dsonar.sources=${SONAR_SOURCES} -Dsonar.SourceEncoding=UTF-8"
        }
      }
    }
  }
}