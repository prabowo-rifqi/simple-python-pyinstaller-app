node {
    docker.image('python-dind').inside('--privileged --user root') {
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
                sh 'pytest --verbose --junit-xml=test-reports/results.xml sources/test_calc.py > log.txt 2>&1'
            } finally {
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
                junit 'test-reports/results.xml'
            }
        }
        stage('Create Executable') {
            try {
                sh 'pyinstaller --onefile sources/add2vals.py > log.txt 2>&1'
            } finally {
                archiveArtifacts 'dist/add2vals'
            }
        }
        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed', cancel: 'Abort'
        }
        stage('Deploy') {
            try {
                withCredentials([sshUserPrivateKey(credentialsId: '8b0b5e66-4b7d-4887-953c-6b114a06cb90', keyFileVariable: 'AWS_SSH_KEY', usernameVariable: 'SSH_USER')]) {
                    // Tambahkan kunci host AWS ke known_hosts untuk menghindari "Host key verification failed"
                    sh 'mkdir -p ~/.ssh && ssh-keyscan -H 54.169.12.75 >> ~/.ssh/known_hosts'

                    // Transfer file dengan SCP ke path tujuan di server AWS
                    sh 'scp -i $AWS_SSH_KEY -r ./dist/* $SSH_USER@54.169.12.75:/home/ubuntu/my-python-app/ > log.txt 2>&1'

                    echo "Aplikasi berhasil dideploy di server AWS."
                }
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                throw e
            }
        }
    }
}