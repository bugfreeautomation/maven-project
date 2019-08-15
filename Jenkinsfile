pipeline {
    
   tools {
    maven 'localMaven'
  }
  
    agent any
       stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage ('Deploy to Staging'){
            steps {
                build job: 'Deploy-to-staging'
            }
        }

        stage ('Deploy to Production'){
            steps{
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve PRODUCTION Deployment?' , submitter: 'admin'
                }

                build job: 'Deploy-to-Production'
            }
            post {
         
    changed {
        emailext body: '$DEFAULT_CONTENT', recipientProviders: [brokenTestsSuspects(), brokenBuildSuspects(), developers()], subject: '$DEFAULT_SUBJECT'
    }
           success {
                    echo 'Code deployed to Production.'
                    }

                failure {
                    echo ' Deployment failed.'              
                    
                }
            }
        }
    }
}
