parameters {
    stringParam(defaultValue: 'production', description: '', name: 'DEPLOY_ENVIRONMENT')
}
def projectName = 'rightstuff-176212';
def imageName = 'gcr.io/rightstuff-176212/jenkins-slave:docker.master'

podTemplate(cloud: 'local cluster', label: 'docker',
    containers: [containerTemplate(name: 'docker', image: imageName, command: 'cat', ttyEnabled: true, alwaysPullImage: true) ],
    volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]) {
    node('docker') {
        container('docker') {
            checkout scm

            stage('Deploy Production') {
                sh 'kubectl --namespace=production apply -f services/'
                sh "kubectl --namespace=production apply -f production/"
                sh "echo http://`kubectl --namespace=production get service/inv-frontend --output=json | jq -r '.status.loadBalancer.ingress[0].ip'` > inv-frontend"
            }
        }
    }
}