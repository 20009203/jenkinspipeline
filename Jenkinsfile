pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '35.180.214.221', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '35.180.106.138', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
            	sh 'ifconfig'
                sh '''source /var/lib/jenkins/.bashrc
                      mvn clean package'''
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /home/jenkins/TomcatStagingAWS.pem **/target/*.war ec2-user@${params.tomcat_dev}:/opt/apache-tomcat-8.5.35/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/jenkins/TomcatProdAWS.pem **/target/*.war ec2-user@${params.tomcat_prod}:/opt/apache-tomcat-8.5.35/webapps"
                    }
                }
            }
        }
    }
}