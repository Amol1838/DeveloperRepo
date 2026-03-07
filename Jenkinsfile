pipeline {
    agent any

    environment {
        SMOKE_ENV = "dev"
        REG_ENV = "qa"
    }

    triggers {
        githubPush()
        cron('H 5 * * * ')   // Run regression every 5 minutes
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Automation Repo') {
            steps {
                git branch: 'master', url: 'http://github.com/Amol1838/AutomationRepo'
            }
        }

        stage('Verify Maven') {
            steps {
                bat 'mvn -version'
            }
        }

        stage('Run Smoke Tests') {
            when {
                not { triggeredBy 'TimerTrigger' }
            }
            steps {
                echo "Running Smoke Tests on ${env.SMOKE_ENV}"

                bat """
                mvn clean test ^
                -DsuiteXmlFile=smoke_testng.xml ^
                -Denv=${env.SMOKE_ENV}
                """
            }
        }

        stage('Run Regression Tests') {
            when {
                triggeredBy 'TimerTrigger'
            }
            steps {
                echo "Running Regression Tests on ${env.REG_ENV}"

                bat """
                mvn clean test ^
                -DsuiteXmlFile=regression_testng.xml ^
                -Denv=${env.REG_ENV}
                """
            }
        }
    }

    post {
        always {
            junit '**/target/surefire-reports/*.xml'

        }
    }
}
