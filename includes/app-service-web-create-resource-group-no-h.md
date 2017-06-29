Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

In genere, il gruppo di risorse e le risorse vengono create in un'area nelle vicinanze. Per visualizzare tutte le località supportate per le app Web di Azure, eseguire il comando `az appservice list-locations`. 