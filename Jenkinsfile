node {
    docker.image('python:3.9-slim').inside('--network host') {
        stage('Install Dependencies') {
            // Memastikan file requirements.txt ada pada direktori yang benar
            sh 'ls -la' // Menampilkan daftar file di dalam kontainer
            sh 'pip install --no-cache-dir -r requirements.txt'  // Menambahkan --no-cache-dir
        }

        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }

        stage('Test') {
            sh './jenkins/scripts/test.sh'
        }
    }
}