## <a name="rest"></a>Distribuire mediante le API REST 
 
È possibile utilizzare il [API REST del servizio distribuzione](https://github.com/projectkudu/kudu/wiki/REST-API) per distribuire il file con estensione zip all'App in Azure. Per https://<app_name>.scm.azurewebsites.net/api/zipdeploy sufficiente inviare una richiesta POST. La richiesta POST deve contenere il file ZIP nel corpo del messaggio. Le credenziali di distribuzione per l'app vengono fornite nella richiesta utilizzando l'autenticazione di base HTTP. Per ulteriori informazioni, vedere il [riferimento per la distribuzione push zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

L'esempio seguente usa lo strumento cURL per inviare una richiesta contenente il file con estensione zip. In un computer Mac o Linux o Bash su Windows, è possibile eseguire cURL dal terminale. Sostituire il `<zip_file_path>` segnaposto con il percorso del file zip del progetto. Sostituire anche `<app_name>` con il nome univoco dell'app.

Sostituire il `<deployment_user>` segnaposto con il nome utente delle credenziali di distribuzione. Quando richiesto dalla cURL, digitare la password. Per informazioni su come impostare le credenziali di distribuzione per l'app, vedere [impostare e reimpostare le credenziali a livello di utente](../articles/app-service/app-service-deployment-credentials.md#userscope).   

```bash
curl POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

La richiesta attiva distribuzione push dal file zip caricati. È possibile esaminare le distribuzioni correnti e passate tramite l'endpoint https://<app_name>.scm.azurewebsites.net/api/deployments, come illustrato nell'esempio seguente cURL. Nuovamente, sostituire `<app_name>` con il nome dell'app e `<deployment_user>` con il nome utente delle credenziali di distribuzione.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```