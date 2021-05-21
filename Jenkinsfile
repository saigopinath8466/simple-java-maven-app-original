pipeline {
    agent {
        label 'agent1'
    }
    tools {
         maven 'maven362'
    }
    environment {
         target_user = "ubuntu"
         target_server = "172.31.28.150"
         EMAIL_TO = "gopinathsaramalla98@gmail.com"
         }
    // I have schedule the test cases for every minute, we can schedule the build as per requirement.
    triggers {
        cron('*/1 * * * *')
    }
    stages{
        stage('checkout'){
            steps{
                checkout scm
            }
        }
        
        stage('build'){
            steps{
                sh "mvn clean install -DskipTests"
            }
        }
        
        stage('test'){
            steps{
                sh "mvn test"
                junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'
            }
        }
     //   I have generated the SSH keys and pasted the public key in destination "VM" and the private key is pasted in "testforcron". So the jar file can be deployed in destination VM.   
        stage('deploying to VM') {
             steps{
               sshagent(['testforcron']) {
               sh "scp -o StrictHostKeyChecking=no target/my-app-1.0-SNAPSHOT.jar $target_user@$target_server:/home/ubuntu"
                 }
            }
        }
    }
    
    post {
        always {
            deleteDir()
        }
        // This should be acheived if we configure the STMP server in jenkins
        failure{
            emailext body: 'Check console output at $BUILD_URL to view the results.', subject: 'Reg: Build failed in jenkins', to: '${EMAIL_TO}'
        }
        success {
            echo "the job is success"
        }
    }
}
