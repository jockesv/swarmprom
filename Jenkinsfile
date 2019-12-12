pipeline {
    agent any
    environment {
        TEST_REGISTRY = 'as01:6001'
        PROD_REGISTRY = 'tvvls018:6000'
    }
    stages {
        stage('build') {
            environment {
                REGISTRY = "${TEST_REGISTRY}"
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

        stage('prod_push') {
            when { branch 'master' }
            environment {
                REGISTRY = "${PROD_REGISTRY}"
            }
            steps {   
                sh 'echo deploy to prod'    
                sh "docker tag ${TEST_REGISTRY}/swarmprom-grafana:5.3.4 ${REGISTRY}/swarmprom-grafana:5.3.4"
                sh "docker tag ${TEST_REGISTRY}/swarmprom-grafana:5.3.4 ${REGISTRY}/swarmprom-grafana:latest"
                sh "docker push ${REGISTRY}/swarmprom-grafana"
            }           
        }

        stage('prod_deploy') {
            when { branch 'master' }
            environment {
                REGISTRY = 'tvvls018:6000'
                DOCKER_HOST = '172.18.0.1:12376'
            }
            steps {
                sh 'docker stack deploy -c docker-compose.prod.yml mon'
                sh 'echo deploy to prod'               
            }           
        }
    }
}
