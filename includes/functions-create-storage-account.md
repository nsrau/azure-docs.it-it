## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Funzioni usa un account di uso generico di Archiviazione di Azure per gestire lo stato e altre informazioni sulle funzioni. Creare un account di archiviazione di uso generico nel gruppo di risorse creato usando il comando [az storage account create](/cli/azure/storage/account#create).

Nel comando seguente sostituire il segnaposto `<storage_name>` con il nome globalmente univoco dell'account di archiviazione. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Al termine della creazione dell'account di archiviazione, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```