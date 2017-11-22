## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app per le funzioni, database e account di archiviazione.

Nell'esempio seguente viene creato il gruppo di risorse denominato `myResourceGroup`.  
Se non si usa Cloud Shell, prima accedere usando `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
In genere, il gruppo di risorse e le risorse vengono create in un'area nelle vicinanze. Per visualizzare tutte le località supportate per i piani di Servizio app, eseguire il comando [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations).