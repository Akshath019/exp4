pipeline{
    agent any
    stages{

        stage('Build'){
            steps{
                sh 'javac Main.java'
            }
        }
        stage('Test'){
            steps{
                sh 'java Main.java'
            }
        }
    }
}