// This adds install and test stages before static code analysis
pipeline {
  environment {
     registry = "pallavimantrao/vatcal"
        registryCredentials = "dockerhub_id"
        dockerImage = ""
  }
  agent any

  stages {
    stage('Checkout') {
        steps {
          // Get some code from a GitHub repository
          git branch: 'main', url: 'https://github.com/pallavimantrao/lbg-vat-calculator.git'
        }
    }
    stage('Install') {
        steps {
            // Install the ReactJS dependencies
            sh "npm install"
        }
    }
    stage('Test') {
        steps {
          // Run the ReactJS tests
          sh "npm test"
        }
    }
     stage('SonarQube Analysis') {
      environment {
        scannerHome = tool 'sonarqube'
        }
        steps {
            withSonarQubeEnv('sonar-qube-1') {        
              sh "${scannerHome}/bin/sonar-scanner"
        }
        timeout(time: 10, unit: 'MINUTES'){
          waitForQualityGate abortPipeline: true
          }
        }
    }
   
            stage ('Build Docker Image'){
                steps{
                    script {
                        dockerImage = docker.build(registry)
                    }
                }
            }

            stage ("Push to Docker Hub"){
                steps {
                    script {
                        docker.withRegistry('', registryCredentials) {
                            dockerImage.push("${env.BUILD_NUMBER}")
                            dockerImage.push("latest")
                        }
                    }
                }
            }

            stage ("Clean up"){
                steps {
                    script {
                        sh 'docker image prune --all --force --filter "until=48h"'
                           }
                }
            }
        }
  }



