## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). 

L'esempio seguente crea una macchina virtuale denominata *myVM* e le chiavi SSH, se non esistono già in un percorso predefinito. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. Il comando imposta anche *azureuser* come nome utente dell'amministratore. Questo nome verrà usato nei passaggi successivi per la connessione alla macchina virtuale. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Dopo che la VM è stata creata, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente. Prendere nota di `publicIpAddress`. Questo indirizzo verrà usato nei passaggi successivi per l'accesso alla macchina virtuale.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web 

Per impostazione predefinita, nelle VM Linux distribuite in Azure sono consentite solo le connessioni SSH. Poiché questa macchina virtuale verrà usata come server Web, è necessario aprire la porta 80 da Internet. Usare il comando [az vm open-port](/cli/azure/vm#az_vm_open_port) per aprire la porta.  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>Usare SSH per connettersi alla macchina virtuale


Se non si conosce già l'indirizzo IP pubblico della macchina virtuale, eseguire il comando [az network public-ip list](/cli/azure/network/public-ip#list). Questo indirizzo IP sarà necessario in alcuni passaggi successivi.


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Usare il comando seguente per creare una sessione SSH con la macchina virtuale. Sostituire l'indirizzo IP pubblico corretto della macchina virtuale. In questo esempio l'indirizzo IP è *40.68.254.142*. *azureuser* è il nome utente dell'amministratore impostato al momento della creazione della macchina virtuale.

```bash
ssh azureuser@40.68.254.142
```

