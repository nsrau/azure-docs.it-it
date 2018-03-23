## <a name="rest"></a>Distribuire il file con estensione zip con le API REST 

È possibile usare le [API REST del servizio distribuzione](https://github.com/projectkudu/kudu/wiki/REST-API) per distribuire il file ZIP nella propria app in Azure. Per eseguire la distribuzione, inviare una richiesta POST all'indirizzo https://<app_name>.scm.azurewebsites.net/api/zipdeploy. La richiesta POST deve contenere il file ZIP nel corpo del messaggio. Le credenziali di distribuzione per l'app vengono fornite nella richiesta usando l'autenticazione di base HTTP. Per altre informazioni, vedere le [informazioni di riferimento sulla distribuzione push tramite file ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Per l'autenticazione HTTP di base sono necessarie le credenziali di distribuzione del servizio app. Per informazioni su come impostare le credenziali di distribuzione, vedere [Impostare e reimpostare le credenziali a livello di utente](../articles/app-service/app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Con cURL

L'esempio seguente usa lo strumento cURL per distribuire un file con estensione zip. Sostituire i segnaposto `<username>`, `<password>`, `<zip_file_path>` e `<app_name>`. Quando richiesto da cURL, digitare la password.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Questa richiesta attiva la distribuzione push dal file ZIP caricato. È possibile esaminare le distribuzioni correnti e passate usando l'endpoint https://<app_name>.scm.azurewebsites.net/api/deployments, come illustrato nell'esempio cURL seguente. Anche in questo caso, sostituire il segnaposto `<app_name>` con il nome dell'app e `<deployment_user>` con il nome utente delle credenziali di distribuzione.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Con PowerShell

L'esempio seguente usa [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) per inviare una richiesta contenente il file con estensione zip. Sostituire i segnaposto `<deployment_user>`, `<deployment_password>`, `<zip_file_path>` e `<app_name>`.

```PowerShell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

Questa richiesta attiva la distribuzione push dal file ZIP caricato. Per esaminare le distribuzioni correnti e precedenti, eseguire i comandi seguenti. Sostituire nuovamente il segnaposto `<app_name>`.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
