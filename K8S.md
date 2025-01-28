INGRESS

    apiVersion: v1
    kind: Service
    metadata:
      name: members-validator-app
      namespace: members-validator-namespace
    spec:
      type: LoadBalancer
      selector:
        app: members-validator-app
      ports:
      - protocol: TCP
        port: 80
        targetPort: 80
