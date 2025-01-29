node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        
        junit 'test-reports/results.xml'
    }

    stage('Manual Approval')  {         
        input message: 'Lanjutkan ke tahap Deploy? (Klik "Proceed" untuk melanjutkan ke tahap Deploy)'     
    }

    stage('Deploy') {
        docker.image('python:3.8-alpine').inside('-u root') {
        sh 'apk add --no-cache binutils'
        sh 'pip install pyinstaller'
        sh 'pyinstaller --onefile sources/add2vals.py'
        sh 'sleep 60 & wait'
        }
        archiveArtifacts artifacts: 'dist/add2vals', onlyIfSuccessful: true
    }
}