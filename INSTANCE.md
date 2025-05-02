#### **1. Check if SSH key was added correctly**

    gcloud compute firewall-rules create allow-ssh-from-iap \
      --allow=tcp:22 \
      --source-ranges=35.235.240.0/20 \
      --target-tags=ssh-enabled \
      --description="Allow SSH from Cloud IAP"


Run this to view SSH metadata:

```bash
gcloud compute instances describe sandbox-1edtech-new --zone=us-central1-a --format="get(metadata.items)"
```

If no SSH key is set or your key is missing, re-add it:

```bash
gcloud compute ssh sandbox-1edtech-new --zone=us-central1-a --project=moodle-415914 --ssh-key-expire-after=1d
```

---

#### **2. Confirm SSH is installed in image**

If the boot disk is from snapshot, and it’s corrupted or missing SSH service, you won’t get in.

Attach a startup script to fix:

```bash
gcloud compute instances add-metadata sandbox-1edtech-new \
  --zone=us-central1-a \
  --metadata=startup-script='#! /bin/bash
    sudo apt update
    sudo apt install -y openssh-server
    sudo systemctl enable ssh
    sudo systemctl restart ssh'
```

---

#### **3. Try connecting from IAP Tunnel (if public IP fails)**

```bash
gcloud compute ssh sandbox-1edtech-new \
  --zone=us-central1-a \
  --tunnel-through-iap
```

---

#### **4. If still blocked, try serial console access**

Enable it to debug:

```bash
gcloud compute instances add-metadata sandbox-1edtech-new \
  --zone=us-central1-a \
  --metadata=serial-port-enable=1
```

Then connect:

```bash
gcloud compute connect-to-serial-port sandbox-1edtech-new --zone=us-central1-a
```

