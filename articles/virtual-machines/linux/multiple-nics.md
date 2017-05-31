---
title: "Creare una VM Linux in Azure con più schede di interfaccia di rete | Documentazione Microsoft"
description: "Informazioni su come creare una VM Linux con più schede di interfaccia di rete collegate usando l&quot;interfaccia della riga di comando di Azure 2.0 o i modelli di Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 8a2931e462079c101c91497d459d7d3126234244
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Come creare una macchina virtuale Linux in Azure con più schede di interfaccia di rete
È possibile creare una macchina virtuale (VM) in Azure con più interfacce di rete virtuale (NIC) collegate. Uno scenario comune è quello di avere subnet diverse per la connettività front-end e back-end oppure una rete dedicata a una soluzione di monitoraggio o backup. Questo articolo illustra come creare una VM con più schede di interfaccia di rete collegate e come aggiungere o rimuovere le schede di interfaccia di rete da una VM esistente. Per informazioni dettagliate, incluse quelle sulla creazione di più schede di rete all'interno degli script di Bash, consultare la sezione dedicata alla [distribuzione di macchine virtuali con più schede di rete](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Le differenti [dimensioni della macchina virtuale](sizes.md) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza.

Questo articolo illustra come creare una macchina virtuale con più schede di interfaccia di rete usando l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Creare risorse di supporto
Installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e accedere a un account Azure tramite il comando [az login](/cli/azure/#login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *mystorageaccount* e *myVM*.

Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare la rete virtuale con [az network vnet create](/cli/azure/network/vnet#create). L'esempio seguente crea una rete virtuale denominata *myVnet* e una subnet denominata *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Creare una subnet per il traffico di back-end con il comando [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). L'esempio seguente crea una subnet denominata *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Creare un gruppo di sicurezza di rete con il comando [az network nsg create](/cli/azure/network/nsg#create). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Creare e configurare più schede di interfaccia di rete
Creare due schede di interfaccia di rete con il comando [az network nic create](/cli/azure/network/nic#create). L'esempio seguente crea due schede di interfaccia di rete, denominate *myNic1* e *myNic2*, connesse al gruppo di sicurezza di rete, con una scheda che si connette a ogni subnet:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Creare una macchina virtuale e collegare le schede di interfaccia di rete
Quando si crea la macchina virtuale, specificare le schede di interfaccia di rete create con `--nics`. L'utente deve anche fare attenzione quando seleziona la dimensione della macchina virtuale. Esistono dei limiti per quanto riguarda il numero totale di schede di rete che è possibile aggiungere. Ulteriori informazioni sulle [dimensioni delle macchine virtuali di Linux](sizes.md). 

Creare una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). L'esempio seguente crea una VM denominata *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

## <a name="add-a-nic-to-a-vm"></a>Aggiungere una scheda di interfaccia di rete a una VM
I passaggi precedenti hanno consentito di creare una VM con più schede di interfaccia di rete. È anche possibile aggiungere schede di interfaccia di rete a una VM esistente con l'interfaccia della riga di comando di Azure 2.0. 

Creare un'altra scheda di interfaccia di rete con [az network nic create](/cli/azure/network/nic#create). L'esempio seguente crea una scheda di interfaccia rete denominata *myNic3* connessa alla subnet back-end e al gruppo di sicurezza di rete creato nei passaggi precedenti:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Per aggiungere una scheda di interfaccia di rete a una VM esistente, deallocare prima di tutto la VM con [az vm deallocate](/cli/azure/vm#deallocate). L'esempio seguente dealloca la VM denominata *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Aggiungere la scheda di interfaccia di rete con [az vm nic add](/cli/azure/vm/nic#add). L'esempio seguente aggiunge *myNic3* a *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Avviare la VM con [az vm start](/cli/azure/vm#start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

## <a name="remove-a-nic-from-a-vm"></a>Rimuovere una scheda di interfaccia di rete da una VM
Per rimuovere una scheda di interfaccia di rete da una VM esistente, deallocare prima di tutto la VM con [az vm deallocate](/cli/azure/vm#deallocate). L'esempio seguente dealloca la VM denominata *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Rimuovere la scheda di interfaccia di rete con [az vm nic remove](/cli/azure/vm/nic#remove). L'esempio seguente rimuove *myNic3* da *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM 
    --nics myNic3
```

Avviare la VM con [az vm start](/cli/azure/vm#start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
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
Quando si cerca di creare una macchina virtuale con più schede di rete, consultare [Dimensioni per le macchine virtuali di Linux](sizes.md). Prestare attenzione al numero massimo di schede di rete supportato per ogni dimensione della macchina virtuale. 
