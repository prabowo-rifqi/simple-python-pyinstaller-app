node {
    docker.image('python-dind').inside('--privileged --user root') {
        stage('Build') {
            try {
                // Menjalankan perintah py_compile untuk memeriksa syntax Python
                sh 'python -m py_compile sources/add2vals.py sources/calc.py > log.txt 2>&1'
                echo "Build completed successfully. Logs are stored in log.txt."
            } catch (Exception e) {
                echo "Build failed. See log.txt for details."
                throw e
            } finally {
                // Mengarsipkan file log
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
            }
        }

        stage('Test') {
            try {
                // Menjalankan pytest dan menyimpan hasilnya dalam format XML
                sh 'pytest --verbose --junit-xml=test-reports/results.xml sources/test_calc.py > log.txt 2>&1'
                echo "Tests completed successfully. Logs are stored in log.txt."
            } catch (Exception e) {
                echo "Tests failed. See log.txt for details."
                throw e
            } finally {
                // Mengarsipkan file log dan hasil test
                archiveArtifacts artifacts: 'log.txt', allowEmptyArchive: true
                junit 'test-reports/results.xml'
            }
        }
        stage('Deliver') {
            try {
                // Menjalankan perintah pyinstaller untuk membuat file executable
                sh 'pyinstaller --onefile sources/add2vals.py >> log.txt 2>&1'
                echo "Executable creation completed. Logs are stored in log.txt."
            } catch (Exception e) {
                echo "Executable creation failed. See log.txt for details."
                throw e
            } finally {
                // Mengarsipkan hasil file executable yang dihasilkan
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
                    sh 'mkdir -p ~/.ssh && ssh-keyscan -H 54.169.12.75 >> ~/.ssh/known_hosts >> log.txt 2>&1'

                    // Transfer file dengan SCP ke path tujuan di server AWS
                    sh 'scp -i $AWS_SSH_KEY -r ./dist/* $SSH_USER@54.169.12.75:/home/ubuntu/my-python-app/ >> log.txt 2>&1'
                    echo "Deployment completed successfully. Logs are stored in log.txt."
                }
            } catch (Exception e) {
                echo "Deployment failed. See log.txt for d
