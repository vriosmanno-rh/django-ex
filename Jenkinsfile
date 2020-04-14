pipeline {
  agent {
    node {
      label 'master'
    }
  }

  environment {
    JENKINS_BASE_AGENT_ARGS = '${computer.jnlpmac} ${computer.name}'
    JENKINS_BASE_AGENT_IMAGE = "registry.redhat.io/openshift3/jenkins-slave-base-rhel7"
    JENKINS_BASE_AGENT_NAME = 'jnlp'
    MVN_PYTHON_AGENT_CONTAINER_MEMORY_SIZE = "1Gi"
    MVN_PYTHON_AGENT_IMAGE = "quay.io/vriosmanno/jenkins-slave-mvn-python"
    MVN_PYTHON_AGENT_LABEL = "mvn-python"
    MVN_PYTHON_AGENT_WORKING_DIR = "/tmp"
    MVN_PYTHON_AGENT_SVCACCT = "jenkins"
  }

  stages {
    stage('SonarQube Analysis') {
      agent {
        kubernetes {
          cloud "openshift"
          label "${env.MVN_PYTHON_AGENT_LABEL}"
          yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins: "slave"
    jenkins/mvn-python: "true"
  name: "${env.MVN_PYTHON_AGENT_LABEL}"
spec:
  containers:
  - name: "${env.JENKINS_BASE_AGENT_NAME}"
    args:
    - "${env.JENKINS_BASE_AGENT_ARGS}"
    image: quay.io/vriosmanno/jenkins-slave-mvn-python
    imagePullPolicy: Always
    resources:
      requests: {
        memory: "${env.MVN_PYTHON_AGENT_CONTAINER_MEMORY_SIZE}"
      }
    securityContext:
      privileged: false
    tty: false
    workingDir: "/tmp"
  restartPolicy: "Never"
"""
        }
      }

      environment {
        scannerHome = tool 'sonar-scanner-tool'

        SONAR_PROJECT_NAME="django-ex"
        SONAR_PROJECT_KEY="django-ex"
        SONAR_PROJECT_SETTING="."
        SONAR_SOURCES="."
        SONAR_SOURCE_ENCODING="UTF-8"
        SONAR_PYTHON_PYLINT_REPORTPATH="pylint-report"
      }

      steps {
        script {
          try {
            sh "pylint --load-plugins pylint_django ./project ./welcome -r n --msg-template='{path}:{line}: [{msg_id}({symbol}), {obj}] {msg}' 2>&1 ${SONAR_PYTHON_PYLINT_REPORTPATH}"
          } catch (err) {
            echo err.getMessage()
            echo "Error detected, but will continue"
          }
        }

        withSonarQubeEnv('sonar') {
          // sh 'ls -la && pwd'
          sh "${scannerHome}/bin/sonar-scanner -X \
            -Dsonar.projectName=${SONAR_PROJECT_NAME} \
            -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
            -Dsonar.host.url=${env.SONAR_HOST_URL} \
            -Dsonar.settings=${SONAR_PROJECT_SETTING} \
            -Dsonar.sources=${SONAR_SOURCES} \
            -Dsonar.SourceEncoding=${SONAR_SOURCE_ENCODING} \
            -Dsonar.python.pylint.reportPath=${SONAR_PYTHON_PYLINT_REPORTPATH}"

          // sh "${SONAR_SCANNER_PATH}/bin/sonar-scanner -X -Dsonar.projectKey=${SONAR_PROJECT_KEY} -Dsonar.host.url=${env.SONAR_HOST_URL} -Dsonar.projectName=${SONAR_PROJECT_NAME} -Dsonar.settings=${SONAR_PROJECT_SETTING} -Dsonar.sources=${SONAR_SOURCES} -Dsonar.SourceEncoding=UTF-8"
        }
      }
    }
  }
}