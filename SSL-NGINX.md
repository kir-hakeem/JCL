### Steps to create the secret:

1. **Obtain Your Certificate and Key Files**:
   Ensure you have the certificate (`tls.crt`) and private key (`tls.key`) files on your local machine or accessible from the environment where you're running `kubectl`.

2. **Use the `kubectl` Command to Create the Secret**:
   You can create the secret directly using the following command in your terminal:

   ```bash
   kubectl create secret tls staging-ledtech-net-tla \
     --cert=/path/to/tls.crt \
     --key=/path/to/tls.key \
     --namespace=default
   ```

   Replace `/path/to/tls.crt` and `/path/to/tls.key` with the actual paths to your certificate and private key files.

3. **Verify the Secret Creation**:
   After running the command, verify that the secret has been created successfully by running:

   ```bash
   kubectl get secret staging-ledtech-net-tla --namespace=default
   ```

   This should return the secret details if it was successfully created.
