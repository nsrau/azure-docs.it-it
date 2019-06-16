---
title: Linee guida per i file di Azure NetApp pianificazione di rete | Microsoft Docs
description: Descrive linee guida che consentono di progettare un'architettura di rete effettive usando file di Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: fa2de14ada5d24531dfecc7f2f709a87f39ea6cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65826473"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Linee guida per la pianificazione della rete per Azure NetApp Files

Pianificazione di architettura di rete è un elemento fondamentale della progettazione di qualsiasi infrastruttura dell'applicazione. Questo articolo consente di progettare un'architettura di rete effettive per i carichi di lavoro sfruttare le funzionalità avanzate NetApp di file di Azure.

Volumi di NetApp file di Azure sono progettati per essere contenuti in una subnet scopo speciale denominata [delegate subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) all'interno della rete virtuale di Azure. Pertanto, è possibile accedere i volumi direttamente dalla rete virtuale, da reti virtuali con peering nella stessa area o in locale tramite un Gateway di rete virtuale (Gateway VPN o ExpressRoute), se necessario. La subnet sia dedicata NetApp in file di Azure e non è disponibile connettività ad altri servizi di Azure o a internet.

## <a name="considerations"></a>Considerazioni  

È necessario comprendere alcuni aspetti durante la pianificazione per Azure NetApp file nella rete.

### <a name="constraints"></a>Vincoli

Non sono attualmente supportate per i file di Azure NetApp funzionalità riportate di seguito: 

* Gruppi di sicurezza di rete (Nsg) sulla subnet
* Route definite dall'utente (Udr) con hop successivo come subnet di file di Azure NetApp
* Criteri di Azure, ad esempio, personalizzati denominazione criteri, sull'interfaccia del file di Azure NetApp
* Servizi di bilanciamento del carico per il traffico di Azure i file di NetApp

File di Azure NetApp si applicano le restrizioni di rete seguenti:

* Il numero di macchine virtuali che possono connettersi a un volume (con una rete virtuale o tra reti virtuali con peering) non può superare i 1000.
* In ogni rete virtuale di Azure (VNet), è possibile delegare solo una subnet in Azure NetApp Files.


### <a name="supported-network-topologies"></a>Topologie di rete supportate

La tabella seguente descrive le topologie di rete supportate da file di Azure NetApp.  Vengono inoltre descritte le soluzioni alternative per le topologie non supportate. 

|    Topologie    |    è supportato    |     Soluzione alternativa    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Connettività al volume in una rete virtuale locale    |    Yes    |         |
|    Connettività al volume in una rete virtuale con peering (stessa area)    |    Yes    |         |
|    Connettività al volume in una rete virtuale con peering (regione o tra il peering globale)    |    No    |    Nessuna    |
|    Connettività a un volume su gateway di ExpressRoute    |    Yes    |         |
|    Connettività da locale a un volume in un rete virtuale spoke su gateway ExpressRoute e il peering con il transito gateway della rete virtuale    |    No    |    Creare una subnet di delegato nella rete virtuale (rete virtuale di Azure con il Gateway) dell'hub    |
|    Connettività da locale a un volume in un rete virtuale spoke tramite gateway VPN    |    Yes    |         |
|    Connettività da locale a un volume in un rete virtuale spoke tramite gateway VPN e il peering con il transito gateway della rete virtuale    |    Yes    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Rete virtuale per i volumi di file di Azure NetApp

In questa sezione illustra i concetti che assiste nella pianificazione della rete virtuale.

### <a name="azure-virtual-networks"></a>Reti virtuali di Azure

