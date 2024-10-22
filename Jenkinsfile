#!/usr/bin/env groovy 

/*
 * This file bootstraps the codified Continuous Delivery pipeline for extensions of SAP solutions such as SAP S/4HANA.
 * The pipeline helps you to deliver software changes quickly and in a reliable manner.
 * A suitable Jenkins instance is required to run the pipeline.
 * More information on getting started with Continuous Delivery can be found here: https://www.project-piper.io/
 */

/*
@Library('piper-lib-os') _

piperPipeline script: this
*/
pipeline {
    agent any

    environment {
        CF_API_ENDPOINT = 'https://api.cf.us10-001.hana.ondemand.com'
        CF_ORG = '280032fctrial'
        CF_SPACE = 'dev'
        CF_CREDENTIALS_ID = 'deploy-to-cf'
    }

    stages {
        stage('Initialize') {
            steps {
                script {
                    echo 'Initializing the pipeline...'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    echo 'Installing dependencies...'
                    sh 'npm ci'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building the project...'
                    sh 'npx cds build --production'
                }
            }
        }

        stage('Deploy to Cloud Foundry') {
            steps {
                script {
                    echo 'Deploying to Cloud Foundry...'
                    withCredentials([usernamePassword(credentialsId: env.CF_CREDENTIALS_ID, usernameVariable: 'CF_USERNAME', passwordVariable: 'CF_PASSWORD')]) {
                        sh "cf login -a ${env.CF_API_ENDPOINT} -u ${CF_USERNAME} -p ${CF_PASSWORD} -o ${env.CF_ORG} -s ${env.CF_SPACE}"
                        sh 'cf push -f mta.yaml'
                    }
                }
            }
        }
    }

    post {
        success {
            script {
                echo 'Pipeline completed successfully!'
            }
        }
        failure {
            script {
                echo 'Pipeline failed! Please check the logs.'
            }
        }
    }
}
