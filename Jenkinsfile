pipeline 
{
    agent any
    environment {
        DOCKER_IMAGE_NAME = "eminturan/denemes:latest"
    }
    tools 
    {
        maven 'M3'
    }
    stages 
    {
        stage('Build Jar')
        {
            steps 
            {
                sh '''
                 mvn clean package
                 cd target
                 cp rest-service-0.0.1-SNAPSHOT.jar rest-service.jar 
                '''
                stash includes: 'target/*.jar', name: 'targetfiles'
            }
        }
        stage('Build Docker Image') 
        {
            steps 
            {
                script 
                {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') 
                    {
                        //app = docker.build(DOCKER_IMAGE_NAME)    
                        //app.push()
                        echo 'Docker Login xXx'
                    }
                }
            }
        }
        stage('Push Docker Image') 
        {
            steps 
            {
                script 
                {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.push()
                }
            }
        }
        stage('Deploy Openshift')
        {
            steps 
            {
                sh 'oc login -u admin -p admin https://192.168.99.100:8443 --insecure-skip-tls-verify=true'
                sh 'oc project jtop'
                sh 'oc delete route denemes'
                sh 'oc delete service denemes'
                sh 'oc delete dc denemes'
                sh 'oc new-app eminturan/denemes:latest --name=denemes'
                sh 'oc expose service denemes'
            }
        }
    }
}
