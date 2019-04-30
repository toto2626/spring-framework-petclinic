pipeline {
    agent any
    tools {
        maven "maven 3.6"
    }
    options {
        parallelsAlwaysFailFast()
    }
     environment {
        NEXUS_ARTIFACT_VERSION= "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Non-Parallel Stage') {
            steps {
                echo 'This stage will be executed first.'
            }
        }
        stage('Parallel Stage') {
            parallel {
                   stage('Checkstyle') {
                        steps{
                            // Run the maven build with checkstyle
                            sh "mvn clean package checkstyle:checkstyle"
                         }
                     }
                    stage('Sonarqube') {
                        steps {
                            withSonarQubeEnv('SonarQube') {
                            //sh "mvn  clean package sonar:sonar -Dsonar.host_url=$SONAR_HOST_URL "
                             echo "Attention la connexion de sonar ne marche pas"
                            }
                         }
                    }
            }
        }
        stage('Publish in Nexus') {
            steps {
                nexusPublisher nexusInstanceId: 'Nexus',
                nexusRepositoryId: 'releases',
                packages: [[$class: 'MavenPackage',
                mavenAssetList: [[classifier: '', extension: '', filePath: 'target/petclinic.war']], mavenCoordinate: [artifactId: 'spring-framework-petclinic', groupId: 'org.springframework.samples', packaging: 'war', version: NEXUS_ARTIFACT_VERSION]]]
            }
        }

        stage('Build image') {
            steps{
                script{
                    def customImage = docker.build("petclinic-project")
                }
            }
        }
        stage('Run Test image') {
            steps{
                def out = sh script: 'CONTAINER_NAME="petclinic-test"
                                      OLD="$(docker ps --all --quiet --filter=name="$CONTAINER_NAME")"
                                      if [ -n "$OLD" ]; then
                                             docker stop $OLD && docker rm $OLD
                                      fi', returnStdout: true
                sh 'docker run -d --name petclinic-test -p 8090:8080 petclinic-project'
            }
        }



    }
}
