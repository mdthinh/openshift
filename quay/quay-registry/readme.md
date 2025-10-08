✅ Tạo DATABASE_SECRET_KEY
        openssl rand -hex 32
        75843c0bd29a496a66cba160dee32d11f9e92e14e1f200b49150c5ba844d02b5


✅ Cài AWS CLI
yum install awscli -y
    🔧 Cấu hình AWS CLI profile cho NooBaa
            aws configure --profile noobaa                      # sau đó nhập ACCESS_KEY và SECRET_ACCESS_KEY lấy được ở dưới
    📂 Liệt kê bucket
            aws --endpoint-url https://10.5.5.15:31965 \
                --no-verify-ssl \
                --profile noobaa \
                s3 ls
    📦 Tạo thêm bucket
            aws --endpoint-url https://10.5.5.15:31965 \
                --no-verify-ssl \
                --profile noobaa \
                s3 mb s3://quay-bucket


✅ Tạo DISTRIBUTED_STORAGE_CONFIG
    📥kiểm tra thông tin ODF noobaas
        oc get noobaas -n openshift-storage

    📥 Kiểm tra noobaa-admin secret (nếu dùng default key)
        oc get secret noobaa-admin -n openshift-storage -o jsonpath='{.data.AWS_ACCESS_KEY_ID}' | base64 -d                 #   cJY0K5NSOEXNoBFoscLK
        oc get secret noobaa-admin -n openshift-storage -o jsonpath='{.data.AWS_SECRET_ACCESS_KEY}' | base64 -d             #   3sOEb2KT3HtboFTtYRFV83fOPrkhUnUgv0nGRlFK

🛠 Lệnh tạo config-bundle-secret từ file config.yaml
    oc new-project quay-enterprise
    oc -n quay-enterprise create secret generic config-bundle-secret \
  --from-file=config.yaml=./config.yaml


  oc create secret generic config-bundle-secret --from-file=config.yaml=config.yaml -n quay-enterprise


  curl -k -X POST https://quay.apps.prod-ocp.mdt.local/api/v1/user/initialize \
  -H "Content-Type: application/json" \
  -d '{
    "username": "quayadmin",
    "password": "Mdt@1311",
    "email": "admin@prod-ocp.mdt.local"
  }'


vim /etc/containers/registries.conf

[[registry]]
location = "quay.apps.prod-ocp.mdt.local"
insecure = true

podman push --tls-verify=false quay.apps.prod-ocp.mdt.local/quayadmin/ocp:nginx-0.1


## Config

```yaml
SERVER_HOSTNAME: quay.apps.prod-ocp.mdt.local
PREFERRED_URL_SCHEME: https
SUPER_USERS:
  - quayadmin
FEATURE_USER_INITIALIZE: true
DATABASE_SECRET_KEY: "75843c0bd29a496a66cba160dee32d11f9e92e14e1f200b49150c5ba844d02b5"
REDIS_HOSTNAME: redis
FEATURE_UI_V2: true
FEATURE_REPO_MIRROR: true
USER_EVENTS_CONFIG:
  enabled: true

DISTRIBUTED_STORAGE_CONFIG:
  default:
    - RHOCSStorage
    - access_key: kEITmOJY4Wzb9PcS3JMp
      bucket_name: quay-image-blob-9ed858e7-2c71-45ba-b00b-b0a24eb51f5f
      hostname: s3.openshift-storage.svc
      is_secure: true
      port: "443"
      secret_key: 2nEEEKFsIhRBupEZM8ibmTj1p2BjLQNhbCsVRCZc
      storage_path: /datastorage/registry
DISTRIBUTED_STORAGE_DEFAULT_LOCATIONS: []
DISTRIBUTED_STORAGE_PREFERENCE:
  - default
FEATURE_USER_CREATION: true
AUTHENTICATION_TYPE: Database
AZURE_LOGIN_CONFIG:
    CLIENT_ID: "937c0fec-97f2-4ecc-86ad-753746b6388e"
    CLIENT_SECRET: "b~48Q~9hAz8mvLehFR7kNbinfwvQvRFa-pD3cazH"
    OIDC_SERVER: https://login.microsoftonline.com/e69dc0dd-5fc0-4729-b9ac-30c9100ed271/v2.0/
    SERVICE_NAME: EntraID  
```