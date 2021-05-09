pipeline {
    agent {
        label 'agent1'
    }
    tools {
         maven 'maven362'
    }
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
        
        stage('target1') {
            environment {
               target_user = "ubuntu"
                 target_server = "34.224.41.159"
            }
                
            steps{
               echo "deploying Dev environment"
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
        failure{
            emailext body: 'Check console output at $BUILD_URL to view the results.', subject: 'Reg: Build failed in jenkins', to: 'gopisaramalla98@gmail.com'
        }
        success {
            echo "the job is success"
        }
    }
}
