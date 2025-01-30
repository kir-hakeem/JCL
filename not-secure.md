1️⃣ Check If SSL is Attached to the Load Balancer

    gcloud compute target-https-proxies describe https-proxy-1edtech --format="get(sslCertificates)"


2️⃣ Verify SSL Certificate Details

    curl -v https://purl.1edtech.org


3️⃣ Check If HTTP to HTTPS Redirect is Configured

    gcloud compute url-maps describe url-map-1edtech


If it doesn’t include a redirect, create one:

    gcloud compute url-maps add-path-matcher url-map-1edtech \
        --default-service backend-1edtech-specs \
        --path-matcher-name=http-to-https \
        --new-host-rule "hosts=purl.ledtech.org, path-matcher-name=http-to-https"


