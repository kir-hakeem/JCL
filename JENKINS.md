    metrics:
      receivers:
        hostmetrics:
          type: hostmetrics
      service:
        pipelines:
          default_pipeline:
            receivers: [hostmetrics]
    
    resource:
      type: generic_node
      labels:
        project_id: "digitalocean-422117"
        location: "custom"
        namespace: "external"
        node_id: "ansible.1edtech.org"

TEST

    export GOOGLE_APPLICATION_CREDENTIALS=/root/monitoring/google-sa-json.json
    gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
    gcloud projects describe digitalocean-422117
