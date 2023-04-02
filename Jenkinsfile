pipeline {
    agent any
    parameters {
        choice choices: ["build","destroy"], description: "terraform action", name: "terraform_action"
        string defaultValue: "", description: "", name: ""
    }

    stages {
        stage('chkout') {
            steps {
                git brach: 'main', url: '', credentialsid: ''
            }
        }
        stage('Docker Build') {
            steps {
                withDockerRegistry([credentialsId: 'name', url: '']) {
                    script {
                        sh 'docker build -f nginx/Dockerfile -t nginx-image .'
                    }
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    sh 'docker push nginx-image:tag .'
                }
            }
        }
        stage('Terraform Action') {
            steps {
                script {
                    if (param.terraform_action == 'build') {
                        sh 'terraform -chdir=./terraform init'
                        sh 'terraform -chdir=./terraform apply --auto-approve'
                    } else {
                        sh 'terraform -chdir=./terraform apply --auto-approve'
                    }
                }
            }
        }
        stage('Wait For Deployment') {
            steps {
                script {
                    sh 'sleep 240'
                }
            }
        }
        stage('Ansible') {
            steps {
                script {
                    sh 'ansible-playbook -i ansible/aws_instance ansible/ec2_playbook.yaml -vvv'
                }
            }
        }
    }
}