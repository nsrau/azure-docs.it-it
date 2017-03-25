---
title: "Creare una VM Linux in Azure con più schede di interfaccia di rete | Documentazione Microsoft"
description: "Informazioni su come creare una VM Linux con più schede di interfaccia di rete collegate utilizzando l&quot;interfaccia della riga di comando di Azure o i modelli di Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 51edde1be97885b92fe48f65d3f477c73cd19338
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-linux-vm-with-multiple-nics-using-the-azure-cli-10"></a>Creare una macchina virtuale Linux con più schede di interfaccia di rete usando l'interfaccia della riga di comando di Azure 1.0
È possibile creare una macchina virtuale (VM) in Azure con più interfacce di rete virtuale (NIC) collegate. Uno scenario comune è quello di avere subnet diverse per la connettività front-end e back-end, oppure disporre di una rete dedicata a una soluzione di monitoraggio o di backup. In questo articolo vengono presentati i comandi rapidi per creare una macchina virtuale con più schede di rete collegate. Per informazioni dettagliate, incluse quelle sulla creazione di più schede di rete all'interno degli script di Bash, consultare la sezione dedicata alla [distribuzione di macchine virtuali con più schede di rete](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Le differenti [dimensioni della macchina virtuale](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza.

> [!WARNING]
> È necessario collegare più schede di rete quando si crea una VM, poiché non è possibile aggiungere le schede di rete a una macchina virtuale esistente. È possibile [creare una nuova VM basata sui dischi virtuali originali](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e creare più schede di interfaccia di rete mentre si distribuisce la VM.


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#create-supporting-resources): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](virtual-machines-linux-multiple-nics.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa


## <a name="create-supporting-resources"></a>Creare risorse di supporto
Controllare di aver effettuato l'accesso tramite l'[interfaccia della riga di comando di Azure](../cli-install-nodejs.md) in modalità Resource Manager:

```azurecli
azure config mode arm
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `myVM`.

Creare prima un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `WestUS`:

```azurecli
azure group create myResourceGroup -l WestUS
```

Creare un account di archiviazione in cui salvare le VM. Nell'esempio seguente viene creato un nuovo account di archiviazione denominato `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Creare una rete virtuale alla quale connettere le VM. Nell'esempio seguente viene creata una rete virtuale denominata `myVnet` con il prefisso dell'indirizzo `192.168.0.0/16`:

```azurecli
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Creare due subnet per la rete virtuale: una per il traffico front-end e l'altra per il traffico di back-end. Nell'esempio seguente vengono create due subnet chiamate `mySubnetFrontEnd` e `mySubnetBackEnd`:

```azurecli
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Creare e configurare più schede di interfaccia di rete
È possibile leggere ulteriori informazioni sulla [distribuzione di più schede di rete tramite l'interfaccia della riga di comando di Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), incluso lo script del processo di ciclo per creare tutte le schede NIC.

Nell'esempio seguente vengono create due schede di interfaccia di rete, denominate `myNic1` e `myNic2`, ognuna collegata a ogni subnet:

```azurecli
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

In genere è necessario creare anche un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) o un [servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md) per gestire e distribuire il traffico tra le VM. Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Associare le due schede di interfaccia di rete al gruppo di sicurezza di rete usando `azure network nic set`. Nell'esempio seguente vengono associati `myNic1` e `myNic2` a `myNetworkSecurityGroup`:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Creare una macchina virtuale e collegare le schede di interfaccia di rete
In fase di creazione della macchina virtuale, è ora possibile specificare più schede di rete. Anziché utilizzare `--nic-name` per fornire una singola scheda di rete, si utilizza `--nic-names` per fornire un elenco delimitato da virgole di schede di rete. L'utente deve anche fare attenzione quando seleziona la dimensione della macchina virtuale. Esistono dei limiti per quanto riguarda il numero totale di schede di rete che è possibile aggiungere. Ulteriori informazioni sulle [dimensioni delle macchine virtuali di Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nel seguente esempio viene illustrato come specificare più schede di rete e, successivamente, la dimensione della macchina virtuale che supporta l'utilizzo di più schede di rete (`Standard_DS2_v2`):

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
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


