pipeline {
    agent none 
    stages {
       
        stage('Build') { 
            agent {
                docker {
                    image 'python:2-alpine' 
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2'
                }
            }
            steps {
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
        stage('Docker Push') {
      agent any
      steps {
        withCredentials([usernamePassword(credentialsId: 'https://cloud.docker.com/u/mfaizalsg/repository/docker/mfaizalsg/mfaizalsg', passwordVariable: 'ttp.road', usernameVariable: 'mfaizalsg')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh 'docker push mfaizalsg/mfaizalsg:latest'
        }
      }
    }
    }
}
