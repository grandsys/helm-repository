podTemplate(
    label: 'helm-repo', 
    containers: [
        containerTemplate(name: 'jnlp', image: 'henryrao/jnlp-slave', args: '${computer.jnlpmac} ${computer.name}', alwaysPullImage: true)
    ],
    volumes: [
        hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
				persistentVolumeClaim(claimName: 'helm-repository', mountPath: '/var/helm/repo', readOnly: false)
    ]) {
           
    node('helm-repo') {
        stage('checkout') {
            checkout scm
        }
        stage('merge') {
            try {
								sleep 100000
            } catch (e) {
                echo "${e}"
                currentBuild.result = FAILURE
            }
            finally {
                echo 'well done'
            }
        }
    } 
}
