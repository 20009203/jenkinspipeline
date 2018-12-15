pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '172.31.37.73', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '172.31.47.143', description: 'Production Server')
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
                    	sh 'pwd'
                        sh "scp -i /home/jenkins/TomcatStagingAWS.pem **/target/*.war ec2-user@${params.tomcat_dev}:/opt/apache-tomcat-8.5.35/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh 'pwd'
                        sh "scp -i /home/jenkins/TomcatProdAWS.pem **/target/*.war ec2-user@${params.tomcat_prod}:/opt/apache-tomcat-8.5.35/webapps"
                    }
                }
            }
        }
    }
}