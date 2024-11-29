      kubectl run curlpod --image=radial/busyboxplus:curl -i --tty --rm
      curl http://10.24.5.21:8080/login

                
                
                
  
                
                kubectl exec --namespace jenkins1 -it svc/jenkins1 -c jenkins -- /bin/cat /run/secrets/additional/chart-admin-password



Steps to Update Jenkins Plugins

Access the Jenkins Container: From your Kubernetes command, you're already inside the Jenkins container. If not, you can re-enter using:

        kubectl -n new-jenkins3 exec -it jenkinsnew-3-0 -- /bin/bash

Download jenkins-cli.jar: Inside the container, download the jenkins-cli.jar file. You need to know the Jenkins URL (e.g., http://localhost:8080):

    curl -O http://localhost:8080/jnlpJars/jenkins-cli.jar

Update Plugins: Use the jenkins-cli.jar to update all plugins. Run the following command:

        java -jar jenkins-cli.jar -s http://localhost:8080/ safe-restart

If you want to update specific plugins, use:

        java -jar jenkins-cli.jar -s http://localhost:8080/ install-plugin <plugin-name> --restart

List Installed Plugins (Optional): To see the list of installed plugins:

        java -jar jenkins-cli.jar -s http://localhost:8080/ list-plugins

Restart Jenkins: After updating plugins, restart Jenkins to apply changes:

        java -jar jenkins-cli.jar -s http://localhost:8080/ restart

