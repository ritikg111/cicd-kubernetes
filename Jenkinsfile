pipeline{

agent any

tools{
maven 'MAVEN3'

}
environment{
registry = 'ritikgoel/vprofile-app'
registrycreds = 'dockerhubcreds'

}

stages{
stage('Build'){
steps{
sh 'mvn clean install'
}
}
stage('Test'){
steps{
sh 'mvn test'
}
}
stage('Verify'){
steps{
sh 'mvn verify'
}
}
stage('Checkstyle Analysis'){
steps{
sh 'mvn checkstyle:checkstyle'
}
}
stage('Sonarqube Code Analysis'){

environment{
scanner = tool 'sonar4.7'
}
steps{
withSonarQubeEnv('sonar-pro'){
sh '''${scanner}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                           -Dsonar.projectName=vprofile-repo \
                           -Dsonar.projectVersion=1.0 \
                           -Dsonar.sources=src/ \
                           -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                           -Dsonar.junit.reportsPath=target/surefire-reports/ \
                           -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                           -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
}
timeout(time: 10, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
}
}
}
stage('Docker Build'){
steps{
  script{
dockerImage = docker.build("${registry}:v${BUILD_NUMBER}")
  }
}
}

stage('Push Image to Dockerhub'){
steps{
  script{
docker.withRegistry("", "${registrycreds}")
dockerImage.push('latest')
}
}
}

}
}
