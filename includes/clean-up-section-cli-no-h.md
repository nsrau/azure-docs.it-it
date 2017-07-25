Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se non si prevede di aver bisogno di queste risorse in futuro, è possibile eliminarle eliminando il gruppo di risorse.
 
1. Eseguire il comando seguente per assicurarsi che il gruppo di risorse non contenga risorse da salvare:

  ```azurecli
  az group show --name myResourceGroup
  ```

2. Se le risorse visualizzate sono quelle da eliminare, eseguire il comando seguente:
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
