node {
    docker.image('python:3.9-slim').inside('--network host') {
        stage('Install Dependencies') {
            // Memastikan file requirements.txt ada pada direktori yang benar
            sh 'ls -la' // Menampilkan daftar file di dalam kontainer
        }

        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }

        stage('Test') {
            sh './jenkins/scripts/test.sh'
        }
    }
}