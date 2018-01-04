In Cloud Shell creare un gruppo di risorse con il comando [az group create](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *West Europe*. Per visualizzare tutte le località supportate per il servizio app, eseguire il comando `az appservice list-locations`.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

In genere, il gruppo di risorse e le risorse vengono create in un'area nelle vicinanze. 
