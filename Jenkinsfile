def label = "mypod-${UUID.randomUUID().toString()}"
podTemplate(label: label, 
     containers: [
        containerTemplate(name: 'a-360', image: 'katson95/a-360:latest', ttyEnabled: true, command: 'cat'),
        containerTemplate(name: 'maven', image: 'maven:3.3.9-jdk-8-alpine', ttyEnabled: true, command: 'cat')
     ], 
     volumes: [
        hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
  ]) {
    node(label) {
         
        checkout scm
         
        def IMAGE_NAME = 'invent360/nexus-helm'
        def IMAGE_VERSION = 'latest'        
        
         stage('Build Project') {
            container('maven') {
                stage('Build and Project') {
                    sh 'mvn clean package'
                     sh 'docker build -t nexus-helm:latest .'
                    sh 'docker tag nexus-helm:latest invent360/nexus-helm:latest'
                }
            }
        }
         


        stage('Publish Image') {
            container('a-360'){  
                stage('Publish Image to Docker Registry') {
                  withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials-id', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUsername')]) {
                   sh "docker login -u ${env.dockerUsername} -p ${env.dockerPassword}"
                   sh "docker push ${IMAGE_NAME}:${IMAGE_VERSION}"
                }
              }
            }
        }
    }
}
