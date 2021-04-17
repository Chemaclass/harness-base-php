pipeline {
    agent none
    environment {
        COMPOSE_DOCKER_CLI_BUILD = 1
        DOCKER_BUILDKIT = 1
        MY127WS_KEY = credentials('base-my127ws-key-20190523')
    }
    options {
        buildDiscarder(logRotator(daysToKeepStr: '30'))
        parallelsAlwaysFailFast()
    }
    triggers { cron(env.BRANCH_NAME ==~ /^\d+\.\d+\.x$/ ? 'H H(0-6) * * *' : '') }
    stages {
        stage('Build and Test') {
            parallel {
                stage('1. PHP, Drupal 8, Magento 2') {
                    agent {
                        docker {
                            label 'my127ws'
                            alwaysPull true
                            image 'quay.io/inviqa_images/workspace:latest'
                            args '--entrypoint "" --volume /var/run/docker.sock:/var/run/docker.sock --volume "$HOME/.my127:/root/.my127"'
                        }
                    }
                    stages {
                        stage('Prepare') {
                            steps {
                                sh './delete_running_containers.sh'
                                sh './build'
                            }
                        }
                        stage('PHP Static') {
                            steps { sh './test php static' }
                        }
                        stage('Drupal 8 Static') {
                            steps { sh './test drupal8 static' }
                        }
                        stage('Magento 2 Static') {
                            steps { sh './test magento2 static' }
                        }
                        stage('PHP Dynamic') {
                            steps { sh './test php dynamic' }
                        }
                        stage('Drupal 8 Dynamic') {
                            steps { sh './test drupal8 dynamic' }
                        }
                        stage('Magento 2 Dynamic') {
                            steps { sh './test magento2 dynamic' }
                        }
                        stage('PHP Dynamic Mutagen') {
                            steps { sh './test php dynamic mutagen' }
                        }
                        stage('Drupal 8 Dynamic Mutagen') {
                            steps { sh './test drupal8 dynamic mutagen' }
                        }
                        stage('Magento 2 Dynamic Mutagen') {
                            steps { sh './test magento2 dynamic mutagen' }
                        }
                    }
                    post {
                        always {
                            sh '(cd tmp-test && ws destroy) || true'
                            sh 'ws destroy || true'
                            sh './delete_running_containers.sh'
                            cleanWs()
                        }
                    }
                }
                stage('2. Magento 1, Spryker') {
                    agent {
                        docker {
                            label 'my127ws'
                            alwaysPull true
                            image 'quay.io/inviqa_images/workspace:latest'
                            args '--entrypoint "" --volume /var/run/docker.sock:/var/run/docker.sock --volume "$HOME/.my127:/root/.my127"'
                        }
                    }
                    stages {
                        stage('Prepare') {
                            steps {
                                sh './delete_running_containers.sh'
                                sh './build'
                            }
                        }
                        stage('Magento 1 Static') {
                            steps { sh './test magento1 static' }
                        }
                        stage('Spryker Static') {
                            steps { sh './test spryker static' }
                        }
                        stage('Magento 1 Dynamic') {
                            steps { sh './test magento1 dynamic' }
                        }
                        stage('Spryker Dynamic') {
                            steps { sh './test spryker dynamic' }
                        }
                        stage('Magento 1 Dynamic Mutagen') {
                            steps { sh './test magento1 dynamic mutagen' }
                        }
                        stage('Spryker Dynamic Mutagen') {
                            steps { sh './test spryker dynamic mutagen' }
                        }
                    }
                    post {
                        always {
                            sh '(cd tmp-test && ws destroy) || true'
                            sh 'ws destroy || true'
                            sh './delete_running_containers.sh'
                            cleanWs()
                        }
                    }
                }
            }
        }
    }
}
