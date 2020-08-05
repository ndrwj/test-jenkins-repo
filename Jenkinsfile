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

  stage('Build') {
    // Build the image and push it to a staging repository
    repotag = inputConfig['dockerRepository'] + ":${BUILD_NUMBER}"
    docker.withRegistry(inputConfig['dockerRegistryUrl'], inputConfig['dockerCredentials']) {
      app = docker.build(repotag)
      app.push()
    }
  }
}
