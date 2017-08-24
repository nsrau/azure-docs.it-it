Configurare la distribuzione Git locale nell'app per le API con il comando [az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git).   

Il servizio app supporta molti modi per distribuire contenuti a un'app Web, ad esempio FTP, istanza Git locale, GitHub, Visual Studio Team Services e BitBucket. Per questa guida introduttiva si distribuirà un'istanza Git locale. In altre parole, per la distribuzione verrà usato un comando Git che eseguirà il push da un repository locale a un repository in Azure.  

Usare lo script seguente per impostare le credenziali di distribuzione a livello di account da usare quando si esegue il push del codice, assicurandosi di includere i valori personali di nome utente e password.   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
