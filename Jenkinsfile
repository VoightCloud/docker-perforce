String imageVersion = "1.0"
ArrayList<String> imageNames = ["perforce-base", "perforce-server", "perforce-git-fusion", "perforce-p4web", "perforce-proxy", "perforce-swarm"]
String imageRepo = "voight"
String nexusServer = "nexus.voight.org:9042"

podTemplate(label: "build",
        containers: [
            containerTemplate(name: 'docker',
                                image: 'docker:20.10.9',
                                alwaysPullImage: false,
                                ttyEnabled: true,
                                command: 'cat',
                                envVars: [containerEnvVar(key: 'DOCKER_HOST', value: "unix:///var/run/docker.sock")],
                                privileged: true),
            containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent:latest-jdk11', args: '${computer.jnlpmac} ${computer.name}')
        ],
        nodeSelector: 'kubernetes.io/arch=amd64'
    ) {
        node('build') {
            stage('Build') {
                container('docker'){
                    stage("Build"){
                        def scmVars = checkout([
                                $class           : 'GitSCM',
                                userRemoteConfigs: scm.userRemoteConfigs,
                                branches         : scm.branches,
                                extensions       : scm.extensions
                            ])
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



