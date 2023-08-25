pipeline {
  agent any
  tools {
    maven 'maven-' // Replace with actual Maven tool name/version
    jdk 'JDK' // Replace with actual JDK tool name/version
  }

  stages {
    stage('Build maven & SonarQube analysis') {
      steps {
        sh 'pwd'
        withSonarQubeEnv('MySonarQube') {
          sh 'mvn clean package sonar:sonar'
        }
      }
    }

    stage('Copy Artifact') {
      steps {
        sh 'pwd'
        sh 'ls -la'
       // sh 'mv target/*.jar java-web-app.jar'
        sh 'cp -r java-web-app.jar docker'
      }
    }

   // stage('Deploy to Artifactory') {
   //   steps {
     //   script {
      //    def server = Artifactory.server('1') // Configure the server name in Jenkins
       //   def uploadSpec = """{
       //     "files": [{
         //     "pattern": "java-web-app.jar",
        //      "target": "my-local-repo/"
        //    }]
      //    }"""
      //    server.upload(uploadSpec)
      //  }
   //   }
  //  }

    stage('Build docker image') {
      steps {
        script {
          def customImage = docker.build('alaouiyoussef/myrepo', "./docker")
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            customImage.push("${env.BUILD_NUMBER}")
          }
        }
      }
    }

     stage('Deploy to K8s') {
      steps{
        script {
          sh "sed -i 's,java-web-app,alaouiyoussef/myrepo:$BUILD_NUMBER,' deployment.yaml"
          sh "cat deployment.yaml"
          sh "kubectl  get pods"
          sh "kubectl  deployment.yaml"
        }
      }
    }
  }
}
