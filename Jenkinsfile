pipeline {
  agent { node { label 'master' } }

  environment {
    MVN_PYTHON_AGENT_CONTAINER_MEMORY_SIZE = "1Gi"
    MVN_PYTHON_AGENT_IMAGE = "quay.io/vriosmanno/jenkins-slave-mvn-python"
    MVN_PYTHON_AGENT_LABEL = "mvn-python"
    MVN_PYTHON_AGENT_WORKING_DIR = "/tmp"
    MVN_PYTHON_AGENT_SVCACCT = "jenkins"
  }

  stages {
    stage('SonarQube Analysis') {
      agent { label "${env.MVN_PYTHON_AGENT_LABEL}" }

      environment {
        SONAR_SCANNER_PATH="${HOME}/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube"
        SONAR_PROJECT_KEY="django-ex"
        SONAR_PROJECT_NAME="django-ex"
        SONAR_PROJECT_SETTING="."
        SONAR_SOURCES="."
        scannerHome = tool name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
      }

      steps {
        withSonarQubeEnv('SonarQube') {
          // sh 'ls -la && pwd'
          sh 'pylint --load-plugins pylint_django ./project ./welcome -r n --msg-template="{path}:{line}: [{msg_id}({symbol}), {obj}] {msg}" > ./pylint-report'
          sh '${SONAR_SCANNER_PATH}/bin/sonar-scanner -X -Dsonar.projectKey=${SONAR_PROJECT_KEY} -Dsonar.host.url=${env.SONAR_HOST_URL} -Dsonar.projectName=${SONAR_PROJECT_NAME} -Dsonar.settings=${SONAR_PROJECT_SETTING} -Dsonar.sources=${SONAR_SOURCES} -Dsonar.SourceEncoding=UTF-8 -Dsonar.python.pylint.reportPath=./pylint-report'

          // sh "${SONAR_SCANNER_PATH}/bin/sonar-scanner -X -Dsonar.projectKey=${SONAR_PROJECT_KEY} -Dsonar.host.url=${env.SONAR_HOST_URL} -Dsonar.projectName=${SONAR_PROJECT_NAME} -Dsonar.settings=${SONAR_PROJECT_SETTING} -Dsonar.sources=${SONAR_SOURCES} -Dsonar.SourceEncoding=UTF-8"
        }
      }
    }
  }
}