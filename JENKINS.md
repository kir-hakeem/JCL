ENV

    [Service]
    Environment="GOOGLE_APPLICATION_CREDENTIALS=/root/monitoring/google-sa-json.json"
    Environment="GOOGLE_CLOUD_PROJECT=digitalocean-422117"

    
CONFIG
        
    logging:
      receivers:
        syslog:
          type: files
          include_paths:
          - /var/log/messages
          - /var/log/syslog
    
    metrics:
      receivers:
        hostmetrics:
          type: hostmetrics
          collection_interval: 60s
    
    service:
      pipelines:
        metrics:
          receivers: [hostmetrics]
        logs:
          receivers: [syslog]
    
    resource:
      type: generic_node
      labels:
        project_id: "digitalocean-422137"
        location: "us-central1"
        namespace: "external"
        node_id: "ansible.1edtech.org"

TEST

    export GOOGLE_APPLICATION_CREDENTIALS=/root/monitoring/google-sa-json.json
    gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
    gcloud projects describe digitalocean-422117

PHP SCRIPT FOR MONITORING 
    
    <?php
    echo "<h1>Server Health Monitor</h1>";
    echo "<pre>";
    echo "Hostname: " . gethostname() . "\n";
    
    // Uptime
    echo "Uptime:\n";
    system("uptime");
    
    // CPU Load
    echo "\nCPU Load:\n";
    system("top -bn1 | grep 'load average'");
    
    // Memory Usage
    echo "\nMemory Usage:\n";
    system("free -h");
    
    // Disk Usage
    echo "\nDisk Usage:\n";
    system("df -h /");
    
    echo "</pre>";
    ?>
