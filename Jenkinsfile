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
        docker.image('python:2-alpine').inside('--entrypoint=""') {
            sh '''
                # Verifikasi apakah pip terinstal
                which pip
                if [ $? -ne 0 ]; then
                    echo "pip not found!"
                    exit 1
                fi

                # Install PyInstaller versi 3.6 untuk kompatibilitas dengan Python 2.7
                pip install pyinstaller==3.6

                # Verifikasi PyInstaller terinstal
                which pyinstaller
                if [ $? -ne 0 ]; then
                    echo "PyInstaller not found!"
                    exit 1
                fi

                # Membuat executable
                cd ${WORKSPACE}
                mkdir -p dist
                # Menjalankan PyInstaller untuk membuat file executable
                PyInstaller --onefile sources/add2vals.py

                # Menunggu hingga PyInstaller selesai
                wait $!
            '''

            // Mengecek apakah executable berhasil dibuat
            if (fileExists('dist/add2vals')) {
                archiveArtifacts artifacts: 'dist/add2vals', fingerprint: true
            } else {
                error 'PyInstaller gagal membuat executable'
            }
        }
    }

}