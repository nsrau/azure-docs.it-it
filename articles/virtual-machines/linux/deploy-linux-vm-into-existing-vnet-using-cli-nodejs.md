---
title: Distribuire macchine virtuali Linux in una rete esistente con l&quot;interfaccia della riga di comando Azure 1.0 | Microsoft Docs
description: Come distribuire una macchina virtuale Linux in una rete virtuale esistente usando l&quot;interfaccia della riga di comando di Azure 1.0
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
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 767a3f7cadba6b1e71e5a8f5995a9db090e419dd
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-cli-10"></a>Come distribuire una macchina virtuale Linux in una rete virtuale Azure esistente con l'interfaccia della riga di comando di Azure 1.0

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure 1.0 per distribuire una macchina virtuale in una rete virtuale esistente. I requisiti sono:

- [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)
- [File di chiavi SSH pubbliche e private](mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](deploy-linux-vm-into-existing-vnet-using-cli.md): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa


## <a name="quick-commands"></a>Comandi rapidi

Se si vuole eseguire rapidamente l'attività, la sezione seguente indica dettagliatamente i comandi necessari. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, [a partire da qui](deploy-linux-vm-into-existing-vnet-using-cli.md#detailed-walkthrough).

Prerequisiti: gruppo di risorse, rete virtuale, gruppo di sicurezza di rete con SSH in ingresso, subnet. Sostituire gli esempi con le impostazioni desiderate.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Distribuire la macchina virtuale nell'infrastruttura di rete virtuale

```azurecli
azure vm create myVM \
    -g myResourceGroup \
    -l eastus \
    -y linux \
    -Q Debian \
    -o mystorageaccount \
    -u myAdminUser \
    -M ~/.ssh/id_rsa.pub \
    -n myVM \
    -F myVNet \
    -j mySubnet \
    -N myVNic
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

Gli asset di Azure, come le reti virtuali e i gruppi di sicurezza di rete devono essere risorse statiche, ovvero di lunga durata e distribuite raramente. Dopo essere stata distribuita, una rete virtuale può essere usata in nuove distribuzioni senza alcun effetto negativo sull'infrastruttura. Si pensi a una rete virtuale come se fosse uno switch di rete hardware tradizionale. Non è necessario configurare un nuovo switch hardware a ogni distribuzione. In una rete virtuale configurata in modo appropriato, è possibile continuare a distribuire nuovi server più volte, apportando solo le modifiche indispensabili durante il ciclo di vita della rete virtuale.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima creare un gruppo di risorse per organizzare tutti gli elementi che si creano durante questa procedura dettagliata. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

```azurecli
azure group create myResourceGroup --location eastus
```

## <a name="create-the-vnet"></a>Creare la rete virtuale

Il primo passaggio consiste nella compilazione di una rete virtuale nella quale eseguire le macchine virtuali. Ai fini di questa procedura, la rete virtuale contiene una subnet. Per altre informazioni sulle reti virtuali di Azure, vedere [Creare una rete virtuale (classica) usando l'interfaccia della riga di comando di Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md).

```azurecli
azure network vnet create myVNet \
    --resource-group myResourceGroup \
    --address-prefixes 10.10.0.0/24 \
    --location eastus
```

## <a name="create-the-network-security-group"></a>Creare il gruppo di sicurezza di rete

La subnet è protetta da un gruppo di sicurezza di rete esistente, quindi creare il gruppo di sicurezza di rete prima della subnet. I gruppi di sicurezza di rete di Azure sono analoghi a un firewall a livello di rete. Per altre informazioni sui gruppi di sicurezza di rete di Azure, vedere [Come creare gruppi di sicurezza di rete usando l'interfaccia della riga di comando di Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)

```azurecli
azure network nsg create myNetworkSecurityGroup \
    --resource-group myResourceGroup \
    --location eastus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Aggiungere una regola di assenso SSH in ingresso

La macchina virtuale deve accedere da Internet, pertanto è necessaria una regola che consenta di lasciare entrare il traffico in ingresso verso la porta 22 della macchina virtuale.

```azurecli
azure network nsg rule create inboundSSH \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range 22 \
    --destination-address-prefix 10.10.0.0/24 \
    --destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Aggiungere una subnet alla rete virtuale

Le macchine virtuali all'interno della rete virtuale devono trovarsi in una subnet. Ogni rete virtuale può avere più subnet. Creare la subnet e associarla al gruppo di sicurezza di rete.

```azurecli
azure network vnet subnet create mySubNet \
    --resource-group myResourceGroup \
    --vnet-name myVNet \
    --address-prefix 10.10.0.0/26 \
    --network-security-group-name myNetworkSecurityGroup
```

La subnet è ora aggiunta all'interno della rete virtuale e associata a un gruppo di sicurezza di rete e alla regola.


## <a name="add-a-vnic-to-the-subnet"></a>Aggiungere una scheda di interfaccia di rete virtuale alla subnet

Le schede di interfaccia rete virtuale sono importanti in quanto è possibile riusarle connettendole a macchine virtuali differenti. In questo modo la scheda di interfaccia di rete virtuale diventa una risorsa statica, mentre le macchine virtuali possono essere temporanee. Creare una scheda di interfaccia di rete virtuale e associarla alla subnet creata nel passaggio precedente.

```azurecli
azure network nic create myVNic \
    --resource-group myResourceGroup \
    --location eastus \
    ---subnet-vnet-name myVNet \
    --subnet-name mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Distribuire la macchina virtuale nella rete virtuale e nel gruppo di sicurezza di rete

A questo punto sono disponibili una rete virtuale, una subnet all'interno di tale rete virtuale e un gruppo di sicurezza di rete che protegge la subnet da tutto il traffico in ingresso tranne quello verso la porta 22 per SSH. È ora possibile distribuire la macchina virtuale all'interno di questa infrastruttura di rete esistente.

Con l'interfaccia della riga di comando di Azure e il comando `azure vm create`, la VM Linux viene distribuita nel gruppo di risorse, nella rete virtuale, nella subnet e nella scheda di rete virtuale esistenti. Per altre informazioni sull'utilizzo dell'interfaccia della riga di comando per distribuire una macchina virtuale completa, vedere [Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure](create-cli-complete.md).

```azurecli
azure vm create myVM \
    --resource-group myResourceGroup \
    --location eastus \
    --os-type linux \
    --image-urn Debian \
    --storage-account-name mystorageaccount \
    --admin-username myAdminUser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --vnet-name myVNet \
    --vnet-subnet-name mySubnet \
    --nic-name myVNic
```

Usando i flag dell'interfaccia della riga di comando per chiamare le risorse esistenti, si indica ad Azure di distribuire la macchina virtuale all'interno della rete esistente. Una volta distribuite la rete virtuale e la subnet possono essere usate come risorse statiche o permanenti nell'area di Azure.  

## <a name="next-steps"></a>Passaggi successivi

* [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](../windows/cli-deploy-templates.md)
* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](create-cli-complete.md)
* [Creare una VM Linux in Azure usando i modelli](create-ssh-secured-vm-from-template.md)

