node {
    docker.image('python:3.9-slim').inside('--network host') {
        stage('Install Dependencies') {
            sh 'pip install -r requirements.txt'  // Install dependencies jika ada
        }

        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'  // Build aplikasi Python
        }
    }
}