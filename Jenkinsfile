def checkResult(def message){
     if(currentBuild.result == 'SUCCESS' || currentBuild.result == null){
     }else {
       error "FAIL: " + message + " " + currentBuild.result
     }
}

def deploy(){

}

node {
   def mvnHome

   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/wletmp5/spring-pcf-demo'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.
      mvnHome = tool 'M3'
      checkResult("Unable to download project from the repo")
   }
   stage('Build') {
      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
      }
      checkResult("Unable to build the project")
   }

   stage('Publish Reports') {
      parallel(

          "Unit Tests": {
              junit '**/target/surefire-reports/TEST-*.xml'
              archiveArtifacts 'target/*.jar'
              checkResult("There are test failures")
          },

          "SonarQube analysis": {
              if (isUnix()) {
                   sh "'${mvnHome}/bin/mvn' sonar:sonar"
              } else {
                   bat(/"${mvnHome}\bin\mvn" sonar:sonar/)
              }
              checkResult("There are SonarQube test failures")
          }
      )


   }
   stage('Deploy:Dev') {

      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore cf:push"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore cf:push/)
      }
      checkResult("Unable to deploy to Development environment")
   }
}

timeout(time:5, unit:'DAYS') {
    input message:'Approve Production deployment?', submitter: 'wletmp5'
}

node{
    mvnHome = tool 'M3'
    stage('Deploy:Prod'){

     if (isUnix()) {
        sh "'${mvnHome}/bin/mvn' -Pproduction cf:push"
        } else {
           bat(/"${mvnHome}\bin\mvn" -Pproduction cf:push/)
        }
      checkResult("Unable to deploy to Production environment")
   }
}