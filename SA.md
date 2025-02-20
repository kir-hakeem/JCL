YAML 

    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: secrets-init
      namespace: ob30-cert-suite
      annotations:
        iam.gke.io/gcp-service-account: secrets-init@digitalocean-422117.iam.gserviceaccount.com
