node {
    checkout scm
    def testImage = docker.build("petclinic-from-pipeline")

    testImage.inside {
        sh 'ls -alrt /bin'
    }
}
