pipeline {
    agent any
    stages {
        stage('build') {
            environment {
                REGISTRY = 'as01:6001'
            }
            steps {
                sh 'docker-compose -f docker-compose.as01.yml build'
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
                REGISTRY = 'as01:6001'
            }
            steps {
                sh 'echo Deploy to test env'
                sh 'docker-compose -f docker-compose.as01.yml push'
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
