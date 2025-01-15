node {
    docker.image('python:3.9-slim').inside('--network host') {
        // Stage Build
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }

        // Stage Test
        stage('Test') {
            try {
                // Install virtualenv and create a new environment
                sh 'pip install virtualenv'
                sh 'virtualenv venv'

                // Activate the virtual environment and install pytest
                sh 'source venv/bin/activate && pip install pytest'

                // Run the tests with pytest
                sh 'source venv/bin/activate && pytest --junit-xml test-reports/results.xml sources/test_calc.py'
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                throw e
            } finally {
                junit 'test-reports/results.xml'
            }
        }
    }
}