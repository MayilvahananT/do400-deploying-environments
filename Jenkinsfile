pipeline{
  agent any
 
 environment {
    RHT_OCP4_DEV_USER = 'nezkmp'
    DEPLOYMENT_CONFIG_STAGE = 'shopping-cart-stage'
    DEPLOYMENT_CONFIG_PRODUCTION = 'shopping-cart-production'
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
   stage('Deploy - Staging Env') {
      environment {
        APP_NAMESPACE = "${RHT_OCP4_DEV_USER}-${DEPLOYMENT_CONFIG_STAGE}"
      }
      steps{
        sh "oc rollout latest dc/${DEPLOYMENT_CONFIG_STAGE} -n ${APP_NAMESPACE}"
      }
   }
   stage('Deploy - Production Env') {
      environment {
        APP_NAMESPACE = "${RHT_OCP4_DEV_USER}-${DEPLOYMENT_CONFIG_PRODUCTION}"
      }
      input { message 'Deploy to Production Environment?' }
      steps{
        sh "oc rollout latest dc/${DEPLOYMENT_CONFIG_PRODUCTION} -n ${APP_NAMESPACE}"
      }
   }
 }
}
