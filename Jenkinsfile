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
        app = docker.build("gcr.io/mystic-impulse-245222/image2")
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
             sh 'gcloud container clusters create --zone us-central1-a --network sanvpc mycluster2 --num-nodes=2'
        
    }

    stage('Deploy the dokcer base image in Kubernetes') {
             sh 'kubectl apply -f san.yml'
             sleep 120
    }

    
    stage('Delete Service') {
              sh 'kubectl delete service myservice2'
              sleep 60
    }
     stage('Delete cluster'){
            sh 'gcloud container clusters delete mycluster2 --zone us-central1-a --quiet'
     }
    
    
    stage('Delete container images'){
            sh 'gcloud container images delete gcr.io/mystic-impulse-245222/image2  --quiet'
     }
    
    
}
