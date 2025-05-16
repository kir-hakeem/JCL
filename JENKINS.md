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
    header('Content-Type: application/json');
    
    // CPU load average (1, 5, 15 min)
    $load = sys_getloadavg();
    
    // Memory usage
    $memInfo = file_get_contents("/proc/meminfo");
    preg_match("/MemTotal:\s+(\d+)/", $memInfo, $memTotal);
    preg_match("/MemAvailable:\s+(\d+)/", $memInfo, $memAvailable);
    
    // Disk usage
    $diskTotal = disk_total_space("/");
    $diskFree  = disk_free_space("/");
    
    // Uptime
    $uptime = shell_exec("uptime -p");
    
    // Network stats (eth0 example)
    $rx = trim(shell_exec("cat /sys/class/net/eth0/statistics/rx_bytes"));
    $tx = trim(shell_exec("cat /sys/class/net/eth0/statistics/tx_bytes"));
    
    echo json_encode([
        'cpu' => [
            'load_1_min' => $load[0],
            'load_5_min' => $load[1],
            'load_15_min' => $load[2],
        ],
        'memory' => [
            'total_kb' => $memTotal[1],
            'available_kb' => $memAvailable[1],
        ],
        'disk' => [
            'total_bytes' => $diskTotal,
            'free_bytes' => $diskFree,
        ],
        'uptime' => trim($uptime),
        'network' => [
            'rx_bytes' => $rx,
            'tx_bytes' => $tx,
        ],
    ]);
