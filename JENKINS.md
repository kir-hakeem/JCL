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
