---
title: Distribuire le macchine virtuali Linux nelle reti esistenti - Portale di Azure | Documentazione Microsoft
description: Distribuire una macchina virtuale Linux in una rete virtuale di Azure esistente tramite il portale.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: e64449991bc28427d8f559ed13c3bdf9160488db
ms.openlocfilehash: 8e75aa3b38df512dc93031d5a0e9047febe7ab74


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-portal"></a>Distribuire una macchina virtuale Linux in una rete virtuale di Azure esistente protetta con un gruppo di sicurezza di rete tramite il portale

Questo articolo illustra come distribuire una macchina virtuale in una rete virtuale esistente.  È consigliabile che gli asset di Azure, come le reti virtuali e i gruppi di sicurezza di rete siano risorse statiche, ovvero di lunga durata e distribuite raramente.  Dopo essere stata distribuita, una rete virtuale può essere usata in nuove distribuzioni costanti, senza alcun effetto negativo sull'infrastruttura.  Si pensi a una rete virtuale come se fosse analoga a uno switch di rete hardware tradizionale, il quale non richiede una nuova configurazione a ogni distribuzione.  

In una rete virtuale configurata in modo appropriato, è possibile continuare a distribuire nuovi server più volte, apportando solo le modifiche indispensabili durante il ciclo di vita della rete virtuale.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Innanzitutto, viene distribuito un gruppo di risorse per organizzare tutti gli elementi creati durante questa procedura dettagliata.  Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![createResourceGroup](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>Creare la rete virtuale

Il primo passaggio consiste nella compilazione di una rete virtuale nella quale eseguire le macchine virtuali.  La rete virtuale contiene una subnet. Il gruppo di sicurezza di rete viene associato alla subnet in un passaggio successivo.

![createVNet](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>Aggiungere una scheda di interfaccia di rete virtuale alla subnet

Le schede di rete virtuale sono importanti in quanto possono essere connesse a diverse macchine virtuali. In questo senso la scheda di rete virtuale è una risorsa statica, mentre le macchine virtuali possono essere temporanee. Creare una scheda di rete virtuale e associarla alla subnet creata nel passaggio precedente.

![createVNic](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-nsg"></a>Creare il gruppo di sicurezza di rete

I gruppi di sicurezza di rete di Azure sono analoghi ai firewall a livello di rete. Per altre informazioni, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![createNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>Aggiungere una regola di assenso SSH in ingresso

La VM Linux deve accedere da Internet, pertanto viene creata una regola che consente il traffico in ingresso verso la porta 22 della VM Linux.

![createInboundSSH](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>Associare il gruppo di sicurezza di rete alla subnet

Dopo aver creato la rete virtuale e la subnet, il gruppo di sicurezza di rete viene associato alla subnet.  È possibile associare i gruppi di sicurezza di rete a un'intera subnet o a una scheda di rete virtuale singola.  In questo modo il firewall filtra il traffico a livello di subnet e tutte le schede di rete virtuali e le macchine virtuali all'interno della subnet sono protette dal gruppo di sicurezza di rete; se invece il gruppo sicurezza di rete è associato a una singola scheda di rete virtuale, proteggerà solo una macchina virtuale.

![associateNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Distribuire la macchina virtuale nella rete virtuale e nel gruppo di sicurezza di rete

Tramite il portale di Azure, la VM Linux viene distribuita nel gruppo di risorse, nella rete virtuale, nella subnet e nella scheda di rete virtuale esistenti.

![createVM](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

Usando il portale per scegliere le risorse esistenti, si indica ad Azure di distribuire la macchina virtuale all'interno della rete esistente.  Come già detto, dopo essere state distribuite la rete virtuale e la subnet possono essere usate come risorse statiche o permanenti nell'area di Azure.  

## <a name="next-steps"></a>Passaggi successivi

* [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux in Azure usando i modelli](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Jan17_HO4-->


