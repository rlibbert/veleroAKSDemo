# veleroAKSDemo

rl@Gurpreet RLWorkspace % AZURE_BACKUP_SUBSCRIPTION_NAME='rlibbert internal'          
rl@Gurpreet RLWorkspace % echo $AZURE_BACKUP_SUBSCRIPTION_NAME                              
rlibbert internal


rl@Gurpreet RLWorkspace % AZURE_BACKUP_SUBSCRIPTION_ID=$(az account list --query="[?name=='$AZURE_BACKUP_SUBSCRIPTION_NAME'].id | [0]" -o tsv)
rl@Gurpreet RLWorkspace % echo $AZURE_BACKUP_SUBSCRIPTION_ID
b1247c54-82da-45a1-b901-8adc221e0503

rl@Gurpreet RLWorkspace % AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
rl@Gurpreet RLWorkspace % az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location EastUS

{
  "id": "/subscriptions/b1247c54-82da-45a1-b901-8adc221e0503/resourceGroups/Velero_Backups",
  "location": "eastus",
  "managedBy": null,
  "name": "Velero_Backups",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}


rl@Gurpreet RLWorkspace % AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"

rl@Gurpreet RLWorkspace % az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

rl@Gurpreet RLWorkspace % AZURE_CLIENT_SECRET=`az ad sp create-for-rbac --name "robsvelerosp" --role "Contributor" --query 'password' -o tsv --scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID /subscriptions/$AZURE_BACKUP_SUBSCRIPTION_ID`
rl@Gurpreet RLWorkspace % echo $AZURE_CLIENT_SECRET
yiN52bP~FrTZvwyf_wR3elWxN-O-fb96CO 


rl@Gurpreet RLWorkspace % AZURE_CLIENT_ID=`az ad sp list --display-name "robsvelerosp" --query '[0].appId' -o tsv`

rl@Gurpreet RLWorkspace % echo $AZURE_CLIENT_ID
311b838a-1986-497f-8929-9e624f627d49

rl@Gurpreet RLWorkspace % velero install \
    --provider azure \
    --plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
    --bucket $BLOB_CONTAINER \
    --secret-file ./credentials-velero \
    --backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID,subscriptionId=$AZURE_BACKUP_SUBSCRIPTION_ID \
    --snapshot-location-config apiTimeout=5m,resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,subscriptionId=$AZURE_BACKUP_SUBSCRIPTION_ID



deploy the evil jenkins app


 export SERVICE_IP=$(kubectl get svc --namespace jenkins my-release-jenkins --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo "Jenkins URL: http://$SERVICE_IP/"

2. Login with the following credentials

  echo Username: user
  echo Password: $(kubectl get secret --namespace jenkins my-release-jenkins -o jsonpath="{.data.jenkins-password}" | base64 --decode)

  Backup the jenkins namespace

  velero backup create jenkins-backup-004 --include-namespaces jenkins

  velero backup describe jenkins-backup-004  

  delete the namespace

  kubectl delete ns jenkins

  Restore the app

velero restore create --from-backup jenkins-backup-004 


export SERVICE_IP=$(kubectl get svc --namespace jenkins my-release-jenkins --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
echo "Jenkins URL: http://$SERVICE_IP/"

Done!
