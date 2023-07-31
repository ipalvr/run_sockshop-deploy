node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        // This builds the actual image; synonymous to docker build on the command line
        app = docker.build("compute/front-end:0.3.12")
    }

    stage('Push image') {
        //Finally, we'll push the image with two tags. 1st, the incremental build number from Jenkins, then 2nd, the 'latest' tag.
        try {
            docker.withRegistry('https://nexusdocker-master-maxwell-mweibeler.mweibeler.demo.twistlock.com', 'twistlock_creds') {
                app.push("0.3.12")
            }
        }catch(error) {
            echo "1st push failed, retrying"
            retry(5) {
                docker.withRegistry('https://nexusdocker-master-maxwell-mweibeler.mweibeler.demo.twistlock.com', 'twistlock_creds') {
                    app.push("0.3.12")
                }
            }
        }
    }

    stage('Deploy Sock-Shop') {
        sh 'kubectl delete --ignore-not-found=true -f complete-demo.yaml'
        sh 'kubectl apply -f complete-demo.yaml'
    }
}