node {
  def app

  stage('maven build') {
    def maven = docker.image('maven:3.8.2-adoptopenjdk-11')
    maven.inside {
      sh 'mvn clean install'
    }
  }
  stage('package') {
    app = docker.build('localhost:5000/spring-petclinic')
  }

  // stage('push image') {
  //   /* Finally, we'll push the image with two tags:
  //        * First, the incremental build number from Jenkins
  //        * Second, the 'latest' tag.
  //        * Pushing multiple tags is cheap, as all the layers are reused. */
  //   docker.withRegistry('http://localhost:5000') {
  //     app.push("${env.BUILD_NUMBER}")
  //     app.push("latest")
  //   }
  // }
}
