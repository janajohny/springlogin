pipeline {
environment {
imagename = "janajks/springlogin"
registryCredential = 'dockerhub'
dockerImage = ''
}
agent {
    node {
        label 'jslave'
        //customWorkspace '/some/other/path'
    }
}
stages {
stage('Cloning Git') {
steps {
git([url: 'https://github.com/janajohny/springlogin.git', branch: 'master', credentialsId: 'git'])
}
}
stage ('Build') {
        steps {
            sh 'mvn clean install -DskipTests'           
        }
    }
stage('Building image') {
steps{
script {
dockerImage = docker.build imagename
}
}
}
stage('Deploy Push') {
steps{
script {
docker.withRegistry( '', registryCredential ) {
dockerImage.push("$BUILD_NUMBER")
dockerImage.push('latest')
}
}
}
}
// Stopping Docker containers for cleaner Docker run
     stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=login -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=login -q | xargs -r docker container rm'
         }
       }
      
    stage('Docker Run') {
     steps{
         script {
                sh 'docker run -d -p 8080:8080 --network login-mysql --rm --name login ${imagename}:${BUILD_NUMBER}'
            }
      }
    }
}
}
