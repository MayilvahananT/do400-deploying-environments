pipeline{
 agent {
   node {
     label 'maven'
   }
 }

 stages {
   stage('Test') {
     steps {
       sh './mvnw clean test'
     }
   }
   stage('Package') {
      steps {
        sh './mvnw clean package -DskipTests -Dquarkus.package.type=uber-jar'
        archiveArtifacts target/*.jar
      }
   }
 }
}
