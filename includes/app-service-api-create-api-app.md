
Creare un'[app per le API](../articles/app-service-api/app-service-api-apps-why-best-platform.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/appservice/web#create). 

L'app Web fornisce uno spazio host per l'API e un URL per visualizzare l'app distribuita.

Nel comando seguente sostituire *\<nome_app>* con un nome univoco. Se `<app_name>` non è univoco, verrà visualizzato il messaggio di errore "Il sito Web con il nome <nome_app> specificato esiste già". L'URL predefinito dell'app Web è `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Al termine della creazione dell'app Web, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```