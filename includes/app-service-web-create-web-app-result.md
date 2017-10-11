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

Passare al sito per visualizzare l'app Web appena creata. Sostituire _&lt;nome app>_ con un nome app univoco.

```bash
http://<app name>.azurewebsites.net
```
