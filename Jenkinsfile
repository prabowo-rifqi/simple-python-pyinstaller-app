node {
    // Stage 'Build'
    stage('Build') {
        docker.image('python:2-alpine').inside {
            try {
                // Menjalankan perintah py_compile untuk memeriksa syntax Python
                sh 'python -m py_compile sources/add2vals.py sources/calc.py > log.txt 2>&1'
            } finally {
                // Mengarsipkan file log
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
            }
        }
    }

    // Stage 'Test'
    stage('Test') {
        docker.image('qnib/pytest').inside {
            try {
                // Menjalankan pytest dan menyimpan hasilnya dalam format XML
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py > log.txt 2>&1'
            } finally {
                // Mengarsipkan file log dan hasil test
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
                junit 'test-reports/results.xml'
            }
        }
    }

    // Stage 'Deliver'
    stage('Deliver') {
        docker.image('cdrx/pyinstaller-linux:python2').inside {
            try {
                // Membuat executable menggunakan PyInstaller
                sh 'pyinstaller --onefile sources/add2vals.py > build_log.txt 2>&1'
            } finally {
                // Jika build berhasil, arsipkan hasil dari build
                archiveArtifacts artifacts: 'dist/add2vals', allowEmptyArchive: true
            }
        }
    }
}