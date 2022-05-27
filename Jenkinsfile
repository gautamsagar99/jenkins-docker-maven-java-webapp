pipeline{
    
    //this agent is like global all stages runs on this agent
    agent{
        
        label "linuxbuildnode"
    }
    
    
    stages{
        stage('SCM'){
            steps{
            // echo "i am SCM stage...."
            // git 'https://github.com/vimallinuxworld13/jenkins-docker-maven-java-webapp.git'
            git "https://github.com/gautamsagar99/jenkins-docker-maven-java-webapp.git"
            }
        }
        stage('Build by Maven'){
            steps{
            // echo "i am from Maven stage...."
            sh 'mvn clean package'
            
            }
        }
         stage('Build Docker Own Image'){
            steps{
            
            sh "sudo docker build -t gautamsagar99/javaweb:${BUILD_TAG} ."
            
            }
        }
         stage('Push Image to Docker HUB'){
            steps{
                
                withCredentials([string(credentialsId: 'DOCKER_HUB_PWD', variable: 'DOCKER_HUB_PASS_CODE')]) {
                // some block
                sh "sudo docker login -u gautamsagar99 -p $DOCKER_HUB_PASS_CODE"
                }
            
            
            sh "sudo docker push gautamsagar99/javaweb:${BUILD_TAG}"
            
            }
        }
         stage('Deploy webAPP in DEV Env'){
            steps{
            
            sh "sudo docker rm -f myjavaapp"
            sh "sudo docker run -d -p 8080:8080 --name myjavaapp gautamsagar99/javaweb:${BUILD_TAG}"
            
            }
        }
        stage('Deploy webAPP in QA/ Test Env'){
            steps{
                
                sshagent(['QA_ENV_SSH_CRED']) {
                // some block
                sh "ssh -o StrictHostKeyChecking=no ec2-user@13.233.138.193 sudo docker rm -f myjavaapp"
                sh "ssh ec2-user@13.233.138.193 sudo docker run -d -p 8080:8080 --name myjavaapp gautamsagar99/javaweb:${BUILD_TAG}"
                }
            
            
            
            }
        }
        
          stage('approval'){
            steps{
                
                //input(message: "Release to prod ?")
                script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }
            
                
            }
            
            }
        }
        
        
        
         stage('Deploy webAPP in Prod Env'){
            steps{
                
                sshagent(['QA_ENV_SSH_CRED']) {
                // some block
                sh "ssh -o StrictHostKeyChecking=no ec2-user@13.233.129.71 sudo docker rm -f myjavaapp"
                sh "ssh ec2-user@13.233.129.71 sudo docker run -d -p 8080:8080 --name myjavaapp gautamsagar99/javaweb:${BUILD_TAG}"
                }
            
            
            
            }
        }
        
    }
    
}