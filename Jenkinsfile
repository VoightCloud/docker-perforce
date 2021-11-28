String imageVersion = "1.9"
ArrayList<String> imageNames = ["perforce-base", "perforce-server", "perforce-git-fusion", "perforce-p4web", "perforce-proxy", "perforce-swarm"]
String imageRepo = "voight"
String nexusServer = "nexus.voight.org:9042"

stage("Build"){
    podTemplate(label: "build",
        containers: [
            containerTemplate(name: 'docker',
                                image: 'docker:20.10.9',
                                alwaysPullImage: false,
                                ttyEnabled: true,
                                command: 'cat',
                                envVars: [containerEnvVar(key: 'DOCKER_HOST', value: "unix:///var/run/docker.sock")],
                                privileged: true),
            containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent:latest-jdk11', args: '${computer.jnlpmac} ${computer.name}'),
        ],
        volumes: [
                hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
        ],
        nodeSelector: 'kubernetes.io/arch=amd64'
    ) {
        node('build') {
            stage('Checkout') {
                def scmVars = checkout([
                        $class           : 'GitSCM',
                        userRemoteConfigs: scm.userRemoteConfigs,
                        branches         : scm.branches,
                        extensions       : scm.extensions
                    ])
            }

            stage("Docker build"){
                container('docker'){
                    for(imageName in imageNames) {
                        docker.withRegistry("https://${nexusServer}", 'NexusDockerLogin') {
                            dir(imageName) {
                                image = docker.build("${imageRepo}/${imageName}:${imageVersion}-amd64")
                                image.push("${imageVersion}-amd64")
                                image.push("latest")
                            }
                        }
                    }
                }
            }
        }
    }
}



