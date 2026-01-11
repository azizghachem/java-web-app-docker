node {

    stage('SCM Checkout'){
        git url: 'https://github.com/MithunTechnologiesDevOps/java-web-app-docker.git', branch: 'master'
    }

    stage("Maven Clean Package"){
        def mavenHome = tool name: "Maven-3.5.6", type: "maven"
        sh "${mavenHome}/bin/mvn clean package"
    } 

    stage('Build Docker Image'){
        sh 'docker build -t azizghachem/java-web-app .'
    }

    stage('Push Docker Image'){
        withCredentials([usernamePassword(credentialsId: 'Docker_Hub_Pwd', 
                                         usernameVariable: 'DOCKER_USER', 
                                         passwordVariable: 'DOCKER_PASS')]) {
            sh """
                echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                docker push azizghachem/java-web-app
            """
        }
    }

    stage('Run Docker Image In Dev Server'){
        def dockerRun = 'docker run -d -p 8080:8080 --name java-web-app azizghachem/java-web-app'

        sshagent(['Docker_SERVER']) {
            // Stop le conteneur s'il existe
            sh 'ssh -o StrictHostKeyChecking=no vboxuser@192.168.1.94 "docker stop java-web-app || true"'
            
            // Supprime le conteneur s'il existe
            sh 'ssh vboxuser@192.168.1.94 "docker rm java-web-app || true"'
            
            // Lancer le nouveau conteneur
            sh "ssh vboxuser@192.168.1.94 '${dockerRun}'"
        }
    }

}
