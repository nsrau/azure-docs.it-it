Usare il comando [az appservice web create](/cli/azure/appservice/web#create) per creare l'app Web. Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app. `<app_name>` viene usato nel sito DNS predefinito per l'app Web. Se `<app_name>` non è univoco, verrà visualizzato il messaggio di errore descrittivo "Il sito Web con il nome <app_name> specificato esiste già".

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
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

Passare al sito (`http://<app_name>.azurewebsites.net`) per visualizzare l'app Web appena creata.

![App Web creata nel servizio app](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>Configurare la distribuzione con l'istanza Git locale

Il servizio app supporta molti modi per distribuire contenuti a un'app Web, ad esempio FTP, istanza Git locale, GitHub, Visual Studio Team Services e BitBucket. 

Per questa guida introduttiva si distribuirà un'istanza Git locale. In altre parole, per la distribuzione verrà usato un comando Git che eseguirà il push da un repository locale a un repository in Azure. 

Usare il comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) per configurare l'accesso dell'istanza Git locale all'app Web.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

L'output presenta il formato seguente:

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

Salvare l'URI indicato perché sarà necessario nel passaggio successivo. L'elemento `<username>` corrisponde all'[utente della distribuzione](#configure-a-deployment-user) creato nel passaggio precedente.

## <a name="push-to-azure-from-git"></a>Eseguire il push in Azure da Git

Aggiungere un'istanza remota di Azure al repository Git locale.

```bash
git remote add azure <URI from previous step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app. Verrà richiesta la password creata in precedenza quando è stato creato l'utente della distribuzione. Assicurarsi di immettere la password creata in [Configurare un utente della distribuzione](#configure-a-deployment-user), anziché quella usata per accedere al portale di Azure.

```azurecli
git push azure master
```

Il comando precedente restituisce informazioni simili all'esempio seguente:

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>Passare all'app


Passare all'app distribuita:

```
http://<app_name>.azurewebsites.net
```

