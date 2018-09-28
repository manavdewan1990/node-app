pipeline {
    agent {
        docker {
            image 'goforgold/build-container:latest'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Create Packer AMI') {

            steps {
                script
                        {
                            if (env.CREATE_AMI == 'true') {
                                withCredentials([
                                        usernamePassword(credentialsId: 'ada90a34-30ef-47fb-8a7f-a97fe69ff93f', passwordVariable: 'AWS_SECRET', usernameVariable: 'AWS_KEY')
                                ]) {
                                   /* sh 'packer build -var aws_access_key=${AWS_KEY} -var aws_secret_key=${AWS_SECRET} packer/packer.json'
                               */ }
                            }
                        }


            }
        }

        stage('AWS Deployment') {
            steps {
                withCredentials([
                        usernamePassword(credentialsId: 'ada90a34-30ef-47fb-8a7f-a97fe69ff93f', passwordVariable: 'AWS_SECRET', usernameVariable: 'AWS_KEY'),
                        usernamePassword(credentialsId: '2facaea2-613b-4f34-9fb7-1dc2daf25c45', passwordVariable: 'REPO_PASS', usernameVariable: 'REPO_USER'),
                        [$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'f30ac8b2-6480-49fb-afb3-2feb311a4a75']
                ]) {
                    sh 'rm -rf node-app-terraform'
                    sh 'git clone https://github.com/manavdewan1990/node-app-terraform.git'
                    sh '''
               cd node-app-terraform
               terraform init -var access_key=${AWS_KEY} -var secret_key=${AWS_SECRET} -backend-config="profile=default"
               terraform destroy   -force  -var access_key=${AWS_KEY} -var secret_key=${AWS_SECRET}
               ls

            '''
                }
            }
        }
    }
    environment {
        npm_config_cache = 'npm-cache'
    }
}
