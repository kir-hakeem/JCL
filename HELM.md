deployment
    
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: oauth2-proxy
      namespace: new-jenkins1
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: oauth2-proxy
      template:
        metadata:
          labels:
            app: oauth2-proxy
        spec:
          containers:
          - name: oauth2-proxy
            image: quay.io/oauth2-proxy/oauth2-proxy:v7.6.0
            args:
              - --provider=google
              - --email-domain=1edtech.org
              - --upstream=http://jenkinsnew-1:8080
              - --http-address=0.0.0.0:4180
              - --redirect-url=https://jenkinsnew1.1edtech.org/oauth2/callback
              - --cookie-secure=true
              - --cookie-secret=$(OAUTH2_PROXY_COOKIE_SECRET)
              - --client-id=$(OAUTH2_PROXY_CLIENT_ID)
              - --client-secret=$(OAUTH2_PROXY_CLIENT_SECRET)
            env:
            - name: OAUTH2_PROXY_CLIENT_ID
              valueFrom:
                secretKeyRef:
                  name: oauth2-proxy-secret
                  key: client-id
            - name: OAUTH2_PROXY_CLIENT_SECRET
              valueFrom:
                secretKeyRef:
                  name: oauth2-proxy-secret
                  key: client-secret
            - name: OAUTH2_PROXY_COOKIE_SECRET
              valueFrom:
                secretKeyRef:
                  name: oauth2-proxy-secret
                  key: cookie-secret
            ports:
            - containerPort: 4180
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: oauth2-proxy
      namespace: new-jenkins1
    spec:
      selector:
        app: oauth2-proxy
      ports:
        - protocol: TCP
          port: 4180
          targetPort: 4180
