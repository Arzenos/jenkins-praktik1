pipeline {
    agent {
        docker {
            image 'python:3.10'
            args '-u root:root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest test_app.py'
            }
        }

        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: "release/.*", comparator: "REGEXP"
                }
            }
            steps {
                echo "Simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post {
        success {
            script {
                def payload = [
                    content: "✅ Build SUCCESS on `${env.BRANCH_NAME}`\n🔗URL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discordapp.com/api/webhooks/1425090678982967306/55jIgPC0J5O_wt0BLLvAIvLlN51W3YVX2MaQV6KKl3Juva5LbGV6ZspZukaWcGt91kdh'
                )
            }
        }

        failure {
            script {
                def payload = [
                    content: "❌ Build FAILED on `${env.BRANCH_NAME}`\n🔗URL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discordapp.com/api/webhooks/1425090678982967306/55jIgPC0J5O_wt0BLLvAIvLlN51W3YVX2MaQV6KKl3Juva5LbGV6ZspZukaWcGt91kdh'
                )
            }
        }
    }
}
