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
                // Install pytest before running tests
                sh 'pip install pytest'

                // Run the tests with pytest
                sh 'pytest --junit-xml test-reports/results.xml sources/test_calc.py'
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                throw e
            } finally {
                junit 'test-reports/results.xml'
            }
        }
    }
}