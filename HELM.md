📝 Files Content
1️⃣ Chart.yaml

    apiVersion: v2
    name: ob30-cert-suite
    version: 0.1.0
    description: OB30 Cert Suite Deployment with Secret Manager integration

2️⃣ values.yaml

    image:
      repository: openjdk
      tag: "17-jdk-slim"
      pullPolicy: IfNotPresent
    
    serviceAccount:
      name: ob30-sa
      gcpServiceAccount: secrets-init@digitalocean-422117.jam.gserviceaccount.com
    
    secretManager:
      projectId: "581155432306"
      usernameSecret: "verifiable-credentials-ob-username"

3️⃣ templates/namespace.yaml
    
    apiVersion: v1
    kind: Namespace
    metadata:
      name: ob30-cert-suite

4️⃣ templates/serviceaccount.yaml
    
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: {{ .Values.serviceAccount.name }}
      namespace: ob30-cert-suite
      annotations:
        iam.gke.io/gcp-service-account: "{{ .Values.serviceAccount.gcpServiceAccount }}"

5️⃣ templates/secretproviderclass.yaml

    apiVersion: secrets-store.csi.x-k8s.io/v1
    kind: SecretProviderClass
    metadata:
      name: gcp-secrets
      namespace: ob30-cert-suite
    spec:
      provider: gcp
      parameters:
        secrets: |
          - resourceName: "projects/{{ .Values.secretManager.projectId }}/secrets/{{ .Values.secretManager.usernameSecret }}/versions/latest"
            fileName: "db-username"

6️⃣ templates/deployment.yaml
    
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: ob30-cert-suite-deployment
      namespace: ob30-cert-suite
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: ob30-cert-suite
      template:
        metadata:
          labels:
            app: ob30-cert-suite
        spec:
          serviceAccountName: {{ .Values.serviceAccount.name }}
          containers:
            - name: ob30-app
              image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
              imagePullPolicy: {{ .Values.image.pullPolicy }}
              volumeMounts:
                - name: secrets-store
                  mountPath: "/mnt/secrets-store"
                  readOnly: true
              env:
                - name: DB_USERNAME
                  valueFrom:
                    secretKeyRef:
                      name: gcp-secrets
                      key: db-username
          volumes:
            - name: secrets-store
              csi:
                driver: secrets-store.csi.k8s.io
                readOnly: true
                volumeAttributes:
                  secretProviderClass: gcp-secrets

7️⃣ templates/service.yaml
    
    apiVersion: v1
    kind: Service
    metadata:
      name: ob30-cert-suite-service
      namespace: ob30-cert-suite
    spec:
      selector:
        app: ob30-cert-suite
      ports:
        - protocol: TCP
          port: 80
          targetPort: 8080