Prima del provisioning di un volume di file di Azure NetApp, è necessario creare una rete virtuale di Azure (VNet) o usarne uno già esistente nella sottoscrizione. La rete virtuale definisce il limite di rete del volume.  Per altre informazioni sulla creazione di reti virtuali, vedere la [documentazione sulla rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Subnet

Subnet segmentare la rete virtuale in spazi di indirizzi separati che possono essere utilizzati per le risorse di Azure in essi contenuti.  Volumi di NetApp file di Azure sono contenuti in una subnet con finalità speciali chiamata [delegate subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

La delega di subnet offre autorizzazioni esplicite per il servizio file NetApp di Azure per creare le risorse specifiche del servizio nella subnet.  Usa un identificatore univoco nella distribuzione del servizio. In questo caso, viene creata un'interfaccia di rete per abilitare la connettività NetApp in file di Azure.

Se si usa una nuova rete virtuale, è possibile creare una subnet e delegare la subnet NetApp in file di Azure seguendo le istruzioni [delegare una subnet per i file di Azure NetApp](azure-netapp-files-delegate-subnet.md). È inoltre possibile delegare una subnet vuota esistente che non è stata già delegata ad altri servizi.

Se è stato eseguito il peering della rete virtuale con un'altra rete virtuale, è possibile espandere lo spazio degli indirizzi della rete virtuale. Per questo motivo, è necessario creare nello spazio di indirizzi della rete virtuale della nuova subnet delegata. Se è necessario estendere lo spazio degli indirizzi, è necessario eliminare il peering prima di espandere lo spazio degli indirizzi delle reti.

### <a name="udrs-and-nsgs"></a>Route definite dall'utente e gli Nsg

Gruppi di sicurezza di rete (Nsg) con un hop successivo non sono utilizzabile come subnet delegata per i file di Azure NetApp. Analogamente, route definite dall'utente (Udr) inoltre non sono supportate. 

In alternativa, è possibile applicare gli Nsg ad altre subnet che consentono o negano il traffico da e verso la subnet di Azure i file di NetApp Delegate.  

## <a name="azure-native-environments"></a>Ambienti nativi di Azure

Il diagramma seguente illustra un ambiente di Azure nativo:

![Ambiente di rete di Azure native](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Rete virtuale locale

Uno scenario di base consiste nel creare o connettersi a un volume di file di Azure NetApp da una macchina virtuale (VM) nella stessa rete virtuale. Per la rete virtuale 2 nel diagramma precedente, Volume 1 viene creato in una subnet del delegato e può essere montata su 1 macchina virtuale nella subnet predefinita.

### <a name="vnet-peering"></a>Peering reti virtuali

Se si dispone di altre reti virtuali nella stessa area che richiedono l'accesso alle risorse di altro, le reti virtuali possono essere connesse tramite [peering reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) per abilitare la connettività sicura tramite l'infrastruttura di Azure. 

Prendere in considerazione della rete virtuale 2 e rete virtuale 3 nel diagramma precedente. Se 1 macchina virtuale deve connettersi alla macchina virtuale 2 e Volume 2 o se è necessario connettersi da VM 1 o Volume 1 2 macchine Virtuali, è necessario abilitare la rete virtuale di peering tra la rete virtuale 2 e 3 della rete virtuale. 

Inoltre, prendere in considerazione uno scenario in cui è stato eseguito il peering della rete virtuale 1 con rete virtuale 2 e ha eseguito il peering di rete virtuale 2 con 3 di rete virtuale nella stessa area. Le risorse dalla rete virtuale 1 possono connettersi alle risorse nella rete virtuale 2, ma non è possibile connettersi alle risorse nella rete virtuale 3, a meno che non ha eseguito il peering della rete virtuale 1 e rete virtuale 3. 

Nel diagramma precedente, anche se 3 macchine Virtuali possono connettersi a Volume 1, 4 macchine Virtuali non è possibile connettersi a Volume 2.  Il motivo è che non viene eseguito il peering di reti virtuali spoke, e _routing di transito non è supportato tramite il peering reti virtuali_.

## <a name="hybrid-environments"></a>Ambienti ibridi

Il diagramma seguente illustra un ambiente ibrido: 

![Ambiente di rete ibrida](../media/azure-netapp-files/azure-netapp-files-networ-hybrid-environment.png)

Nello scenario ibrido, le applicazioni da on-premises data center devono accedere alle risorse in Azure.  Ciò avviene se si desidera estendere il data center di Azure o se si desidera utilizzare servizi nativi di Azure o il ripristino di emergenza. Visualizzare [Gateway VPN di opzioni di pianificazione](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) su come connettere più risorse locali per le risorse in Azure tramite una VPN site-to-site o ExpressRoute.

In una topologia hub-spoke ibrida, l'hub di rete virtuale di Azure funge da un punto centrale della connettività alla rete locale. Gli spoke sono le reti virtuali con peering con l'hub e possono essere usati per isolare i carichi di lavoro.

A seconda della configurazione. È possibile connettere risorse da locale alle risorse nell'hub e spoke.

Nella topologia illustrata in precedenza, la rete locale è connessa a un hub di rete virtuale in Azure, ed esistono spoke 2 il peering reti virtuali con rete virtuale dell'hub.  In questo scenario, le opzioni di connettività supportate per i volumi di file di Azure NetApp sono i seguenti:

* Le risorse locali VM 1 e 2 della macchina virtuale possono connettersi a Volume 1 nell'hub tramite una VPN site-to-site o ExpressRoute. 
* Le risorse locali, VM 1 e 2 della macchina virtuale possono connettersi a Volume 2 o 3 Volume.
* 3 macchine Virtuali nell'hub di rete virtuale può connettersi a volume nello spoke 1 rete virtuale 2 e 3 Volume in spoke 2 della rete virtuale.
* 4 macchine Virtuali dallo spoke di rete virtuale 1 e 5 VM dallo spoke 2 della rete virtuale possono connettersi a Volume 1 nella rete virtuale dell'hub.

4 macchine Virtuali nello spoke 1 rete virtuale non può connettersi a Volume 3 spoke 2 della rete virtuale. Inoltre, 5 VM in spoke VNet2 Impossibile connettersi al Volume 2 nello spoke 1 rete virtuale. Ciò avviene perché non ha eseguito il peering reti virtuali spoke e _routing di transito non è supportato tramite il peering reti virtuali_.

## <a name="next-steps"></a>Passaggi successivi

[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
