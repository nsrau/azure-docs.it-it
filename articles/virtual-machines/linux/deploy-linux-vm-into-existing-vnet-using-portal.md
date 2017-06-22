---
title: Distribuire macchine virtuali Linux in una rete esistente con il portale di Azure | Microsoft Docs
description: Distribuire una macchina virtuale Linux in una rete virtuale di Azure esistente tramite il portale.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 964c0fc41773b50a9fbe476df47460484c2ada66
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-portal"></a>Come distribuire una macchina virtuale Linux in una rete virtuale Azure esistente con il portale di Azure

Questo articolo illustra come usare distribuire una macchina virtuale in una rete virtuale esistente. Gli asset di Azure, come le reti virtuali e i gruppi di sicurezza di rete, devono essere risorse statiche, ovvero di lunga durata e distribuite raramente. Dopo essere stata distribuita, una rete virtuale può essere usata in nuove distribuzioni costanti, senza alcun effetto negativo sull'infrastruttura. Si pensi a una rete virtuale come se fosse analoga a uno switch di rete hardware tradizionale, il quale non richiede una nuova configurazione a ogni distribuzione.  

In una rete virtuale configurata in modo appropriato, è possibile continuare a distribuire nuovi server più volte, apportando solo le modifiche indispensabili durante il ciclo di vita della rete virtuale.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima creare un gruppo di risorse per organizzare tutti gli elementi che si creano durante questa procedura dettagliata. Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>Creare la rete virtuale

Creare una rete virtuale in cui eseguire le macchine virtuali. La rete virtuale contiene una subnet e verrà associata al gruppo di sicurezza di rete con questa subnet in un passaggio successivo.

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>Aggiungere una scheda di interfaccia di rete virtuale alla subnet

Le schede di interfaccia rete virtuale sono importanti in quanto è connetterle a macchine virtuali diverse. In questo modo la scheda di interfaccia di rete virtuale diventa una risorsa statica, mentre le macchine virtuali possono essere temporanee. Creare una scheda di interfaccia di rete virtuale e associarla alla subnet creata nel passaggio precedente.

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-network-security-group"></a>Creare il gruppo di sicurezza di rete

I gruppi di sicurezza di rete di Azure sono analoghi a un firewall a livello di rete. Per altre informazioni sui gruppi di sicurezza di rete di Azure, vedere [Che cos'è un gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md).

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>Aggiungere una regola di assenso SSH in ingresso

La macchina virtuale deve accedere da Internet, pertanto viene creata una regola che consente di lasciare entrare il traffico in ingresso verso la porta 22 della macchina virtuale.

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>Associare il gruppo di sicurezza di rete alla subnet

Dopo aver creato la rete virtuale e la subnet, il gruppo di sicurezza di rete viene associato alla subnet. È possibile associare i gruppi di sicurezza di rete a un'intera subnet o a una scheda di rete virtuale singola. Con il firewall che filtra il traffico a livello di subnet, tutte le schede di interfaccia di rete virtuale e le macchine virtuali all'interno della subnet sono protette dal gruppo di sicurezza di rete. L'altro approccio è l'associazione del gruppo di sicurezza di rete con una sola scheda di interfaccia di rete virtuale e con la protezione di una sola macchina virtuale.

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Distribuire la macchina virtuale nella rete virtuale e nel gruppo di sicurezza di rete

Tramite il portale di Azure, la VM Linux viene distribuita nel gruppo di risorse, nella rete virtuale, nella subnet e nella scheda di rete virtuale esistenti.

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

Usando il portale per scegliere le risorse esistenti, si indica ad Azure di distribuire la macchina virtuale all'interno della rete esistente. Una volta distribuite la rete virtuale e la subnet possono essere usate come risorse statiche o permanenti nell'area di Azure.  

## <a name="next-steps"></a>Passaggi successivi

* [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](../windows/cli-deploy-templates.md)
* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](create-cli-complete.md)
* [Creare una VM Linux in Azure usando i modelli](create-ssh-secured-vm-from-template.md)

