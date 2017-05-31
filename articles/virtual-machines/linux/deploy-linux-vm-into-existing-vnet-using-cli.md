---
title: Distribuire macchine virtuali Linux in una rete esistente con l&quot;interfaccia della riga di comando Azure 2.0 | Microsoft Docs
description: Informazioni su come distribuire una macchina virtuale Linux in una rete virtuale esistente dall&quot;interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 932fd74ec83f43b604382346ee2c273f5453fcd0
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-cli"></a>Come distribuire una macchina virtuale Linux in una rete virtuale Azure esistente con l'interfaccia della riga di comando di Azure

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure 2.0 per distribuire una macchina virtuale in una rete virtuale esistente. I requisiti sono:

- [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)
- [File di chiavi SSH pubbliche e private](mac-create-ssh-keys.md)

È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](deploy-linux-vm-into-existing-vnet-using-cli-nodejs.md).


## <a name="quick-commands"></a>Comandi rapidi
Se si vuole eseguire rapidamente l'attività, la sezione seguente indica in dettaglio i comandi necessari. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, [a partire da qui](#detailed-walkthrough).

Per creare questo ambiente personalizzato, è necessario installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e connetterla a un account Azure usando [az login](/cli/azure/#login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myVnet* e *myVM*.

**Pre-requisiti:** gruppo di risorse di Azure, rete virtuale e subnet, gruppo di sicurezza di rete con SSH in ingresso e una scheda di interfaccia di rete virtuale.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Distribuire la macchina virtuale nell'infrastruttura di rete virtuale

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

Gli asset di Azure, come le reti virtuali e i gruppi di sicurezza di rete devono essere risorse statiche, ovvero di lunga durata e distribuite raramente. Dopo essere stata distribuita, una rete virtuale può essere usata in nuove distribuzioni senza alcun effetto negativo sull'infrastruttura. Si pensi a una rete virtuale come se fosse analoga a uno switch di rete hardware tradizionale, il quale non richiede una nuova configurazione a ogni distribuzione. In una rete virtuale configurata in modo appropriato, è possibile continuare a distribuire nuovi server nella rete virtuale più volte, apportando solo le modifiche indispensabili durante il ciclo di vita della rete virtuale.

Per creare questo ambiente personalizzato, è necessario installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e connetterla a un account Azure usando [az login](/cli/azure/#login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myVnet* e *myVM*.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima creare un gruppo di risorse di Azure per organizzare tutti gli elementi che si creano durante questa procedura dettagliata. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Creare il gruppo di risorse con [az group create](/cli/azure/group#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-the-virtual-network"></a>Creare la rete virtuale

Viene create una rete virtuale di Azure nella quale avviare le macchine virtuali. Per altre informazioni sulle reti virtuali, vedere [Creare una rete virtuale usando l'interfaccia della riga di comando di Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md). Creare la rete virtuale con [az network vnet create](/cli/azure/network/vnet#create). L'esempio seguente crea una rete virtuale denominata *myVnet* e una subnet denominata *mySubnet*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.10.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.10.1.0/24
```

## <a name="create-the-network-security-group"></a>Creare il gruppo di sicurezza di rete

I gruppi di sicurezza di rete di Azure sono analoghi a un firewall a livello di rete. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Come creare gruppi di sicurezza di rete usando l'interfaccia della riga di comando di Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md). Creare il gruppo di sicurezza di rete con [az network nsg create](/cli/azure/network/nsg#create). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Aggiungere una regola di assenso SSH in ingresso

La macchina virtuale deve accedere da Internet, pertanto è necessaria una regola che consenta di lasciare entrare il traffico in ingresso verso la porta 22 della macchina virtuale. Aggiungere una regole in ingresso per il gruppo di sicurezza di rete con [az network nsg rule create](/cli/azure/network/nsg/rule#create). L'esempio seguente crea una regola denominata *myNetworkSecurityGroupRuleSSH*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --priority 1000 \
    --protocol tcp \
    --destination-port-range 22 \
```

## <a name="attach-the-subnet-to-the-network-security-group"></a>Collegare la subnet al gruppo di sicurezza di rete

È possibile applicare le regole del gruppo di sicurezza di rete a una subnet o a una specifica interfaccia di rete. Collegare quindi il gruppo di sicurezza di rete alla subnet. Collegare la subnet al gruppo di sicurezza di rete con [az network vnet subnet update](/cli/azure/network/vnet/subnet#update):

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="add-a-virtual-network-interface-card-to-the-subnet"></a>Aggiungere una scheda di interfaccia di rete virtuale alla subnet

Le schede di interfaccia di rete virtuale sono importanti in quanto è possibile riusarle connettendole a macchine virtuali differenti. In questo modo la scheda di interfaccia di rete virtuale diventa una risorsa statica, mentre le macchine virtuali possono essere temporanee. Creare una scheda di interfaccia di rete virtuale e associarla alla subnet con [az network nic create](/cli/azure/network/nic#create). L'esempio seguente crea una scheda di interfaccia di rete virtuale denominata *myNic*:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Distribuire la macchina virtuale nell'infrastruttura di rete virtuale

A questo punto sono disponibili una rete virtuale e un gruppo di sicurezza di rete che protegge la subnet bloccando tutto il traffico in ingresso tranne quello verso la porta 22 per SSH. È ora possibile distribuire la macchina virtuale all'interno di questa infrastruttura di rete esistente.

Creare la macchina virtuale con [az vm create](/cli/azure/vm#create). Per altre informazioni sui flag da usare con l'interfaccia della riga di comando di Azure 2.0 per distribuire una macchina virtuale completa, vedere [Creare un ambiente Linux completo usando l'interfaccia della riga di comando di Azure](create-cli-complete.md).

Nell'esempio seguente viene creata una VM usando Azure Managed Disks. Questi dischi vengono gestiti dalla piattaforma Azure e non richiedono alcuna pianificazione o alcuna posizione per l'archiviazione. Per altre informazioni sui dischi gestiti, vedere [Azure Managed Disks overview](../../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks). Se si desidera usare dischi non gestiti, vedere la nota aggiuntiva seguente.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Se si usa Managed Disks, ignorare questo passaggio. Se si desidera usare dischi non gestiti, è necessario aggiungere i parametri aggiuntivi seguenti al comando della procedura per creare dischi non gestiti nell'account di archiviazione denominato `mystorageaccount`: 

```azurecli
    --use-unmanaged-disk \
    --storage-account mystorageaccount
```

Usando i flag dell'interfaccia della riga di comando per chiamare le risorse esistenti, si indica ad Azure di distribuire la macchina virtuale all'interno della rete esistente. Una volta distribuite la rete virtuale e la subnet possono essere usate come risorse statiche o permanenti nell'area di Azure. In questo esempio non si è creato e assegnato un indirizzo IP pubblico alla scheda di interfaccia di rete virtuale, pertanto questa macchina virtuale non è accessibile pubblicamente da Internet. Per altre informazioni, vedere [Creare una macchina virtuale con un IP pubblico statico usando l'interfaccia della riga di comando di Azure](../../virtual-network/virtual-network-deploy-static-pip-arm-cli.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle modalità per creare macchine virtuali in Azure, vedere le risorse seguenti:

* [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](../windows/cli-deploy-templates.md)
* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](create-cli-complete.md)
* [Creare una VM Linux in Azure usando i modelli](create-ssh-secured-vm-from-template.md)

