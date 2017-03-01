---
title: Uso del DNS interno per la risoluzione dei nomi di macchine virtuali in Azure | Documentazione Microsoft
description: Uso del DNS interno per la risoluzione dei nomi di macchine virtuali in Azure.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 8584606666fe93630f6486c16350a619787c8d14
ms.openlocfilehash: 9d3b1f7582a487f5c9f4e58852d41fac91028bdc
ms.lasthandoff: 02/17/2017


---

# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Uso del DNS interno per la risoluzione dei nomi di macchine virtuali in Azure

Questo articolo illustra come impostare nomi DNS interni statici per macchine virtuali Linux usando schede di interfaccia di rete virtuale (VNic) e nomi di etichette DNS. Si ricorre ai nomi DNS statici per i servizi di infrastruttura permanenti, ad esempio per un server di compilazione Jenkins, usato per questo documento, o un server Git.

I requisiti sono:

* [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)
* [File di chiavi SSH pubbliche e private](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0 (anteprima)](virtual-machines-linux-static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): l'interfaccia della riga di comando di nuova generazione per il modello di distribuzione di gestione delle risorse


## <a name="quick-commands"></a>Comandi rapidi

Se si vuole eseguire rapidamente l'attività, la sezione seguente indica dettagliatamente i comandi necessari. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, [a partire da qui](#detailed-walkthrough).  

Prerequisiti: gruppo di risorse, rete virtuale, gruppo di sicurezza di rete con SSH in ingresso, subnet.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Creare una scheda di interfaccia di rete virtuale con un nome DNS interno statico

Il flag dell'interfaccia della riga di comando `-r` consente di impostare l'etichetta DNS, che indica il nome DNS statico per la scheda di interfaccia di rete virtuale.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Distribuire la macchina virtuale nella rete virtuale, associare il gruppo di sicurezza di rete e connettere la scheda di interfaccia di rete virtuale

Il flag `-N` consente di connette la scheda di interfaccia virtuale alla macchina virtuale durante la distribuzione di Azure.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

In Azure, un'infrastruttura interamente basata sul modello CiCd (Continuous Integration and Continuous Deployment) richiede la disponibilità di server statici o di lunga durata.  È consigliabile che gli asset di Azure, come le reti virtuali e i gruppi di sicurezza di rete, siano risorse statiche, ovvero di lunga durata e distribuite raramente.  Dopo essere stata distribuita, una rete virtuale può essere usata in nuove distribuzioni senza alcun effetto negativo sull'infrastruttura.  Aggiungendo a questa rete statica un server repository Git e un server di automazione Jenkins, è possibile distribuire un modello CiCd in un ambiente di test o di sviluppo.  

I nomi DNS interni sono risolvibili solo all'interno di una rete virtuale di Azure.  Trattandosi di nomi interni, non sono risolvibili esternamente in Internet e garantiscono quindi una sicurezza aggiuntiva per l'infrastruttura.

_Sostituire i nomi nell'esempio con nomi personalizzati._

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Per organizzare tutti gli elementi creati in questa procedura dettagliata è necessario un gruppo di risorse.  Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Creare la rete virtuale

Il primo passaggio consiste nella compilazione di una rete virtuale nella quale eseguire le macchine virtuali.  Ai fini di questa procedura, la rete virtuale contiene una subnet.  Per altre informazioni sulle reti virtuali di Azure, vedere [Creare una rete virtuale (classica) usando l'interfaccia della riga di comando di Azure](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Creare il gruppo di sicurezza di rete

La subnet è protetta da un gruppo di sicurezza di rete esistente che viene creato prima della subnet.  I gruppi di sicurezza di rete di Azure sono analoghi ai firewall a livello di rete.  Per altre informazioni sui gruppi di sicurezza di Azure, vedere [Come creare gruppi di sicurezza di rete nell'interfaccia della riga di comando di Azure](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Aggiungere una regola di assenso SSH in ingresso

La VM Linux deve accedere da Internet, pertanto è necessaria una regola che consenta di lasciare entrare il traffico in ingresso verso la porta 22 della VM Linux.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Aggiungere una subnet alla rete virtuale

Le macchine virtuali all'interno della rete virtuale devono trovarsi in una subnet.  Ogni rete virtuale può avere più subnet.  Creare la subnet e associarla al gruppo di sicurezza di rete per aggiungere un firewall alla subnet.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

La subnet è ora aggiunta all'interno della rete virtuale e associata a un gruppo di sicurezza di rete e alla relativa regola.

## <a name="creating-static-dns-names"></a>Creazione di nomi DNS statici

Sebbene Azure sia molto flessibile, per poter usare nomi DNS per la risoluzione dei nomi di macchine virtuali, è necessario crearli come schede di interfaccia di rete virtuale mediante l'etichettatura DNS.  Le schede di interfaccia di rete virtuale sono importanti in quanto possono essere riusate connettendole ad altre macchine virtuali. In questo senso, la scheda di interfaccia di rete virtuale è una risorsa statica, mentre le macchine virtuali possono essere temporanee.  Applicando l'etichettatura DNS a una scheda di interfaccia di rete virtuale, è possibile abilitare nella rete virtuale la risoluzione di nomi semplici provenienti da altre macchine virtuali.  L'uso di nomi risolvibili consente anche ad altre macchine virtuali di accedere al server di automazione in base al nome DNS `Jenkins` o al server Git come `gitrepo`.  Creare una scheda di rete virtuale e associarla alla subnet creata nel passaggio precedente.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Distribuire la macchina virtuale nella rete virtuale e nel gruppo di sicurezza di rete

A questo punto sono disponibili una rete virtuale, una subnet all'interno di tale rete virtuale e un gruppo di sicurezza di rete che funge da firewall per proteggere la subnet bloccando tutto il traffico in ingresso tranne quello verso la porta 22 per SSH.  È ora possibile distribuire la macchina virtuale all'interno di questa infrastruttura di rete esistente.

Con l'interfaccia della riga di comando di Azure e il comando `azure vm create`, la VM Linux viene distribuita nel gruppo di risorse, nella rete virtuale, nella subnet e nella scheda di rete virtuale esistenti.  Per altre informazioni sull'utilizzo dell'interfaccia della riga di comando per distribuire una macchina virtuale completa, vedere [Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

Usando i flag dell'interfaccia della riga di comando per chiamare le risorse esistenti, si indica ad Azure di distribuire la macchina virtuale all'interno della rete esistente.  Come già detto, dopo essere state distribuite la rete virtuale e la subnet possono essere usate come risorse statiche o permanenti nell'area di Azure.  

## <a name="next-steps"></a>Passaggi successivi

* [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux in Azure usando i modelli](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

