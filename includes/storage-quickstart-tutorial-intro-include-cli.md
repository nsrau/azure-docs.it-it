## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un account di archiviazione per utilizzo generico con il comando [az storage account create](/cli/azure/storage/account#create). L'account di archiviazione per utilizzo generico può essere usato per tutti e quattro i servizi: BLOB, file, tabelle e code. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Specificare le credenziali dell'account di archiviazione

L'interfaccia della riga di comando di Azure richiede le credenziali dell'account di archiviazione per la maggior parte dei comandi usati in questa esercitazione. Anche se esistono diverse opzioni per fornirle, uno dei modi più semplici consiste nell'impostare le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`.

Usare prima il comando [az storage account keys list](/cli/azure/storage/account/keys#list) per visualizzare le chiavi dell'account di archiviazione:

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Impostare ora le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`. Questa operazione può essere eseguita nella shell Bash con il comando `export`:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_ACCESS_KEY="myStorageAccountKey"
```