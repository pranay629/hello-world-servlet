pipeline {
    agent any 
    tools { 
        maven 'maven' 
      
    }
stages { 
     
 stage('Preparation') { 
     steps {
// for display purposes

      // Get some code from a GitHub repository

      git 'https://github.com/pranay629/hello-world-servlet.git'

      // Get the Maven tool.
     
 // ** NOTE: This 'M3' Maven tool must be configured
 
     // **       in the global configuration.   
     }
   }

   stage('Build') {
       steps {
       // Run the maven build

      //if (isUnix()) {
         sh 'mvn -Dmaven.test.failure.ignore=true install'
      //} 
      //else {
      //   bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
       }
//}
   }
 
  stage('Results') {
      steps {
      junit '**/target/surefire-reports/TEST-*.xml'
      archiveArtifacts 'target/*.war'
      }
 }
 stage('Sonarqube') {
    environment {
        scannerHome = tool 'sonarqube'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        timeout(time: 10, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
    }
}
     stage('Artifact upload') {
      steps {
     nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/helloworld.war']], mavenCoordinate: [artifactId: 'hello-world-servlet-example', groupId: 'com.geekcap.vmturbo', packaging: 'war', version: '$BUILD_NUMBER']]]
      }
 }

     stage('Deploy war'){
      steps{
            sh label: '', script:'ansible-playbook deployed.yml'
            deploy adapters: [tomcat8(credentialsId: 'e9a4dc48-ecd1-4983-bc32-c04de10f4153', path: '', url: 'http://13.126.36.199:8080/manager/html')], contextPath: '/var/lib/jenkins/workspace/pipilene-script/target/*.war', war: 'war'
        }
    }
}
post {
        success {
            mail to:"bellamkonda11321@gmail.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Build success"
        }
        failure {
            mail to:"bellamkonda11321@gmail.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Build failed"
        }
    }       
}
