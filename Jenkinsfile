pipeline {
    agent any

    tools {
        maven 'M2_HOME'    // Nom exact de Maven configuré dans Jenkins
        jdk 'JAVA_HOME'     // Nom exact du JDK configuré dans Jenkins
    }

    environment {
        GIT_REPO_URL = 'https://github.com/MeguebliMohamed/Foyer.git'
        SONARQUBE_SERVER = 'SonarQube'                // Nom du serveur SonarQube dans Jenkins
        SONARQUBE_TOKEN = credentials('sonar-token')  // ID du token SonarQube (Secret Text)
    }

    stages {
        stage('Pull from Git') {
            steps {
                echo '📥 Clonage du projet depuis GitHub...'
                git branch: 'main', url: "${GIT_REPO_URL}"
            }
        }

        stage('Clean Project') {
            steps {
                echo '🧹 Nettoyage du projet...'
                sh 'mvn clean'
            }
        }

        stage('Compile Project') {
            steps {
                echo '⚙ Compilation du projet...'
                sh 'mvn compile'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo '🔍 Analyse du code avec SonarQube...'
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.projectKey=tp-foyer \
                        -Dsonar.host.url=${env.SONAR_HOST_URL} \
                        -Dsonar.login=${SONARQUBE_TOKEN}
                    """
                    echo "🔗 Résultats SonarQube : ${env.SONAR_HOST_URL}/dashboard?id=tp-foyer"
                }
            }
        }

        stage('Build JAR') {
            steps {
                echo '📦 Génération du fichier JAR...'
                sh 'mvn package -DskipTests'
            }
        }

        stage('Archive Artifact') {
            steps {
                echo '🗃 Archivage du JAR généré...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline exécuté avec succès !'
        }
        failure {
            echo '❌ Le pipeline a échoué. Vérifiez les logs Jenkins.'
        }
    }
}