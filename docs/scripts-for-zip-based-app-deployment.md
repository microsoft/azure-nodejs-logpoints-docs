# Zip Deployment scripts
## Powershell script for windows users
1. [Install the Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest#install-on-windows) and then run `az login` to configure your account
1. Download "sample-app.zip" from [here](https://logpointsassets.blob.core.windows.net/deploy-tools/sample-app.zip)
1. Download/Copy the attached powershell script from [here](https://logpointsassets.blob.core.windows.net/deploy-tools/deployApp.ps1) to the same folder you have downloaded the above sample app.
    
```powershell
## Script to deploy your webapp to the newly created App service running Logpoints image environment
## ASSUMES, that your zip file to be deployed in in the same directory and named "sample-app.zip"
param (
    [Parameter(Mandatory=$true)][string]$appName,
    [Parameter(Mandatory=$true)][string]$resourceGroup,
    [Parameter(Mandatory=$true)][string]$sourceFilePath
)

az webapp config container set --name $appName -g $resourceGroup --enable-app-service-storage true -o tsv

Write-Output "Getting Deployment configuration username and password for zipdeploy"
# get the deployment credentials
$user = az webapp deployment list-publishing-profiles -n $appName -g $resourceGroup `
    --query "[?publishMethod=='MSDeploy'].userName" -o tsv

$pass = az webapp deployment list-publishing-profiles -n $appName -g $resourceGroup `
    --query "[?publishMethod=='MSDeploy'].userPWD" -o tsv

# basic auth with Invoke-WebRequest: https://stackoverflow.com/a/27951845/7532
$pair = "$($user):$($pass)"
$encodedCreds = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($pair))
$basicAuthValue = "Basic $encodedCreds"

$Headers = @{
    Authorization = $basicAuthValue
}

Write-Output "Deploying sample app from @ $sourceFilePath"

# use kudu deploy from zip file
Invoke-WebRequest -Uri https://$appName.scm.azurewebsites.net/api/zipdeploy -Headers $Headers `
    -InFile $sourceFilePath -ContentType "multipart/form-data" -Method Post

Write-Output "Deployment completed."
```

## Bash script for linux user
1. If you are using a linux box, you can use the script here instead

```bash
##
## ZIP DEPLOYMENT SCRIPT FOR APP SERVICE
## USAGE: ./zipDeploy.sh <web-app-name> <resource-group-name>
##

function wait_for_webapp_ready() {
    MAX_TRIES="$1"
    HTTP_EXIT_CODE="$2"

    CURL_CMD="curl -s -D - http://$WEB_APP_NAME.azurewebsites.net -o /dev/null -m 5"
    echo "Waiting for webapp to be ready with $HTTP_EXIT_CODE HTTP status code in $MAX_TRIES tries.."
    echo "Executing command: $CURL_CMD"

    READY="0"
    RETRY_COUNT="0"
    while [[ "$READY" -ne $HTTP_EXIT_CODE && $RETRY_COUNT -lt $MAX_TRIES ]]; do
        OP=($($CURL_CMD))
        READY=${OP[1]}
        RETRY_COUNT=$[RETRY_COUNT+1]
        echo -ne "Attempt: $RETRY_COUNT, HTTP status code: $READY"\\r
        sleep 3
    done
    echo ""

    if [[ $MAX_TRIES -eq $RETRY_COUNT ]]; then
        echo "Unable to bring up application after $MAX_TRIES attempts. Exiting. "
        exit 1
    else
        echo "WebApp Ready: $READY"
    fi
}

if [ "$#" -ne 3 ]; then
    echo "ERROR: Not enough parameters."
    echo "USAGE: ./$0 <web-app-name> <resource-group-name> <path-to-zipped-application>"
    exit 1
fi

WEB_APP_NAME=$1
RESOURCE_GROUP_NAME=$2
SAMPLE_APP=$3

echo -ne "Looking for deployment profile username and password : "
USER=$(az webapp deployment list-publishing-profiles -n $WEB_APP_NAME  -g $RESOURCE_GROUP_NAME --query "[?publishMethod=='MSDeploy'].userName" -o tsv)
PASSWD=$(az webapp deployment list-publishing-profiles -n $WEB_APP_NAME  -g $RESOURCE_GROUP_NAME --query "[?publishMethod=='MSDeploy'].userPWD" -o tsv)
echo " [DONE]"

echo -ne "Deploying sample app at $SAMPLE_APP : "
curl -X POST -u $USER:$PASSWD https://$WEB_APP_NAME.scm.azurewebsites.net/api/zipdeploy --data-binary @${SAMPLE_APP} -o /dev/null 2> /dev/null
echo " [DONE]"

## WAIT FOR WEBAPP TO BE READY NOW...
wait_for_webapp_ready 100 200

```

