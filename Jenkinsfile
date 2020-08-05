stage('Configure') {
    abort = false
    inputConfig = input id: 'InputConfig', message: 'Credentials for pipeline', parameters: [string(defaultValue: '', description: 'Name of the docker repository', name: 'dockerRepository', trim: true)]

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

  stage('Checkout') {
      // Clone the git repository
      checkout scm
      def path = sh returnStdout: true, script: "pwd"
      path = path.trim()
      dockerfile = path + "/Dockerfile"
      anchorefile = path + "/anchore_images"
    }

  stage('Build') {
    // Build the image and push it to a staging repository
    repotag = inputConfig['dockerRepository'] + ":${BUILD_NUMBER}"
    docker.build(repotag)
  }
}
