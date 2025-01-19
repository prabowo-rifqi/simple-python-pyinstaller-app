node {
    docker.image('python-dind').inside('--privileged --user root') {
        stage('Build') {
            try {
                // Menjalankan perintah py_compile untuk memeriksa syntax Python
                sh 'echo "Starting Build Stage" > log.txt'
                sh 'python -m py_compile sources/add2vals.py sources/calc.py >> log.txt 2>&1'
                sh 'echo "Build Stage completed" >> log.txt'
            } finally {
                // Mengarsipkan file log
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
            }
        }

        stage('Test') {
            try {
                // Menjalankan pytest dan menyimpan hasilnya dalam format XML
                sh 'echo "Starting Test Stage" >> log.txt'
                sh 'pytest --verbose --junit-xml=test-reports/results.xml sources/test_calc.py >> log.txt 2>&1'
                sh 'echo "Test Stage completed" >> log.txt'
            } finally {
                // Mengarsipkan file log dan hasil test
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
                junit 'test-reports/results.xml'
            }
        }
        stage('Create Executable') {  // Mengganti nama stage menjadi Create Executable
            try {
                // Menjalankan perintah pyinstaller untuk membuat file executable
                sh 'echo "Starting Create Executable Stage" >> log.txt'
                sh 'pyinstaller --onefile sources/add2vals.py >> log.txt 2>&1'
                sh 'echo "Create Executable Stage completed" >> log.txt'
            } finally {
                // Mengarsipkan hasil file executable yang dihasilkan
                archiveArtifacts 'dist/add2vals'
            }
        }
        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed', cancel: 'Abort'
            sh 'echo "Manual Approval Stage completed" >> log.txt'
        }
        stage('Deploy') {
            try {
                withCredentials([sshUserPrivateKey(credentialsId: '8b0b5e66-4b7d-4887-953c-6b114a06cb90', keyFileVariable: 'AWS_SSH_KEY', usernameVariable: 'SSH_USER')]) {
                    // Tambahkan kunci host AWS ke known_hosts untuk menghindari "Host key verification failed"
                    sh 'echo "Starting Deploy Stage" >> log.txt'
                    sh 'mkdir -p ~/.ssh && ssh-keyscan -H 54.169.12.75 >> ~/.ssh/known_hosts >> log.txt 2>&1'

                    // Transfer file dengan SCP ke path tujuan di server AWS
                    sh 'scp -i $AWS_SSH_KEY -r ./dist/* $SSH_USER@54.169.12.75:/home/ubuntu/my-python-app/ >> log.txt 2>&1'

                    // SSH ke server AWS dan pindahkan hasil build ke folder yang tepat
                    sh 'ssh -i $AWS_SSH_KEY $SSH_USER@54.169.12.75 << EOF\n' +
                       '/home/ubuntu/my-python-app/add2vals 5 7 >> log.txt 2>&1' +
                       'EOF'

                    echo "Aplikasi berhasil dideploy di server AWS."
                    sh 'echo "Deploy Stage completed" >> log.txt'
                }
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                throw e
            }
        }
    }
}