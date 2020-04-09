/*properties([pipelineTriggers([githubPush()])])*/

node {
    def app

    stage('Clone repository') {
        /* repository cloned to our Jenkins workspace */

        checkout scm
    }
    stage('fix docker: Got permission denied while trying to connect to the Docker daemon socket') {
             sh 'sudo chmod 666 /var/run/docker.sock'
    }

    stage('Build dokcer base image locally') {
        /* To builds the dockerimage */
        //update your GCR registry URI
        app = docker.build("gcr.io/mystic-impulse-245222/soloo0000")
    }

    stage('Test docker base image') {
       

        app.inside {
            sh 'echo "Container image successfully created "'
        }
    }

    stage('Push docker base image from local to GCR') {
        /* Finally, we'll push the image */
        //docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
        // update your GCR registry URI and jenkins crendential paramater
        docker.withRegistry('https://gcr.io', 'gcr:mystic-impulse-245222')    {
            //app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage('create K8s cluster') {
             sh 'gcloud container clusters create --zone us-central1-a --network sanvpc mycluster --num-nodes=2'
        
    }

    stage('Deploy the dokcer base image in Kubernetes') {
             sh 'kubectl create deployment mydep --image=gcr.io/mystic-impulse-245222/soloo0000'
    }

    stage('Create a LoadBalancer Service to expose the url'){ 
             sh 'kubectl expose deployment mydep --type=LoadBalancer --port 80 --target-port 8080'
             sleep 120 // seconds
    }
    
    stage('Get Service or the External IP'){
             sh 'kubectl get service'
             sleep 100
    }

    stage('Delete Service') {
              sh 'kubectl delete service mydep'
              sleep 60
    }
     stage('Delete cluster'){
            sh 'gcloud container clusters delete mycluster --zone us-central1-a --quiet'
     }
}
