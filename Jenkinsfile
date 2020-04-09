pipeline {
  agent { node { label 'master' } }

  environment {
    MVN_PYTHON_AGENT_CONTAINER_MEMORY_SIZE = "1Gi"
    MVN_PYTHON_AGENT_IMAGE = "quay.io/vriosmanno/jenkins-slave-mvn-python"
    MVN_PYTHON_AGENT_LABEL = "mvn-python"
    MVN_PYTHON_AGENT_WORKING_DIR = "/tmp"
    MVN_PYTHON_AGENT_SVCACCT = "jenkins"

    SONAR_PROJECT_NAME="-Dsonar.projectName=django-ex"
    SONAR_PROJECT_KEY="-Dsonar.projectKey=django-ex"
    SONAR_HOST_URL="-Dsonar.host.url=${env.SONAR_HOST_URL}"
    SONAR_PROJECT_SETTING="-Dsonar.settings=."
    SONAR_SOURCES="-Dsonar.sources=."
    SONAR_SOURCE_ENCODING="-Dsonar.SourceEncoding=UTF-8"
    SONAR_PYTHON_PYLINT_REPORTPATH="-Dsonar.python.pylint.reportPath=pylint-report"
  }

  stages {
    stage('SonarQube Analysis') {
      agent { label "${env.MVN_PYTHON_AGENT_LABEL}" }

      environment {
        scannerHome = tool 'SonarQube'

        SONAR_SCANNER_PATH="/tmp/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube"
      }

      steps {
        withSonarQubeEnv('SonarQube') {
          // sh 'ls -la && pwd'
          sh 'sleep 2m'
          sh 'pylint --load-plugins pylint_django ./project ./welcome -r n --msg-template="{path}:{line}: [{msg_id}({symbol}), {obj}] {msg}" 2>&1 pylint-report'
          sh 'echo ${scannerHome}'
          sh "echo ${scannerHome}"
          sh '${scannerHome}/bin/sonar-scanner -X ${env.SONAR_PROJECT_NAME} ${env.SONAR_PROJECT_KEY} ${env.SONAR_HOST_URL} ${env.SONAR_PROJECT_SETTING} ${env.SONAR_SOURCES} ${env.SONAR_SOURCE_ENCODING} ${env.SONAR_PYTHON_PYLINT_REPORTPATH}'

          // sh "${SONAR_SCANNER_PATH}/bin/sonar-scanner -X -Dsonar.projectKey=${SONAR_PROJECT_KEY} -Dsonar.host.url=${env.SONAR_HOST_URL} -Dsonar.projectName=${SONAR_PROJECT_NAME} -Dsonar.settings=${SONAR_PROJECT_SETTING} -Dsonar.sources=${SONAR_SOURCES} -Dsonar.SourceEncoding=UTF-8"
        }
      }
    }
  }
}