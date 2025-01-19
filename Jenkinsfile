node {
    docker.image('python-dind').inside('--network host --privileged') {
        stage('Build') {
            try {
                // Menjalankan perintah py_compile untuk memeriksa syntax Python
                sh 'python -m py_compile sources/add2vals.py sources/calc.py > log.txt 2>&1'
            } finally {
                // Mengarsipkan file log
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
            }
        }

        stage('Test') {
            try {
                // Menjalankan pytest dan menyimpan hasilnya dalam format XML
                sh 'pytest --verbose --junit-xml=test-reports/results.xml sources/test_calc.py > log.txt 2>&1'
            } finally {
                // Mengarsipkan file log dan hasil test
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
                junit 'test-reports/results.xml'
            }
        }
        stage('Deliver') {
            try {
                // Menjalankan perintah pyinstaller untuk membuat file executable
                sh 'pyinstaller --onefile sources/add2vals.py'
            } finally {
                // Mengarsipkan hasil file executable yang dihasilkan
                archiveArtifacts 'dist/add2vals'
            }
        }
        stage('Run Executable') {
            try {
                // Jalankan file hasil dari artifacts
                sh './dist/add2vals 5 7'
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                throw e
            }
        }
    }
}