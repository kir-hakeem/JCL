✅ Step 1: Install the Secret Store CSI Driver

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: ob30-cert-suite-deployment
      namespace: ob30-cert-suite
      labels:
        app: ob30-cert-suite
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
          serviceAccountName: "{{ .Values.serviceAccount.name }}"
          containers:
            - name: ob30-app
              image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
              ports:
                - containerPort: 8080
              env:
                - name: ENVIRONMENT
                  value: "{{ .Values.environment }}"
              volumeMounts:
                - name: secrets-store
                  mountPath: "/mnt/secrets-store"
                  readOnly: true
          volumes:
            - name: secrets-store
              csi:
                driver: secrets-store.csi.k8s.io
                readOnly: true
                volumeAttributes:
                  secretProviderClass: "{{ .Values.secretProviderClass }}"


Run the following command to install the driver via Helm:

    helm repo add secrets-store-csi-driver https://kubernetes-sigs.github.io/secrets-store-csi-driver/charts
    helm repo update
    
    helm install csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver \
      --namespace kube-system \
      --set syncSecret.enabled=true \
      --set enableSecretRotation=true

🔎 Verify installation:

    kubectl get pods -n kube-system | grep csi

Look for pods like secrets-store-csi-driver-* running successfully.
✅ Step 2: Install GCP Secret Manager Provider

    kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/secrets-store-csi-driver-provider-gcp/main/deploy/provider-gcp-plugin.yaml

🔎 Verify provider installation:

    kubectl get pods -n kube-system | grep provider-gcp




✅ Solution Steps
    
    helm upgrade --install ob30-cert-suite ./ob30-cert-suite -n ob30-cert-suite --values values.yaml



Step 1: Verify Secret in Secret Manager

Make sure the secret exists and is accessible:

    gcloud secrets describe verifiable-credentials-db-username --project=581155432306

🔔 Ensure your service account (secrets-init@digitalocean-422117.iam.gserviceaccount.com) has the Secret Manager Secret Accessor role:
    
    gcloud projects add-iam-policy-binding 581155432306 \
      --member="serviceAccount:secrets-init@digitalocean-422117.iam.gserviceaccount.com" \
      --role="roles/secretmanager.secretAccessor"

Step 2: Update the SecretProviderClass Configuration

Check if your SecretProviderClass is correctly referencing the secret:
    
    apiVersion: secrets-store.csi.x-k8s.io/v1
    kind: SecretProviderClass
    metadata:
      name: gcp-secrets
      namespace: ob30-cert-suite
    spec:
      provider: gcp
      parameters:
        secrets: |
          - resourceName: "projects/581155432306/secrets/verifiable-credentials-db-username/versions/latest"
            path: "db-username"

✅ Apply the changes:

    kubectl apply -f secretproviderclass.yaml -n ob30-cert-suite

Step 3: Update Deployment with Correct Volume Mount

Make sure your deployment references the SecretProviderClass:
    
    volumes:
      - name: secrets-store
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
            secretProviderClass: "gcp-secrets"
    
    containers:
      - name: ob30-app
        image: openjdk:17-jdk-slim
        volumeMounts:
          - name: secrets-store
            mountPath: "/mnt/secrets-store"
        env:
          - name: DB_USERNAME
            valueFrom:
              secretKeyRef:
                name: verifiable-credentials-db-username
                key: db-username

Apply the deployment:

    kubectl apply -f deployment.yaml -n ob30-cert-suite
    kubectl rollout restart deployment ob30-cert-suite-deployment -n ob30-cert-suite



COMMAND

    kubectl get namespace ob30-cert-suite -o json | jq '.spec.finalizers = []' | \
    kubectl replace --raw "/api/v1/namespaces/ob30-cert-suite/finalize" -f -


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
      gcpServiceAccount: secrets-init@digitalocean-422117.iam.gserviceaccount.com
    
    secretManager:
      projectId: "581155432306"
      usernameSecret: "verifiable-credentials-ob-username"
    
    service:
      name: ob30-cert-suite-service
      port: 80
      targetPort: 8080

    ingress:
      enabled: true
      className: "nginx"
      hosts:
        - host: ob30.local
          paths:
            - path: /
              pathType: Prefix
      tls: []
    autoscaling:
      enabled: false
      minReplicas: 1
      maxReplicas: 3
      targetCPUUtilizationPercentage: 80



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
                  ports:
                    - containerPort: {{ .Values.service.targetPort }}
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
      name: {{ .Values.service.name }}
      namespace: ob30-cert-suite
    spec:
      selector:
        app: ob30-cert-suite
      ports:
        - protocol: TCP
          port: {{ .Values.service.port }}
          targetPort: {{ .Values.service.targetPort }}


8️⃣ templates/tests/test-connection.yaml (Fixed Error)

    apiVersion: v1
    kind: Pod
    metadata:
      name: test-connection
      namespace: ob30-cert-suite
      labels:
        app: ob30-cert-suite
    spec:
      containers:
        - name: curl
          image: curlimages/curl:latest
          command: ["curl", "-v", "{{ .Values.service.name }}:{{ .Values.service.port }}"]
      restartPolicy: Never


templates/ingress.yaml
    
    {{- if .Values.ingress.enabled }}
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: ob30-cert-suite-ingress
      namespace: ob30-cert-suite
      annotations:
        kubernetes.io/ingress.class: {{ .Values.ingress.className }}
    spec:
      rules:
        {{- range .Values.ingress.hosts }}
        - host: {{ .host }}
          http:
            paths:
              {{- range .paths }}
              - path: {{ .path }}
                pathType: {{ .pathType }}
                backend:
                  service:
                    name: {{ $.Values.service.name }}
                    port:
                      number: {{ $.Values.service.port }}
              {{- end }}
        {{- end }}
      {{- if .Values.ingress.tls }}
      tls:
        {{- toYaml .Values.ingress.tls | nindent 4 }}
      {{- end }}
    {{- end }}
    
templates/hpa.yaml
    
    {{- if .Values.autoscaling.enabled }}
    apiVersion: autoscaling/v2
    kind: HorizontalPodAutoscaler
    metadata:
      name: ob30-cert-suite-hpa
      namespace: ob30-cert-suite
    spec:
      scaleTargetRef:
        apiVersion: apps/v1
        kind: Deployment
        name: ob30-cert-suite-deployment
      minReplicas: {{ .Values.autoscaling.minReplicas }}
      maxReplicas: {{ .Values.autoscaling.maxReplicas }}
      metrics:
        - type: Resource
          resource:
            name: cpu
            target:
              type: Utilization
              averageUtilization: {{ .Values.autoscaling.targetCPUUtilizationPercentage }}
    {{- end }}

