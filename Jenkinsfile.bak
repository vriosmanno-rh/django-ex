/**
Set needed environment variables on master to be used down below
**/
node ('master') {
  env.OCP_API_SERVER = "${env.OPENSHIFT_API_URL}"
  env.OCP_TOKEN = readFile('/var/run/secrets/kubernetes.io/serviceaccount/token').trim()
  def defaultNamespace = readFile('/var/run/secrets/kubernetes.io/serviceaccount/namespace').trim()

  env.JENKINS_BASE_AGENT_ARGS = '${computer.jnlpmac} ${computer.name}'
  env.JENKINS_BASE_AGENT_NAME = 'jnlp'
  env.JENKINS_BASE_AGENT_IMAGE = "registry.redhat.io/openshift3/jenkins-slave-base-rhel7"

  env.MVN_PYTHON_AGENT_CONTAINER_MEMORY_SIZE = "1Gi"
  env.MVN_PYTHON_AGENT_IMAGE = "quay.io/vriosmanno/jenkins-slave-mvn-python"
  env.MVN_PYTHON_AGENT_LABEL = "mvn-python"
  env.MVN_PYTHON_AGENT_SVCACCT = "jenkins"
  env.MVN_PYTHON_AGENT_WORKING_DIR = "/tmp"
}

podTemplate(
  label: "${env.MVN_PYTHON_AGENT_LABEL}",
  cloud: 'openshift',
  serviceAccount: "${env.MVN_PYTHON_AGENT_SVCACCT}",
  containers: [
    containerTemplate(
      name: "${env.MVN_PYTHON_AGENT_LABEL}-base",
      image: "${env.MVN_PYTHON_AGENT_IMAGE}",
      alwaysPullImage: true,
      ttyEnabled: true,
      privileged: false,
      command: '',
      resourceRequestMemory: "${env.MVN_PYTHON_AGENT_CONTAINER_MEMORY_SIZE}"
    ),
    containerTemplate(
      name: "${env.JENKINS_BASE_AGENT_NAME}",
      image: "${env.JENKINS_BASE_AGENT_IMAGE}",
      args: "${env.JENKINS_BASE_AGENT_ARGS}"
    )
  ]
) {
  node("${env.MVN_PYTHON_AGENT_LABEL}") {
    container("${MVN_PYTHON_AGENT_LABEL}-base") {
      stage('SonarQube Analysis') {
        // This is the name of the SonarQube Scanner you defined under:
        // Jenkins --> Global Tool Configuration
        def scannerHome = tool name: 'sonar-scanner-tool',
          type: 'hudson.plugins.sonar.SonarRunnerInstallation';

        // These are value of the project you want to scan
        // Make sure to update these for your use case
        def SONAR_PROJECT_NAME="django-ex";
        def SONAR_PROJECT_KEY="django-ex";
        def SONAR_PROJECT_SETTING=".";
        def SONAR_SOURCES=".";
        def SONAR_SOURCE_ENCODING="UTF-8";
        def SONAR_PYTHON_PYLINT_REPORTPATH="pylint-report";
        
        try {
          sh "pylint --load-plugins pylint_django ./project ./welcome -r n --msg-template='{path}:{line}: [{msg_id}({symbol}), {obj}] {msg}' 2>&1 ${SONAR_PYTHON_PYLINT_REPORTPATH}"
        } catch (err) {
          echo err.getMessage()
          echo "Error detected, but will continue"
        }

        withSonarQubeEnv('sonar') {
          sh "${scannerHome}/bin/sonar-scanner -X \
            -Dsonar.projectName=${SONAR_PROJECT_NAME} \
            -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
            -Dsonar.host.url=${env.SONAR_HOST_URL} \
            -Dsonar.settings=${SONAR_PROJECT_SETTING} \
            -Dsonar.sources=${SONAR_SOURCES} \
            -Dsonar.SourceEncoding=${SONAR_SOURCE_ENCODING} \
            -Dsonar.python.pylint.reportPath=${SONAR_PYTHON_PYLINT_REPORTPATH}"
        }
      }

      stage('Results') {
        try {
          junit '**/target/surefire-reports/TEST-*.xml'
          archive 'target/*.jar'
        } catch (err) {
          echo "Reports were not pushed"
        }
      }
    }
  }
}