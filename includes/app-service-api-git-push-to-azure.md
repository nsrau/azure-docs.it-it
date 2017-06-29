Usare l'interfaccia della riga di comando di Azure per ottenere l'URL di distribuzione remota per l'app per le API e configurare la distribuzione Git locale per poter effettuare il push in remoto.

```bash
giturl=$(az webapp deployment source config-local-git -n $app_name \ -g myResourceGroup --query [url] -o tsv)

git remote add azure $giturl
```

Effettuare il push all'istanza remota di Azure per distribuire l'app. Verrà richiesta la password creata in precedenza quando è stato creato l'utente della distribuzione. Assicurarsi di immettere la password creata in un passaggio precedente della guida introduttiva, anziché quella usata per accedere al portale di Azure.

```bash
git push azure master
```
