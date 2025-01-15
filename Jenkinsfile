node {
    docker.image('python:3.9-slim').inside('--network host') {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
}