From the logs:

The kube-dns service appears to be running and is configured to handle DNS queries for cluster.local. This indicates that DNS resolution for internal cluster services is working.
The error in the kubectl exec command indicates that the nslookup command is not installed in the container. This is common for lightweight containers where tools like nslookup are not included.

Steps to Troubleshoot and Resolve:
1. Use an Alternative Pod with nslookup

Since the container does not have nslookup, use a pod with debugging utilities (like busybox or ubuntu) to test DNS resolution:

    kubectl -n kube-system run debug --rm -it --image=busybox --restart=Never -- sh

Inside the busybox pod, run:

    nslookup 34.170.235.39

2. Test DNS Resolution Internally

Verify DNS resolution for the IP address 34.170.235.39 and the hostname (if available). If DNS resolution fails here, the issue lies with the kube-dns configuration.
3. Verify Upstream Nameserver

Check the kube-dns deployment logs for the configured upstream nameservers. The following line from your logs:

UpstreamNameservers: []

Indicates that no upstream DNS servers are set. This means kube-dns cannot resolve external addresses.
Update Upstream Nameservers

Modify the kube-dns configuration to add upstream DNS servers (e.g., Google's 8.8.8.8):

Edit the kube-dns ConfigMap:

    kubectl -n kube-system edit configmap kube-dns

Add the following entry to include upstream DNS servers:

stubDomains:
  default.svc.cluster.local: ["8.8.8.8", "8.8.4.4"]

Save and exit.

Restart the kube-dns pods to apply changes:

    kubectl -n kube-system rollout restart deployment kube-dns

4. Verify NGINX Configuration

If DNS resolution issues persist, you can bypass kube-dns by explicitly setting a resolver in the NGINX configuration. Modify the ingress controller ConfigMap:

Edit the ConfigMap:

    kubectl -n ingress-nginx edit configmap nginx-configuration

Add or modify the resolver entry:

resolver: 8.8.8.8;

Restart the ingress controller:

    kubectl -n ingress-nginx rollout restart deployment ingress-nginx-controller

5. Verify Application Connectivity

    Once the changes are applied, test the application connectivity via the ingress again.
    Review ingress controller logs and application logs for further debugging.
