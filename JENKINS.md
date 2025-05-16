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
