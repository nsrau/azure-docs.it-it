In Cloud Shell creare un'[app Web](../articles/app-service/app-service-web-overview.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp#create). 

L'app Web fornisce uno spazio host per il codice e un URL per visualizzare l'app distribuita.

Nel comando seguente sostituire *\<nome_app>* con un nome univoco (i caratteri validi sono `a-z`, `0-9` e `-`). Se `<app_name>` non è univoco, verrà visualizzato il messaggio di errore "Il sito Web con il nome <nome_app> specificato esiste già". L'URL predefinito dell'app Web è `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
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

Passare al sito per visualizzare l'app Web appena creata.

```bash
http://<app_name>.azurewebsites.net
```
