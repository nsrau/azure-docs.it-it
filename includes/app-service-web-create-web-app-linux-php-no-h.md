In Cloud Shell creare un'[app Web](../articles/app-service/containers/app-service-linux-intro.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp#create). Non dimenticare di sostituire `<app name>` con un nome univoco dell'app.

Il runtime nel comando seguente viene impostato su `PHP|7.0`. Per visualizzare tutti i runtime supportati, eseguire [az webapp list-runtimes](/cli/azure/webapp#list-runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "PHP|7.0" --deployment-local-git
```

Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure visualizza un output simile all'esempio seguente:

```json
Local git is configured with url of 'https://<username>@<app name>.scm.azurewebsites.net/<app name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

È stata creata una nuova app Web vuota in un contenitore Linux, con la distribuzione Git abilitata.

> [!NOTE]
> L'URL dell'elemento Git remoto è riportato nella proprietà `deploymentLocalGitUrl`, con il formato `https://<username>@<app name>.scm.azurewebsites.net/<app name>.git`. Salvare questo URL, perché sarà necessario in un secondo momento.
>
