ingress

    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: jenkins-ingress
      namespace: new-jenkins1
      annotations:
        nginx.ingress.kubernetes.io/auth-url: "https://jenkinsnew1.1edtech.org/oauth2/auth"
        nginx.ingress.kubernetes.io/auth-signin: "https://jenkinsnew1.1edtech.org/oauth2/start?rd=$request_uri"
        nginx.ingress.kubernetes.io/ssl-redirect: "true"
        nginx.ingress.kubernetes.io/backend-protocol: "HTTP"
    spec:
      rules:
      - host: jenkinsnew1.1edtech.org
        http:
          paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: jenkinsnew-1
                port:
                  number: 8080
      tls:
      - hosts:
        - jenkinsnew1.1edtech.org
        secretName: jenkinsnew-1
