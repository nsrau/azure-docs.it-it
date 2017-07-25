Usare l'interfaccia della riga di comando di Azure per ottenere l'URL della distribuzione remota per l'app per le API. Nel comando seguente sostituire *\<nome_app>* con il nome dell'app Web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Configurare la distribuzione Git locale per consentire il push nel computer remoto.

```bash
git remote add azure <URI from previous step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app. Verrà richiesta la password creata in precedenza quando è stato creato l'utente della distribuzione. Assicurarsi di immettere la password creata in un passaggio precedente della guida introduttiva, anziché quella usata per accedere al portale di Azure.

```bash
git push azure master
```
