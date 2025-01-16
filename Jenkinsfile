node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            try {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py > log.txt 2>&1'
            } finally {
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
            }
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside {
            try {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py > log.txt 2>&1'
            } finally {
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
                junit 'test-reports/results.xml'
            }
        }
    }
    node {
        // Definisikan agent Docker
        docker.image('cdrx/pyinstaller-linux:python2').inside {

            // Stage untuk Deliver
            try {
                stage('Deliver') {
                    // Jalankan perintah untuk membuat executable menggunakan PyInstaller
                    sh 'pyinstaller --onefile sources/add2vals.py'
                }

                // Jika sukses, arsipkan artifacts
                archiveArtifacts 'dist/add2vals'
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                throw e
            }
        }
    }

}