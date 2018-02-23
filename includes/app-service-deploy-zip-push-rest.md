## <a name="rest"></a>Eseguire la distribuzione usando le API REST 
 
È possibile usare le [API REST del servizio distribuzione](https://github.com/projectkudu/kudu/wiki/REST-API) per distribuire il file ZIP nella propria app in Azure. È sufficiente inviare una richiesta POST a https://<app_name>.scm.azurewebsites.net/api/zipdeploy. La richiesta POST deve contenere il file ZIP nel corpo del messaggio. Le credenziali di distribuzione per l'app vengono fornite nella richiesta usando l'autenticazione di base HTTP. Per altre informazioni, vedere le [informazioni di riferimento sulla distribuzione push tramite file ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

L'esempio seguente usa lo strumento cURL per inviare una richiesta contenente il file ZIP. È possibile eseguire cURL dal terminale in un computer Mac o Linux oppure usando Bash in Windows. Sostituire il segnaposto `<zip_file_path>` con il percorso del file ZIP del progetto. Sostituire inoltre `<app_name>` con il nome univoco dell'app.

Sostituire il segnaposto `<deployment_user>` con il nome utente delle credenziali di distribuzione. Quando richiesto da cURL, digitare la password. Per informazioni su come impostare le credenziali di distribuzione per l'app, vedere [Impostare e reimpostare le credenziali a livello di utente](../articles/app-service/app-service-deployment-credentials.md#userscope).   

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Questa richiesta attiva la distribuzione push dal file ZIP caricato. È possibile esaminare le distribuzioni correnti e passate usando l'endpoint https://<app_name>.scm.azurewebsites.net/api/deployments, come illustrato nell'esempio cURL seguente. Anche in questo caso, sostituire il segnaposto `<app_name>` con il nome dell'app e `<deployment_user>` con il nome utente delle credenziali di distribuzione.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```