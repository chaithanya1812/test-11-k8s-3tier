pipeline{
    agent any
    tools{
        maven 'MAVEN'
    }
    stages{
        stage('git-clone'){
            steps{
                git branch: 'FIRST', url: 'https://github.com/chaithanya1812/test-11-k8s-3tier.git'
            }
        }
        stage('maven build'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('SonarqubeReport'){
            steps{
               sh 'mvn sonar:sonar'
            
            }
        }
        stage('Nexus-Artifact'){
            steps{
               sh 'mvn deploy'
            
            }
        }
        stage('docker login & docker push'){
            steps{
                withCredentials([string(credentialsId: 'ddockerloginn', variable: 'dockerlogin')]) {
                    sh 'docker login -u chaitu1812 --password $dockerlogin'
                 }
                sh 'docker build -t chaitu1812/kubernetesproject .'
                sh 'docker push chaitu1812/kubernetesproject'
                sh 'docker rmi chaitu1812/kubernetesproject'
            }
        }
       stage('Deploy into k8s'){
            steps{
                sshagent(['k8slogin']) {
                sh "scp -o StrictHostKeyChecking=no springbootjava.yaml ec2-user@65.0.185.157:/home/ec2-user/"    
                sh "ssh -o StrictHostKeyChecking=no ec2-user@65.0.185.157 kubectl apply -f springbootjava.yaml"    
                }
            }
        }
    }
}
