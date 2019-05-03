pipeline {
    agent any

   parameters {
        string(defaultValue: "TEST", description: 'What environment?', name: 'userFlag')
    }

    stages {
        stage("userFlag") {
            steps {
                echo "flag: ${params.userFlag}"
            }
        }
    }
}
