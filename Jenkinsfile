pipeline {
    agent any
    stages {
        stage('build') {
            steps {
                sh 'docker-compose build'
            }
        }

        stage('test') {
            steps {
                // sh 'env | sort'
                sh 'echo run tests'
            }
        }

        stage('deploy_test') {
            environment {
                REGISTRY = 'as01:6000'
            }
            steps {
                sh 'echo Deploy to test env'
                sh 'docker-compose push'
                sh 'docker stack deploy -c docker-compose.as01.yml mon'
            }           
        }

        stage('stage') {
            when { branch 'master' }
            steps {
                sh 'echo deploy to staging'
            }          
        }

        stage('Sanity check') {
            when { branch 'master' }
            steps {
                input "Does the staging environment look ok?"
                // milestone()
            }
        }      

        stage('prod') {
            when { branch 'master' }
            steps {
                sh 'echo deploy to prod'               
            }           
        }
    }
}
