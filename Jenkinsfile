pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                  git branch: env.Github_Branch, changelog: false, credentialsId: env.Github_Credentials, poll: false, url: env.Github_URL
            }
        }        
        stage('Build') {
            steps {
                script 
                {
                    if ( env.Build_Tool == 'Maven')
                     {
                         sh 'mvn package -s settings.xml'
                     }
                     else if ( env.Build_Tool == 'Ant')
                     {
                        sh 'ant -buildfile build'
                     }
                }
            }
        }
        stage('Test') {
            steps {
                script
                    {
                        if (env.Perform_Sonar_Scan == 'true' )
                        {
                                sh 'mvn sonar:sonar -Dsonar.projectKey=cicd-solution -Dsonar.host.url=https://c951da7c7c5331.localhost.run -Dsonar.login=5278e2ccf6aea994ccdb4f356be5f4e4df923f7f'
                        }
                   }
                }
          }
        stage('Deploy') {
            steps {
                script {
                    if (env.Perform_EC2_deployment == 'true')
                    {
                        withCredentials([string(credentialsId: 'aws_key', variable: 'aws_key'), string(credentialsId: 'ec2_access_key', variable: 'ec2_access_key'), string(credentialsId: 'ec2_secret_key', variable: 'ec2_secret_key'), , string(credentialsId: 'aws_key', variable: 'aws_key')]) 
                        {
                             sh 'echo $aws_key > aws_key.pub'
                             sh 'ansible-playbook -vvvvv ansible/playbook.yml -e \"ec2_access_key=$ec2_access_key\" -e \"ec2_secret_key=$ec2_secret_key\"'
                        }
                    }
                }
            }
        }
    }
}
