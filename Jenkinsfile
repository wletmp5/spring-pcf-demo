def checkResult(def message){
     if(currentBuild.result == 'SUCCESS'){
     }else {
       error "FAIL: " + message + " " + currentBuild.result
     }
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
      parallel{

          stage('Unit Tests'){
              junit '**/target/surefire-reports/TEST-*.xml'
              archiveArtifacts 'target/*.jar'
              checkResult("There are test failures")
          }

          stage('SonarQube analysis'){
              if (isUnix()) {
                   sh "'${mvnHome}/bin/mvn' sonar:sonar"
              } else {
                   bat(/"${mvnHome}\bin\mvn" sonar:sonar/)
              }
              checkResult("There are SonarQube test failures")
          }

      }
   }
   stage('Deploy:Dev') {

      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore cf:push"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore cf:push/)
      }
   }
}

timeout(time:5, unit:'DAYS') {
    input message:'Approve Production deployment?', submitter: 'wletmp5'
}

node{
    stage('Deploy:Prod') {
      // Run the maven build

   }
}