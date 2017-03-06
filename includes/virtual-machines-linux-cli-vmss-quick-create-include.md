## <a name="prerequisites"></a>Prerequisiti

Se non lo si è già fatto, ottenere una [versione di valutazione gratuita della sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/) e installare l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="create-the-scale-set"></a>Creare il set di scalabilità

Creare innanzitutto un gruppo di risorse per distribuire il set di scalabilità in:

```azurecli
az group create --location westus --name myResourceGroup
```

A questo punto creare i set di scalabilità usando il comando `az vmss create`. Nell'esempio seguente viene creato un set di scalabilità di Linux denominato `myvmss` nel gruppo di risorse denominato `myrg`:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Nell'esempio seguente viene creato un set di scalabilità di Windows con la stessa configurazione:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Se si desidera scegliere un'immagine del sistema operativo diversa, è possibile visualizzare le immagini disponibili mediante il comando `az vm image list` o `az vm image list --all`. Per visualizzare le informazioni di connessione per le macchine virtuali nel set di scalabilità, usare il comando `az vmss list_instance_connection_info`:

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```