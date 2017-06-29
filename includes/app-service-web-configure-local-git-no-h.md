Configurare la distribuzione Git locale nell'app Web con il comando [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git).

Il servizio app supporta molti modi per distribuire contenuti a un'app Web, ad esempio FTP, istanza Git locale, GitHub, Visual Studio Team Services e BitBucket. Per questa guida introduttiva si distribuirà un'istanza Git locale. In altre parole, per la distribuzione verrà usato un comando Git che eseguirà il push da un repository locale a un repository in Azure. 

Nel comando seguente sostituire *\<nome_app>* con il nome dell'app Web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

L'output presenta il formato seguente:

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

L'elemento `<username>` corrisponde all'[utente della distribuzione](#configure-a-deployment-user) creato nel passaggio precedente.

Salvare l'URI visualizzato, poiché sarà necessario nel passaggio successivo. 
