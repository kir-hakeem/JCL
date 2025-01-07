To resolve the issue in Step 2 (allowing Jenkins to access the Docker socket on Kubernetes), you need to configure the Jenkins agent Pod Template or Kubernetes Deployment to mount the Docker socket. Here's how to achieve this:
Option 1: Update the Jenkins Kubernetes Pod Template

    Access Jenkins Kubernetes Cloud Configuration:
        Navigate to Manage Jenkins > Manage Nodes and Clouds > Configure Clouds.
        Click on your Kubernetes cloud configuration.

    Update the Pod Template:
        Find the Pod Template used for running Jenkins jobs.
        Add a Volume and a Volume Mount to the container configuration to bind the Docker socket.

    Configuration:

        Add a volume for /var/run/docker.sock:

volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock
      type: File

Mount the volume inside the Jenkins agent container:

        containers:
          - name: jnlp
            volumeMounts:
              - name: docker-sock
                mountPath: /var/run/docker.sock

    Save and Apply Changes:
        Save the updated Kubernetes Pod Template configuration.
        Restart the Jenkins agents (delete running agent pods to allow the system to recreate them with the new configuration).

Option 2: Modify the Kubernetes YAML File

If you’re managing Jenkins agents directly with a YAML configuration file, update the configuration to include the Docker socket.

Edit the Deployment or Pod YAML File: Find the YAML file managing the Jenkins agents. Add a volume and volume mount for the Docker socket.

Example Updated YAML File:

        apiVersion: v1
        kind: Pod
        metadata:
          name: jenkins-agent
          namespace: ob30-cert-suite
        spec:
          containers:
          - name: jenkins-agent
            image: jenkins/inbound-agent:4.13-2
            volumeMounts:
            - name: docker-sock
              mountPath: /var/run/docker.sock
          volumes:
          - name: docker-sock
            hostPath:
              path: /var/run/docker.sock
              type: File

Apply the YAML File: Apply the updated configuration:

        kubectl apply -f jenkins-agent.yaml
