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
                resources:
                  requests:
                    cpu: "200m"
                    memory: "256Mi"
                  limits:
                    cpu: "500m"
                    memory: "512Mi"
              - name: docker
                image: docker:dind
                securityContext:
                  privileged: true
                resources:
                  requests:
                    cpu: "500m"
                    memory: "1Gi"
                  limits:
                    cpu: "1"
                    memory: "2Gi"
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

