pipeline {
    agent { node { label "maven" } }
    parameters {
        string (name: 'INVOKER', defaultValue: 'John')
        string (name: 'BRANCH', defaultValue: '')
    }
    environment {
        BRANCH_NAME = "main"
    }
    stages {
        stage("Announce our start") {
            steps {
                echo "Hello ${params.INVOKER}, about to build Greeter service."
            }
        }
        stage("Ask which branch of the repository to clone if not set") {
            when {
                expression { params.BRANCH == '' }
            }
            steps {
                script {
                    timeout(time: 60, unit: "SECONDS") {
                        try {
                            def response = input(
                                message: "What is the branch to clone (default is \"main\")?",
                                ok: "Proceed",
                                parameters: [
                                    string(name: "BRANCH_NAME",
                                            description: "The name of the branch to clone",
                                            defaultValue: "main")
                                ]
                            )
                            BRANCH_NAME = response
                            echo "Got input response: ${response}"
                            // echo "Cloning branch ${response}"
                        } catch (exc) {
                            echo "User input timed out: ${exc}"
                            echo "Proceeding with defaults."
                        }
                    }
                    echo "After INPUT stage: cloning ${BRANCH_NAME}"
                }
            }
        }
        stage ("Clone git repository") {
            steps {
                echo "Cloning branch ${BRANCH_NAME} from ${GIT_URL}"
                git url: "${GIT_URL}", branch: "${BRANCH_NAME}"
            }
        }
        stage ("Run unit tests") {
            steps {
                sh '''
                    cd demoproject
                    mvn test
                '''
            }
        }
        stage ("Run verify - code coverage") {
            steps {
                sh '''
                    cd demoproject
                    mvn verify
                '''
            }
        }
    }
}