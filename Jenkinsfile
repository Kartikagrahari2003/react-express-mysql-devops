stage('Debug User') {
    steps {
        sh '''
        echo "===== WHOAMI ====="
        whoami

        echo "===== ID ====="
        id

        echo "===== GROUPS ====="
        groups

        echo "===== DOCKER SOCKET ====="
        ls -l /var/run/docker.sock

        echo "===== DOCKER TEST ====="
        docker ps
        '''
    }
}