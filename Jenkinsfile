node {
  def appName = 'spring-petclinic'
  def img

  stage('checkout') {
    git url: 'http://localhost:9999/quoc/spring-petclinic-gitea.git', branch: 'main'
  }

  stage('maven build') {
    def maven = docker.image('maven:3.8.6-openjdk-11')

    maven.inside("-v $HOME/.m2:/root/.m2") {
      sh 'mvn -DskipTests=true -Dcheckstyle.skip clean install'
    }
  }
  stage('pack image') {
    img = docker.build("localhost:5000/${appName}")
  }

   stage('push image') {
     docker.withRegistry('http://localhost:5000') {
        img.push()
     }
   }

   stage('run') {
        docker.withRegistry('http://localhost:5000') {
            img.pull()
        }

        sh '''
            #!/bin/bash
            set +e
            pet=`docker ps | grep petclinic | cut -d' ' -f1`
            if [ -n "$pet" ]
            then
                docker stop petclinic
            fi
        '''

        img.run("--rm --name petclinic -p 4444:8080")
   }
}
