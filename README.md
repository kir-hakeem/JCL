kubectl apply -f https://storage.googleapis.com/cloudsql-proxy-k8s/cloud-sql-proxy.yaml

            
            
            kubectl -n jenkins1 exec -it pod/jenkins1-0 -- rm -f /var/jenkins_home/plugins/<problematic-plugin>.jpi


QQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQQ
            JENKINS_OPTS: --argumentsRealm.passwd.admin=admin --argumentsRealm.roles.user=admin --argumentsRealm.roles.admin=admin -Djenkins.install.runSetupWizard=false


Steps to Edit the StatefulSet Configuration

Edit the StatefulSet YAML: Use the command to edit the configuration:

            kubectl edit statefulset jenkins1 -n jenkins1

Modify the JAVA_OPTS Environment Variable: Under the env section, add or modify the JAVA_OPTS variable to include the flag to disable plugin loading:

            - name: JAVA_OPTS
              value: "-Djenkins.install.runSetupWizard=false -Djenkins.plugin.disable=true"

Save the Changes: After editing, save the changes and exit the editor. This will update the StatefulSet.

Restart the StatefulSet: Ensure the updated configuration is applied by restarting the StatefulSet:

            kubectl rollout restart statefulset jenkins1 -n jenkins1


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

