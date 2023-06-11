pipeline{
    agent{
        label 'Master'
    }

    tools {
        maven 'maven_3.9.0'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ramya-03/java-maven-war-app.git']])
            }

        }

        stage('Maven-Build'){
            steps{
                sh 'mvn clean install'
            }
        }

        stage('Sonar Scan'){
            steps{
                withSonarQubeEnv("SonarQube") {
                    sh "${tool("Sonar_4.8")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://ec2-13-239-122-0.ap-southeast-2.compute.amazonaws.com:9000/ \
                    -Dsonar.login=sqp_9718f6dcf7e4843f010c8526d40c49b1e81519ac \
                    -Dsonar.java.binaries=target \
                    -Dsonar.projectKey=java-mavne-app-war"
                }
            }
        }

        stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('deployment'){
            agent{
                label 'Ansible'
            }
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }

    }
}
