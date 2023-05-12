pipeline {
    agent {label "agentfarm" }

    stages {
        stage('Installing Maven') {
            steps {
                sh 'sudo apt-get update -y && sudo apt-get upgrade -y'
                sh 'sudo apt-get install -y wget tree unzip maven'
            }
        }
        stage('Compiling and Running Test Case') {
            steps {
                sh 'mvn clean'
                sh 'mvn compile'
                sh 'mvn test'
            }
        }
        stage('Creating Package') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Install sonarqube cli') {
            steps {
                sh 'wget -O sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.6.2.2472-linux.zip'
                sh 'unzip -o -q sonar-scanner.zip'
                sh 'sudo rm -rf /opt/sonar-scanner'
                sh 'sudo mv --force sonar-scanner-cli-4.6.2.2472-linux /opt/sonar-scanner'
                sh 'sudo sh -c \'echo "#/bin/bash \nexport PATH=\\\"$PATH:/opt/sonar-scanner/bin\\\"" > /etc/profile.d/sonar-scanner.sh\''
                sh 'chmod +x /opt/sonar-scanner/bin/sonar-scanner'
                sh './etc/profile.d/sonar-scanner'
            }
        }
        stage ('Analyzing Code Quality') {
            steps {
                sh '/opt/sonar-scanner/bin/sonar-scanner -Dsonar.projectKey=babuni119002_devops-boot-camp-key -Dsonar.organization=babuni119002 -Dsonar.qualitygate.wait=true -Dsonar.qualitygate.timeout=300 -Dsonar.sources=src/main/java/ -Dsonar.java.binaries=target/classes -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=c628f50daacd3c187a73062ef4b9a7e2250e78ee'
            }
        }
        stage('Deploying Application using Ansible') {
            steps {
                sh 'export ANSIBLE_HOST_KEY_CHECKING=False && ansible-playbook -- private-key=/home/ubuntu/.ssh/vm-instance-key.pem -i host_inventory deploy-artifact.yml'
                    }
        }
    }
}
