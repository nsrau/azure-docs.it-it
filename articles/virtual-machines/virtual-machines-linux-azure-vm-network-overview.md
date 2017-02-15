---
title: Panoramica di rete delle macchine virtuali Linux e Azure | Microsoft Docs
description: Una panoramica di rete delle VM Linux e Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: b5420e35-0463-4456-9803-6142db150f2e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 9cc4fcb68148ef64829fff6135449a3c0efb75d0


---
# <a name="azure-and-linux-vm-network-overview"></a>Panoramica di rete delle macchine virtuali Linux e Azure
## <a name="virtual-networks"></a>Reti virtuali
Una rete virtuale di Azure (VNet) è una rappresentazione della propria rete personalizzata nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. È possibile controllare completamente i blocchi di indirizzi IP, le impostazioni DNS, i criteri di sicurezza e le tabelle di route in questa rete. È anche possibile segmentare ulteriormente la rete virtuale in subnet e avviare macchine virtuali (VM) IaaS di Azure e/o servizi cloud (istanze del ruolo PaaS). È anche possibile connettere la rete virtuale alla rete locale usando una delle opzioni di connettività disponibili in Azure. In pratica è possibile espandere la rete ad Azure, con il controllo completo sui blocchi di indirizzi IP con tutti i vantaggi di livello aziendale offerti da Azure.

* [Panoramica di Rete virtuale.](../virtual-network/virtual-networks-overview.md)

Per creare una rete VNet usando l'interfaccia della riga di comando di Azure, accedere qui in modo di seguire la procedura dettagliata.

* [Come creare una rete virtuale con l'interfaccia della riga di comando di Azure](../virtual-network/virtual-networks-create-vnet-arm-cli.md)

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Un gruppo di sicurezza di rete contiene un elenco di regole dell'elenco di controllo di accesso (ACL) che consentono o rifiutano il traffico di rete alle istanze VM in una rete virtuale. I gruppi di sicurezza di rete possono essere associati a subnet o singole istanze VM in una subnet. Quando un gruppo di sicurezza di rete viene associato a una subnet, le regole dell'elenco di controllo di accesso si applicano a tutte le istanze VM in tale subnet. Inoltre il traffico verso una singola VM può essere ulteriormente limitato associando un gruppo di sicurezza di rete direttamente a tale VM.

* [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
* [Come creare gruppi di sicurezza di rete nell'interfaccia della riga di comando di Azure](../virtual-network/virtual-networks-create-nsg-arm-cli.md)

## <a name="user-defined-routes"></a>Route definite dall'utente
Quando si aggiungono macchine virtuali (VM) a una rete virtuale (VNet) in Azure, si noterà che le macchine virtuali sono in grado di comunicare con altri in rete, automaticamente. Non è necessario specificare un gateway, anche se le macchine virtuali si trovano in subnet diverse. Lo stesso vale per la comunicazione tra macchine virtuali e rete Internet pubblica e anche rete locale se è presente una connessione ibrida tra Azure e il proprio Data Center.

* [Informazioni sulle route definite dall'utente e sull'inoltro IP](../virtual-network/virtual-networks-udr-overview.md)
* [Creare una route UDR l'interfaccia della riga di comando di Azure](../virtual-network/virtual-network-create-udr-arm-cli.md)

## <a name="associating-a-fqdn-to-your-linux-vm"></a>Associazione di un FQDN alla macchina virtuale Linux
Quando si crea una macchina virtuale nel portale di Azure usando il modello di distribuzione Azure Resource Manager, il portale crea automaticamente una risorsa IP pubblica per la macchina virtuale. Usare questo indirizzo IP per accedere in remoto alla VM. Anche se, per impostazione predefinita, il portale non crea un nome di dominio completo, o FQDN (Fully Qualified Domain Name), è possibile aggiungerne uno dopo aver creato la VM.

* [Creare un nome di dominio completo nel portale di Azure](virtual-machines-linux-portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-nics"></a>NIC virtuali
Un'interfaccia di rete è l'interconnessione tra una macchina virtuale (VM) e la rete software sottostante. Questo articolo spiega cos'è un'interfaccia di rete e come viene usata nel modello di distribuzione Azure Resource Manager.

* [Panoramica sulle interfacce di rete virtuali](../virtual-network/virtual-network-network-interface-overview.md)

## <a name="virtual-nics-and-dns-labeling"></a>Etichettatura DNS e NIC virtuali
Se si dispone di un server che deve essere permanente, ma tale server viene gestito in maniera grossolana con frequenti eliminazioni e distribuzioni, può essere utile usare l'etichettatura DNS sulla propria scheda di interfaccia di rete per mantenere il nome nella VNET.  La procedura dettagliata descritta di seguito consente di configurare una NIC denominata in modo permanente con un indirizzo IP statico.

* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network-gateways"></a>Gateway di rete virtuale
Un gateway di rete virtuale viene usato per inviare traffico di rete tra reti virtuali di Azure e percorsi locali e anche tra reti virtuali in Azure. Quando si configura un gateway VPN, è necessario creare e configurare un gateway di rete virtuale e una connessione del gateway di rete virtuale.

* [Informazioni sul gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="internal-load-balancing"></a>Bilanciamento del carico interno
Azure Load Balancer è un servizio di bilanciamento del carico di livello 4 (TCP, UDP). Il servizio di bilanciamento del carico offre disponibilità elevata distribuendo il traffico in ingresso tra istanze del servizio integre in servizi cloud o macchine virtuali in un set di bilanciamento del carico . Azure Load Balancer può anche presentare tali servizi su più porte, più indirizzi IP o entrambi.

* [Creazione di un servizio di bilanciamento del carico interno tramite l'interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)




<!--HONumber=Nov16_HO3-->


