Step 1: Create an RBAC Role and RoleBinding

Create a Role and RoleBinding that grants the required permissions to the new-jenkins2 service account in the ob30-cert-suite namespace.
Role Configuration (jenkins-role.yaml):

    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      namespace: ob30-cert-suite
      name: jenkins-role
    rules:
      - apiGroups: [""]
        resources: ["pods", "pods/log", "services", "endpoints"]
        verbs: ["get", "list", "watch"]
      - apiGroups: ["apps"]
        resources: ["replicasets", "deployments"]
        verbs: ["get", "list", "watch"]

RoleBinding Configuration (jenkins-rolebinding.yaml):

    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      namespace: ob30-cert-suite
      name: jenkins-rolebinding
    subjects:
      - kind: ServiceAccount
        name: new-jenkins2
        namespace: ob30-cert-suite
    roleRef:
      kind: Role
      name: jenkins-role
      apiGroup: rbac.authorization.k8s.io

Step 2: Apply the RBAC Configurations

Apply the above YAML configurations to your Kubernetes cluster:

    kubectl apply -f jenkins-role.yaml
    kubectl apply -f jenkins-rolebinding.yaml

Step 3: Verify RBAC Permissions

Verify that the new-jenkins2 service account can list pods:

    kubectl auth can-i list pods --as=system:serviceaccount:ob30-cert-suite:new-jenkins2 -n ob30-cert-suite

If the output is yes, the permissions are set correctly.
