---
title: "Creare una macchina virtuale Linux con più schede di interfaccia di rete usando l&quot;interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft"
description: "Informazioni su come creare una VM Linux con più schede di interfaccia di rete collegate usando l&quot;interfaccia della riga di comando di Azure 2.0 o i modelli di Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 7f3abdd63e43713d9d1f7ff28e44efc08167fddb
ms.openlocfilehash: e50f9ce362177a2aff8da5b1d516973c41d1149e
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-linux-vm-with-multiple-nics"></a>Creare una VM Linux con più schede di interfaccia di rete
È possibile creare una macchina virtuale (VM) in Azure con più interfacce di rete virtuale (NIC) collegate. Uno scenario comune è quello di avere subnet diverse per la connettività front-end e back-end, oppure disporre di una rete dedicata a una soluzione di monitoraggio o di backup. In questo articolo vengono presentati i comandi rapidi per creare una macchina virtuale con più schede di rete collegate. Per informazioni dettagliate, incluse quelle sulla creazione di più schede di rete all'interno degli script di Bash, consultare la sezione dedicata alla [distribuzione di macchine virtuali con più schede di rete](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Le differenti [dimensioni della macchina virtuale](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza.

Questo articolo illustra come creare una macchina virtuale con più schede di interfaccia di rete usando l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](virtual-machines-linux-multiple-nics-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!WARNING]
> È necessario collegare più schede di rete quando si crea una VM, poiché non è possibile aggiungere le schede di rete a una macchina virtuale esistente. È possibile [creare una nuova VM basata sui dischi virtuali originali](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e creare più schede di interfaccia di rete mentre si distribuisce la VM.


## <a name="create-supporting-resources"></a>Creare risorse di supporto
Installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e accedere a un account Azure tramite il comando [az login](/cli/azure/#login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `myVM`.

Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `WestUS`:

```azurecli
az group create --name myResourceGroup --location westus
```

Creare la rete virtuale con [az network vnet create](/cli/azure/network/vnet#create). L'esempio seguente creata una rete virtuale denominata `myVnet` e una sottorete denominata `mySubnetFrontEnd`:

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnetFrontEnd --subnet-prefix 192.168.1.0/24
```

Creare una subnet per il traffico di back-end con il comando [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). Nell'esempio seguente viene creata una subnet denominata `mySubnetBackEnd`:

```azurecli
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVnet \
    --name mySubnetBackEnd --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Creare e configurare più schede di interfaccia di rete
È possibile leggere ulteriori informazioni sulla [distribuzione di più schede di rete tramite l'interfaccia della riga di comando di Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), incluso lo script del processo di ciclo per creare tutte le schede NIC.

In genere è necessario creare un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) o un [servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md) per gestire e distribuire il traffico tra le VM. Creare un gruppo di sicurezza di rete con il comando [az network nsg create](/cli/azure/network/nsg#create). L'esempio seguente crea un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup \
  --name myNetworkSecurityGroup
```

Creare due schede di interfaccia di rete con il comando [az network nic create](/cli/azure/network/nic#create). Nell'esempio seguente vengono create due schede di interfaccia di rete, denominate `myNic1` e `myNic2`, connesse al gruppo di sicurezza di rete, ognuna collegata a ogni subnet:

```azurecli
az network nic create --resource-group myResourceGroup --name myNic1 \
  --vnet-name myVnet --subnet mySubnetFrontEnd \
  --network-security-group myNetworkSecurityGroup
az network nic create --resource-group myResourceGroup --name myNic2 \
  --vnet-name myVnet --subnet mySubnetBackEnd \
  --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Creare una macchina virtuale e collegare le schede di interfaccia di rete
Quando si crea la macchina virtuale, specificare le schede di interfaccia di rete create con `--nics`. L'utente deve anche fare attenzione quando seleziona la dimensione della macchina virtuale. Esistono dei limiti per quanto riguarda il numero totale di schede di rete che è possibile aggiungere. Ulteriori informazioni sulle [dimensioni delle macchine virtuali di Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Creare una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). Nell'esempio seguente viene creata una VM denominata `myVM` usando [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic1 myNic2
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Creare più schede di interfaccia di rete usando i modelli di Resource Manager
I modelli di Azure Resource Manager utilizzano i file JSON dichiarativi per definire l'ambiente. È possibile consultare una [panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). I modelli di Resource Manager offrono un modo di creare più istanze di una risorsa durante la distribuzione, come ad esempio la creazione di più schede di rete. Utilizzare *Copia* per specificare il numero di istanze da creare:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Ulteriori informazioni sulla [creazione di più istanze utilizzando *Copia*](../azure-resource-manager/resource-group-create-multiple.md). 

È inoltre possibile utilizzare un `copyIndex()` per poi aggiungere un numero al nome di una risorsa, che consente di creare `myNic1`, `myNic2`, e così via. Di seguito viene riportato un esempio di aggiunta del valore di indice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

È possibile consultare un esempio completo di [creazione di più schede di rete utilizzando i modelli di Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Passaggi successivi
Assicurarsi di consultare [Dimensioni delle macchine virtuali di Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) durante il tentativo di creazione di una macchina virtuale con più schede di rete. Prestare attenzione al numero massimo di schede di rete supportato per ogni dimensione della macchina virtuale. 

Tenere presente che non è possibile aggiungere altre schede di rete a una macchina virtuale esistente. È necessario creare tutte le schede di rete quando si distribuisce la macchina virtuale. Prestare attenzione quando si pianificano le distribuzioni per assicurarsi di avere la connettività di rete necessaria fin dall'inizio.


