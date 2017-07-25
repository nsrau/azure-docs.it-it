Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Per visualizzare le posizioni disponibili, eseguire il comando `az appservice list-locations`. In genere si creano risorse in un'area nelle vicinanze.
