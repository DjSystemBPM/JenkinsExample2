pipeline {
    agent any
    tools { 
        maven 'localMvn' 
        jdk 'localJdk' 
    }
    parameters {
         string(name: 'tomcat_dev', defaultValue: '54.187.209.26', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '34.210.221.245', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

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

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /mnt/Maven/tomcatDemo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /mnt/Maven/tomcatDemo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}
