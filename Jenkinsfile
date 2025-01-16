node {
    docker.image('python:3.9-alpine').inside {
        // Stage 'Build'
        stage('Build') {
            try {
                // Menjalankan perintah py_compile untuk memeriksa syntax Python
                sh 'python -m py_compile sources/add2vals.py sources/calc.py > log.txt 2>&1'
            } finally {
                // Mengarsipkan file log
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
            }
        }

        // Stage 'Test'
        stage('Test') {
            try {
                // Menginstal pytest
                sh 'pip install --user pytest'

                // Menjalankan pytest dan menyimpan hasilnya dalam format XML
                sh 'pytest --verbose --junit-xml=test-reports/results.xml sources/test_calc.py > log.txt 2>&1'
            } finally {
                // Mengarsipkan file log dan hasil test
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
                junit 'test-reports/results.xml'
            }
        }
    }
}