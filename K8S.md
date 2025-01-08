pipeline

    agent {
        kubernetes {
            label 'jenkins-agent'
            yaml '''
            kind: Pod
            apiVersion: v1
            spec:
              containers:
              - name: jnlp
                image: jenkins/inbound-agent:4.10-3
                args: ["$(JENKINS_SECRET)", "$(JENKINS_NAME)"]
              - name: docker
                image: docker:dind
                securityContext:
                  privileged: true
                volumeMounts:
                  - name: docker-socket
                    mountPath: /var/run/docker.sock
              volumes:
              - name: docker-socket
                hostPath:
                  path: /var/run/docker.sock
            '''
        }
    }
