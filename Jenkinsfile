//Scripted

/*
node {
	stage('Build') {
		echo "Build"
	}
	stage('Test') {
		echo "Test"
	}
	stage('Integration Test') {
		echo "Integration Test"
	}
}
*/

//Declarative

pipeline {
    //agent any
    agent { docker { image 'maven:3.6.3'} }
    environment {
        dockerHome = tool 'myDocker'
        mavenHome = tool 'myMaven'
        PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"
    }
	stages{
	    stage('Checkout') {
            steps{
                sh 'mvn --version'
                sh 'docker version'
                echo "Build"
                echo "$PATH"
	        }
        }
        stage('Compile') {
            steps{
                sh "mvn clean compile"
            }
        }
        stage('Test') {
            steps{
                sh "mvn Test"
            }
        }
        stage('Integration Test') {
            steps{
                sh "mvn failsafe:integration-test failsafe:verify"
            }
        }
        stage('Package') {
            steps{
                sh "mvn package"
            }
        }
        stage('Build Docker Image') {
            steps{
                //"docker build -t makeshbaboo/currency-exchange-devops:$env.BUILD_TAG"
                script{
                    dockerImage = docker.build("makeshbaboo/currency-exchange-devops:${env.BUILD_TAG}")
                }
            }
        }
        stage('Push Docker Image') {
            steps{
                script{
                    docker.withRegistry('', 'dockerhub'){
                        dockerImage.push();
                        dockerImage.push('latest');
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'testing always'
        }
        success {
            echo 'testing success'
        }
        failure {
            echo 'testing failure'
        }
    }
}
