pipeline {
    agent any

    environment {
        WORKSPACE_DIR = '/workspace'
        WORKSPACE_APP_DIR = '/workspace/apps/springboot-context-jdk24'
    }

    stages {
        stage('All Steps') {
            steps {
                dir("${env.WORKSPACE_APP_DIR}") {
                    script {
                        sh 'mvn clean install'
                    }
                }
            }
            post {
                failure {
                    dir("${env.WORKSPACE_DIR}") {
                        script {
                            def jobOutput = currentBuild.rawBuild.getLog(1000).join('\n')
                            def cleanOutput = jobOutput.replaceAll(/\x1B\[[;0-9]*[a-zA-Z]/, '').replaceAll(/\[\[0;?[0-9;]*m/, '')

                            writeFile file: '/tmp/job_output.txt', text: cleanOutput

                            sh "echo 'Build failed. Calling agent to fix the issues...'"
                            sh "chmod +x agent.py"
                            sh "python agent.py ${WORKSPACE_APP_DIR} /tmp/job_output.txt"
                        }
                    }
                }
            }
        }
    }
}
