    receivers:
      hostmetrics:
        collection_interval: 60s
        scrapers:
          cpu:
          memory:
          disk:
          load:
          filesystem:
          network:
    
    exporters:
      googlecloud:
        project: digitalocean-422117
        user_agent: external-opentelemetry
    
    processors:
      batch:
    
    service:
      pipelines:
        metrics:
          receivers: [hostmetrics]
          processors: [batch]
          exporters: [googlecloud]
