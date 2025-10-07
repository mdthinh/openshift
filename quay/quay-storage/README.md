# Create Quay

1. **Provision Quay Storage**

   * This step involves creating the Quay storage backend (e.g. an S3-compatible bucket).
   * After the bucket is created, retrieve the following details:

     * **Bucket name**
     * **Access key**
     * **Secret key**

2. **Deploy the Quay Registry**

   * Use the retrieved bucket information to populate the `values.yaml` file for the Quay Helm chart.
   * Example configuration:

     ```yaml
     objectStorage:
       bucket_name: quay-bucket-f469bdeb-62c3-4288-8b9e-82c412207850
       access_key: 0KTfMz4h4VRsTfEVJpJm
       secret_key: Qg6U2q18d/2R4vkneNwle2A5WOu2MtJ/vexVPAnT
       hostname: s3.openshift-storage.svc
       is_secure: true
       port: "443"
       storage_path: /datastorage/registry
     ```
