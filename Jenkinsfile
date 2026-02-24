pipeline {
    agent any
    
    triggers {
        // Every 5 minutes on Mondays only
        cron('H/5 * * * 1')
    }
    
    tools {
        maven 'Maven 3.9.0'
        jdk 'JDK 21'
    }
    
    environment {
        // Windows-specific settings
        MAVEN_OPTS = '-Xmx1024m'
        // Use the Maven wrapper for Windows
        MVNW_CMD = 'mvnw.cmd'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/Muntsirul/spring-petclinic.git'
            }
        }
        
        stage('Verify Environment') {
            steps {
                bat '''
                    @echo off
                    echo ===================================
                    echo VERIFYING BUILD ENVIRONMENT
                    echo ===================================
                    echo Java Version:
                    java -version
                    echo.
                    echo Maven Wrapper:
                    dir mvnw.cmd
                    echo.
                    echo Current Directory:
                    cd
                    dir
                '''
            }
        }
        
        stage('Build and Test with JaCoCo') {
            steps {
                bat '''
                    @echo off
                    echo ===================================
                    echo BUILDING WITH MAVEN AND JACOCO
                    echo ===================================
                    
                    echo Running Maven clean, verify and JaCoCo report...
                    call mvnw.cmd clean verify jacoco:report
                    
                    echo.
                    echo Build completed. Checking for artifacts...
                    dir target\\*.jar
                '''
            }
            post {
                always {
                    // Publish JaCoCo report
                    jacoco(
                        execPattern: 'target/jacoco.exec',
                        classPattern: 'target/classes',
                        sourcePattern: 'src/main/java',
                        inclusionPattern: '**/*.class',
                        exclusionPattern: '**/*Test*.class'
                    )
                }
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', 
                                 fingerprint: true,
                                 excludes: 'target/*-sources.jar'
                bat 'echo Artifacts archived successfully!'
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning workspace...'
            cleanWs()
        }
        success {
            echo ' Pipeline completed successfully!'
        }
        failure {
            echo ' Pipeline failed!'
        }
    }
}
