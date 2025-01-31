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
        sh 'apk add --no-cache binutils lftp'
        sh 'pip install pyinstaller'
        sh 'pyinstaller --onefile sources/add2vals.py'

        withCredentials([usernamePassword(credentialsId: 'cpanel-user', usernameVariable: 'FTP_USER', passwordVariable: 'FTP_PASS')]) {
            sh '''#!/bin/sh
            echo "Check File Before Upload..."
            lftp -u $FTP_USER,$FTP_PASS -e "ls htdocs; bye" ftp://ftpupload.net
            '''
            
            sh '''#!/bin/sh
                echo "Uploading file to FTP..."
                lftp -u "$FTP_USER","$FTP_PASS" ftp://ftpupload.net <<EOF
                cd htdocs
                put dist/add2vals
                bye
                EOF
            '''
            
            sh '''#!/bin/sh
            echo "Check File After Upload..."
            lftp -u $FTP_USER,$FTP_PASS -e "ls htdocs; bye" ftp://ftpupload.net
            '''
        }
        sh 'sleep 60 & wait'
        }
        archiveArtifacts artifacts: 'dist/add2vals', onlyIfSuccessful: true
    }
}