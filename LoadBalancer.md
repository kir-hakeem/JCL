1️⃣ Enable Cloud CDN & Load Balancing API

    gcloud services enable compute.googleapis.com
    gcloud services enable cloudcdn.googleapis.com


2️⃣ Create a Backend Bucket

    gcloud compute backend-buckets create backend-1edtech-specs \
        --gcs-bucket-name=1edtech-specs-poc \
        --enable-cdn


3️⃣ Create a URL Map

    gcloud compute url-maps create url-map-1edtech \
    --default-backend-bucket=backend-1edtech-specs


4️⃣ Create a Target HTTPS Proxy

    gcloud compute target-https-proxies create https-proxy-1edtech \
    --url-map=url-map-1edtech \
    --ssl-certificates=ssl-cert-1edtech


5️⃣ Reserve a Global Static IP Address

    gcloud compute addresses create lb-ipv4-1edtech \
    --global


command to get the reserved IP

    gcloud compute addresses describe lb-ipv4-1edtech --global --format="get(address)"


7️⃣ Create a Forwarding Rule


    gcloud compute forwarding-rules create fr-1edtech \
    --global \
    --target-https-proxy=https-proxy-1edtech \
    --address=lb-ipv4-1edtech \
    --ports=443


