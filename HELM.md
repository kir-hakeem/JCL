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

