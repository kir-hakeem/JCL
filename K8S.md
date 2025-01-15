pipeline

    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: datamodels-staging-ingress
      namespace: datamodels-staging
      annotations:
        nginx.ingress.kubernetes.io/configuration-snippet: |
          proxy_set_header API_KEY "your-actual-api-key";
    spec:
      tls:
      - hosts:
        - datamodels-staging.imsglobal.org
        secretName: datamodels-staging-tls
      rules:
      - host: datamodels-staging.imsglobal.org
        http:
          paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: datamodels-staging
                port:
                  number: 80
