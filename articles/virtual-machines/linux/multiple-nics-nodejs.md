---
title: "Creare una VM Linux in Azure con più schede di interfaccia di rete | Documentazione Microsoft"
description: "Informazioni su come creare una VM Linux con più schede di interfaccia di rete collegate utilizzando l'interfaccia della riga di comando di Azure o i modelli di Resource Manager."
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
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 93a32ae7ec0cf73825791e8c8bc3d388cf999ece
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="create-a-linux-virtual-machine-with-multiple-nics-using-the-azure-cli-10"></a>Creare una macchina virtuale Linux con più schede di interfaccia di rete usando l'interfaccia della riga di comando di Azure 1.0
È possibile creare una macchina virtuale (VM) in Azure con più interfacce di rete virtuale (NIC) collegate. Uno scenario comune è quello di avere subnet diverse per la connettività front-end e back-end oppure una rete dedicata a una soluzione di monitoraggio o backup. In questo articolo vengono presentati i comandi rapidi per creare una macchina virtuale con più schede di rete collegate. Le differenti [dimensioni della macchina virtuale](sizes.md) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza.

> [!WARNING]
> È necessario collegare più schede di interfaccia di rete quando si crea una VM, perché non è possibile aggiungere le schede a una VM esistente con l'interfaccia della riga di comando di Azure 1.0. È possibile [aggiungere schede di interfaccia di rete a una VM esistente con l'interfaccia della riga di comando di Azure 2.0](multiple-nics.md). È anche possibile [creare una VM basata sui dischi virtuali originali](copy-vm.md) e creare più schede di interfaccia di rete quando si distribuisce la VM.


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#create-supporting-resources): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](multiple-nics.md): interfaccia avanzata per il modello di distribuzione di gestione delle risorse


## <a name="create-supporting-resources"></a>Creare risorse di supporto
Controllare di aver effettuato l'accesso tramite l'[interfaccia della riga di comando di Azure](../../cli-install-nodejs.md) in modalità Resource Manager:

```azurecli
azure config mode arm
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *mystorageaccount* e *myVM*.

Creare prima un gruppo di risorse. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
azure group create myResourceGroup --location eastus
```

Creare un account di archiviazione in cui salvare le VM. L'esempio seguente crea un account di archiviazione denominato *mystorageaccount*:

```azurecli
azure storage account create mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --kind Storage \
    --sku-name PLRS
```

Creare una rete virtuale alla quale connettere le VM. L'esempio seguente crea una rete virtuale denominata *myVnet* con prefisso dell'indirizzo *192.168.0.0/16*:

```azurecli
azure network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefixes 192.168.0.0/16
```

Creare due subnet per la rete virtuale: una per il traffico front-end e l'altra per il traffico di back-end. L'esempio seguente crea due subnet denominate *mySubnetFrontEnd* e *mySubnetBackEnd*:

```azurecli
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetFrontEnd \
    --address-prefix 192.168.1.0/24
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Creare e configurare più schede di interfaccia di rete
È possibile leggere ulteriori informazioni sulla [distribuzione di più schede di rete tramite l'interfaccia della riga di comando di Azure](../../virtual-machines/linux/multiple-nics.md), incluso lo script del processo di ciclo per creare tutte le schede NIC.

L'esempio seguente crea due schede di interfaccia di rete, denominate *myNic1* e *myNic2*, con una scheda che si connette a ogni subnet:

```azurecli
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic1 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetFrontEnd
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic2 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetBackEnd
```

In genere è necessario creare anche un [gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md) o un [servizio di bilanciamento del carico](../../load-balancer/load-balancer-overview.md) per gestire e distribuire il traffico tra le VM. L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Associare le due schede di interfaccia di rete al gruppo di sicurezza di rete usando `azure network nic set`. L'esempio seguente associa *myNic1* e *myNic2* a *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Creare una macchina virtuale e collegare le schede di interfaccia di rete
In fase di creazione della macchina virtuale, è ora possibile specificare più schede di rete. Anziché utilizzare `--nic-name` per fornire una singola scheda di rete, si utilizza `--nic-names` per fornire un elenco delimitato da virgole di schede di rete. L'utente deve anche fare attenzione quando seleziona la dimensione della macchina virtuale. Esistono dei limiti per quanto riguarda il numero totale di schede di rete che è possibile aggiungere. Ulteriori informazioni sulle [dimensioni delle macchine virtuali di Linux](sizes.md). L'esempio seguente mostra come specificare più schede di interfaccia di rete e quindi una dimensione di VM che supporta l'uso di più schede di interfaccia di rete (*Standard_DS2_v2*):

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Creare più schede di interfaccia di rete usando i modelli di Resource Manager
I modelli di Azure Resource Manager utilizzano i file JSON dichiarativi per definire l'ambiente. È possibile consultare una [panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). I modelli di Resource Manager offrono un modo di creare più istanze di una risorsa durante la distribuzione, come ad esempio la creazione di più schede di rete. Utilizzare *Copia* per specificare il numero di istanze da creare:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Ulteriori informazioni sulla [creazione di più istanze utilizzando *Copia*](../../resource-group-create-multiple.md). 

È inoltre possibile utilizzare un `copyIndex()` per poi aggiungere un numero al nome di una risorsa, che consente di creare `myNic1`, `myNic2`, e così via. Di seguito viene riportato un esempio di aggiunta del valore di indice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

È possibile consultare un esempio completo di [creazione di più schede di rete utilizzando i modelli di Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Passaggi successivi
Assicurarsi di consultare [Dimensioni delle macchine virtuali di Linux](sizes.md) durante il tentativo di creazione di una macchina virtuale con più schede di rete. Prestare attenzione al numero massimo di schede di rete supportato per ogni dimensione della macchina virtuale. 

Tenere presente che non è possibile aggiungere altre schede di rete a una macchina virtuale esistente. È necessario creare tutte le schede di rete quando si distribuisce la macchina virtuale. Prestare attenzione quando si pianificano le distribuzioni per assicurarsi di avere la connettività di rete necessaria fin dall'inizio.

