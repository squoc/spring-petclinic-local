pipeline {
    agent any

    environment {
        JAVA_TOOLS_OPTION = "-Duser.home=/var/maven"

        IMAGE_REGISTRY = 'localhost:5000'
        IMAGE_NAME = 'spring-petclinic'
        IMAGE_VERSION = sh(script: "echo ${currentBuild.duration} | md5sum | cut -d' ' -f1", returnStdout: true).trim()
        // IMAGE_VERSION = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
    }

    parameters {
        string defaultValue: "${currentBuild.id}", description: 'build number', name: 'BUILD_NUMBER'
    }

    stages {
        stage('checkout') {
            steps {
                git url: 'http://localhost:9999/quoc/spring-petclinic-gitea.git', branch: 'main'

            }
        }

        stage('build maven') {
            agent {
                docker {
                    image 'maven:3.8.6-openjdk-11'
                    args "-v ${JENKINS_HOME}/maven:/var/maven/.m2 -e MAVEN_CONFIG=/var/maven/.m2"
                    reuseNode true
                }
            }
            steps {
                sh 'mvn -version'
                sh 'mvn -Dcheckstyle.skip -Dmaven.test.skip=true clean install'
            }
        }

        stage('docker build') {
            steps {
                sh """
                    ls -lah
                    docker build -t ${IMAGE_NAME}:${IMAGE_VERSION} .
                """
            }
        }

        stage('docker tag and pack') {
            environment {
                DES = "${IMAGE_REGISTRY}/${IMAGE_NAME}:${params.BUILD_NUMBER}"
            }
            steps {
                sh """
                    docker tag ${IMAGE_NAME}:${IMAGE_VERSION} ${DES}
                    docker images | head -5
                    docker push $DES
                """
            }
        }
    }


    // post {
    //     always {
    //         cleanWs()
    //     }
    // }
}
