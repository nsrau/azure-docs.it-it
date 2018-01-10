## <a name="deploy-uploaded-zip-file"></a>Distribuire il file ZIP caricato

In Cloud Shell distribuire il file ZIP caricato nell'app Web usando il comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Assicurarsi di sostituire *\<app_name>* con il nome dell'app Web specifica.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

Questo comando distribuisce i file e le directory del file ZIP nella cartella predefinita dell'applicazione del servizio app (`\home\site\wwwroot`) e riavvia l'app. Verranno eseguiti anche eventuali processi d compilazione personalizzati aggiuntivi presenti.
