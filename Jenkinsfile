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
        docker.image('cdrx/pyinstaller-linux:python2').inside('--entrypoint="" -v ${WORKSPACE}:/workspace') {
            sh '''
                cd /workspace
                mkdir -p dist
                python -m pyinstaller --onefile sources/add2vals.py
            '''

            if (fileExists('dist/add2vals')) {
                archiveArtifacts artifacts: 'dist/add2vals', fingerprint: true
            } else {
                error 'PyInstaller failed to create the executable'
            }
        }
    }
}