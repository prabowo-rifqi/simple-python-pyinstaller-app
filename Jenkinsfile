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
                script {
                    sh 'python -m py_compile sources/add2vals.py sources/calc.py > log.txt 2>&1'
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
                }
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                script {
                    // Menulis log hasil tes ke dalam log.txt
                    sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py > log.txt 2>&1'
                }
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                    archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
                }
            }
        }
    }
}
