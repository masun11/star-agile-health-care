pipeline {
    agent { label 'slave1' }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub_login')
    }

    stages {
        stage('SCM Checkout') {
            steps {
                git 'https://github.com/masun11/star-agile-health-care.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }

        stage("Docker build") {
            steps {
                sh 'pwd'
                sh 'whoami'
                sh 'groups'
                sh 'docker version'
                sh "docker build -t mausnulla/healthcare-eta-app:${BUILD_NUMBER} ."
                sh "docker tag mausnulla/healthcare-eta-app:${BUILD_NUMBER} mausnulla/healthcare-eta-app:latest"
                sh 'docker image ls | grep healthcare-eta-app'
            }
        }

        stage('Login2DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push2DockerHub') {
            steps {
                sh "docker push mausnulla/healthcare-eta-app:${BUILD_NUMBER}"
                sh "docker push mausnulla/healthcare-eta-app:latest"
            }
        }

        stage('Cleanup') {
            steps {
                sh 'docker image prune -f'
            }
        }
        
        stage('Deploy to Kubernetes Dev Environment') {
            steps {
		script {
		sshPublisher(publishers: [sshPublisherDesc(configName: 'kubernetes_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubernetesdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		}
            }
    	}
    }
}
