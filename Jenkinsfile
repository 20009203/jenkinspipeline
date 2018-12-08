pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '3.17.9.3', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.223.186.201', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
		stage ('Preparing'){
			steps{
				export M2_HOME='/opt/maven/apache-maven-3.5.3'
				export PATH="$PATH:$M2_HOME/bin"
				echo $PATH
				mvn -version
			}
			post {
                success {
                    echo 'Commande mvn OK!'
                }
            }
		}
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

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /home/jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}