node {
   def mvnHome
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/wletmp5/spring-pcf-demo'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.
      mvnHome = tool 'M3'
   }
   stage('Build') {
      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
      }
   }
   stage('Unit Tests') {
      junit '**/target/surefire-reports/TEST-*.xml'
      archiveArtifacts 'target/*.jar'
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