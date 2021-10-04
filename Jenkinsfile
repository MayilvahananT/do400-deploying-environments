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
        archiveArtifacts 'target/*.jar'
      }
   }
   stage('Build Image') {
     environment { MY_QUAY = credentials('DO400_QUAY_USER') }
     steps {
        sh './mvnw quarkus:add-extension -Dextensions="kubernetes,container-image-jib"'
        sh '''
            ./mvnw clean package -DskipTests \
            -Dquarkus.container-image.build=true \
            -Dquarkus.container-image.registry=quay.io \
            -Dquarkus.container-image.group=$MY_QUAY_USR \
            -Dquarkus.container-image.name=do400-deploying-environments \
            -Dquarkus.container-image.username=$MY_QUAY_USR \
            -Dquarkus.container-image.password="$MY_QUAY_PSW" \
            -Dquarkus.container-image.push=true
        '''
     }
   }
 }
}
