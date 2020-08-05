stage('Configure') {
    abort = false
    inputConfig = input id: 'InputConfig', message: 'Credentials for pipeline', parameters: [string(defaultValue: 'https://index.docker.io/v1/', description: 'Docker registry URL', name: 'dockerRegistryUrl', trim: true), string(defaultValue: '', description: 'Name of the docker repository', name: 'dockerRepository', trim: true), credentials(credentialType: 'com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl', defaultValue: '', description: 'Credentials for connecting to the docker registry', name: 'dockerCredentials', required: true)]

    for (config in inputConfig) {
        if (null == config.value || config.value.length() <= 0) {
          echo "${config.key} cannot be left blank"
          abort = true
        }
    }

    if (abort) {
        currentBuild.result = 'ABORTED'
        error('Aborting build due to invalid input')
    }
}  

node {
  def app
  def Dockerfile
  def repotag
  def latesttag

  try {
  stage('Checkout') {
      // Clone the git repository
      checkout scm
      def path = sh returnStdout: true, script: "pwd"
      path = path.trim()
      dockerfile = path + "/Dockerfile"
    }

  stage('Build') {
    // Build the image and push it to a staging repository
    repotag = inputConfig['dockerRepository'] + ":${BUILD_NUMBER}"
    latesttag = inputConfig['dockerRepository'] + ":latest"
    docker.withRegistry(inputConfig['dockerRegistryUrl'], inputConfig['dockerCredentials']) {
      app = docker.build(repotag)
      app.push()
      app = docker.build(latesttag)
      app.push()
    }
  }
}  finally {
    stage('Cleanup') {
      // Delete the docker image and clean up any allotted resources
      sh script: "docker rmi " + repotag
      sh script: "docker rmi " + latesttag
      }
    }
}
