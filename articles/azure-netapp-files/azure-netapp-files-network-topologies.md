---
title: Linee guida per la pianificazione della rete per Azure NetApp Files | Microsoft Docs
description: Vengono descritte le linee guida per progettare un'architettura di rete efficace usando Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: ramakk
ms.openlocfilehash: d81ae835fa62c5188c8d71a5ae0563259ab027f3
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797422"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Linee guida per la pianificazione della rete per Azure NetApp Files

La pianificazione dell'architettura di rete è un elemento fondamentale della progettazione di qualsiasi infrastruttura di applicazioni. Questo articolo consente di progettare un'architettura di rete efficace per i carichi di lavoro, in modo da sfruttare al meglio le avanzate funzionalità di Azure NetApp Files.

I volumi di Azure NetApp Files sono progettati per essere contenuti in una subnet per scopi specifici denominata [subnet delegata](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet), appartenente alla rete virtuale di Azure. È quindi possibile accedere ai volumi direttamente dalla propria rete virtuale, da reti virtuali con peering della stessa area o dall'ambiente locale su un gateway di rete virtuale (ExpressRoute o Gateway VPN), in base alle esigenze. La subnet è dedicata ad Azure NetApp Files e non è connessa ad altri servizi di Azure né a Internet.

## <a name="considerations"></a>Considerazioni  

È necessario prendere in considerazione alcuni aspetti quando si pianifica la rete Azure per NetApp Files.

### <a name="constraints"></a>Vincoli

Per Azure NetApp Files non sono attualmente supportate le funzionalità seguenti: 

* Gruppi di sicurezza di rete (NSG) applicati alla subnet delegata
* Route definite dall'utente (UDR) applicate alla subnet delegata
* Criteri di Azure (ad esempio, criteri di denominazione personalizzati) nell'interfaccia di Azure NetApp Files
* Servizi di bilanciamento del carico per il traffico di Azure NetApp Files
* Rete WAN virtuale di Azure 
* Gateway di rete virtuale con ridondanza della zona (SKU di gateway con Az) 
* Gateway di rete virtuale attiva/attiva 

Con Azure NetApp Files è necessario rispettare le restrizioni di rete seguenti:

* Il numero di indirizzi IP in uso in una rete virtuale con Azure NetApp Files (incluse le reti virtuali con peering) non deve superare 1000. Microsoft sta lavorando per aumentare questo limite e soddisfare le esigenze di scalabilità degli utenti. 
* In ogni rete virtuale di Azure (VNet), è possibile delegare solo una subnet in Azure NetApp Files.


### <a name="supported-network-topologies"></a>Topologie di rete supportate

Nella tabella seguente vengono descritte le topologie di rete supportate da Azure NetApp Files.  Vengono inoltre indicate eventuali soluzioni alternative per le topologie non supportate. 

|    Topologie    |    Supportata    |     Soluzione alternativa    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Connettività a un volume in una rete virtuale locale    |    Sì    |         |
|    Connettività a un volume in una rete virtuale con peering (stessa area)    |    Sì    |         |
|    Connettività a un volume in una rete virtuale con peering (in aree diverse o con peering globale)    |    No    |    nessuno    |
|    Connettività a un volume sul gateway ExpressRoute    |    Sì    |         |
|    Connettività da locale a un volume in una rete virtuale spoke tramite gateway ExpressRoute e peering di rete virtuale con transito tramite gateway    |    Sì    |        |
|    Connettività da locale a un volume in una rete virtuale spoke tramite gateway VPN    |    Sì    |         |
|    Connettività da locale a un volume in una rete virtuale spoke tramite gateway VPN e peering di rete virtuale con transito tramite gateway    |    Sì    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Rete virtuale per volumi di Azure NetApp Files

In questa sezione vengono illustrati i concetti che possono semplificare la pianificazione di una rete virtuale.

### <a name="azure-virtual-networks"></a>Reti virtuali di Azure

Prima di eseguire il provisioning di un volume di Azure NetApp Files, è necessario creare una rete virtuale di Azure o usarne una già esistente nella sottoscrizione. La rete virtuale definisce il limite di rete del volume.  Per altre informazioni sulla creazione di reti virtuali, vedere la [documentazione sulle reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Subnet

Le subnet segmentano la rete virtuale in spazi di indirizzi separati usabili dalle risorse di Azure contenute.  I volumi di Azure NetApp Files sono contenuti in una subnet per scopi specifici denominata [subnet delegata](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

La delega della subnet offre al servizio Azure NetApp Files autorizzazioni esplicite per creare le risorse specifiche del servizio nella subnet.  Usa inoltre un identificatore univoco per la distribuzione del servizio. In questo caso, viene creata un'interfaccia di rete per abilitare la connettività ad Azure NetApp Files.

Se si usa una nuova rete virtuale, è possibile creare una subnet e delegarla ad Azure NetApp Files seguendo le istruzioni riportate in [Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md). È possibile delegare anche una subnet vuota esistente che non sia già stata delegata ad altri servizi.

Se è stato eseguito il peering della rete virtuale con un'altra rete virtuale, non è possibile espanderne lo spazio di indirizzi. È questo il motivo per cui la nuova subnet delegata deve essere creata nello spazio di indirizzi della rete virtuale. Se è necessario estendere lo spazio di indirizzi, prima di eseguire questa operazione è necessario eliminare il peering della rete virtuale.

### <a name="udrs-and-nsgs"></a>Route definite dall'utente e gruppi di sicurezza di rete

Nelle subnet delegate per Azure NetApp Files non sono supportati i gruppi di sicurezza di rete né le route definite dall'utente. È tuttavia è possibile applicare route definite dall'utente e gruppi di sicurezza di rete ad altre subnet, anche all'interno della stessa rete virtuale della subnet delegata ad Azure NetApp Files.

* Le route definite dall'utente definiscono quindi i flussi di traffico dalle altre subnet alla subnet delegata ad Azure NetApp Files, in modo che siano allineati al flusso di traffico di ritorno da Azure NetApp Files alle altre subnet che usano le route di sistema.  
* In seguito, i gruppi di sicurezza di rete consentono o negano il traffico proveniente e diretto verso la subnet delegata ad Azure NetApp Files. 

## <a name="azure-native-environments"></a>Ambienti nativi di Azure

Il diagramma seguente illustra un ambiente nativo di Azure:

![Ambiente di rete nativo di Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Rete virtuale locale

In uno scenario di base, si crea o ci si connette a un volume di Azure NetApp Files da una macchina virtuale della stessa rete virtuale. Nella rete virtuale 2 del diagramma precedente, il volume 1 viene creato in una subnet delegata e può essere montato sulla macchina virtuale 1 della subnet predefinita.

### <a name="vnet-peering"></a>Peering reti virtuali

Se nella stessa area sono presenti reti virtuali aggiuntive che devono accedere alle risorse reciproche, è possibile metterle in connessione usando il [peering di reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) per abilitare la connettività sicura tramite l'infrastruttura di Azure. 

Si considerino la rete virtuale 2 e la rete virtuale 3 del diagramma precedente. Se la macchina virtuale 1 deve connettersi alla macchina virtuale 2 o al volume 2 oppure se la macchina virtuale 2 deve connettersi alla macchina virtuale 1 o al volume 1, è necessario abilitare il peering di reti virtuali tra la rete virtuale 2 e la rete virtuale 3. 

Si consideri inoltre uno scenario in cui è stato eseguito il peering della rete virtuale 1 con la rete virtuale 2 e della rete virtuale 2 con la rete virtuale 3 della stessa area. Le risorse della rete virtuale 1 possono connettersi alle risorse della rete virtuale 2 ma non alle risorse della rete virtuale 3, a meno che non sia stato eseguito il peering della rete virtuale 1 alla rete virtuale 3. 

Nel diagramma precedente, la macchina virtuale 3 può connettersi al volume 1, ma la macchina virtuale 4 non può connettersi al volume 2.  Questo perché non è stato eseguito il peering tra le reti virtuali spoke e il _routing di transito non è supportato tramite il peering di reti virtuali_.

## <a name="hybrid-environments"></a>Ambienti ibridi

Il diagramma seguente illustra un ambiente ibrido: 

![Ambiente di rete ibrido](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

In uno scenario ibrido, le applicazioni dei data center locali devono accedere alle risorse in Azure.  Questa esigenza si verifica, ad esempio, quando si vuole estendere il datacenter ad Azure, si vogliono usare i servizi nativi di Azure o per il ripristino di emergenza. Per informazioni su come connettere più risorse locali a risorse di Azure tramite una rete VPN da sito a sito o un circuito ExpressRoute, vedere le [opzioni di pianificazione di Gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

In una topologia di rete hub-spoke, l'hub è una rete virtuale in Azure che svolge la funzione di punto centrale di connettività alla rete locale. Gli spoke sono reti virtuali di cui viene eseguito il peering con l'hub e possono essere usati per isolare i carichi di lavoro.

A seconda della configurazione, è possibile connettere le risorse locali alle risorse disponibili nell'hub o negli spoke.

Nella topologia sopra illustrata, la rete locale è connessa a una rete virtuale hub in Azure e, nella stessa area, sono presenti due reti virtuali spoke di cui è stato eseguito il peering con la rete virtuale hub.  In questo scenario, le opzioni di connettività supportate per i volumi di Azure NetApp Files sono le seguenti:

* Le risorse locali "macchina virtuale 1" e "macchina virtuale 2" possono connettersi al volume 1 dell'hub tramite una VPN da sito a sito o un circuito ExpressRoute. 
* Le risorse locali "macchina virtuale 1" e "macchina virtuale 2" possono connettersi al volume 2 o al volume 3 tramite una VPN da sito a sito e il peering di reti virtuali a livello di area.
* La macchina virtuale 3 nella rete virtuale hub può connettersi al volume 2 nella rete virtuale spoke 1 e al volume 3 nella rete virtuale spoke 2.
* La macchina virtuale 4 della rete virtuale spoke 1 e la macchina virtuale 5 della rete virtuale spoke 2 possono connettersi al volume 1 nella rete virtuale hub.
* La macchina virtuale 4 nella rete virtuale spoke 1 non può connettersi al volume 3 nella rete virtuale spoke 2. Analogamente, la macchina virtuale 5 nella rete virtuale spoke 2 non può connettersi al volume 2 nella rete virtuale spoke 1. Questo perché non è stato eseguito il peering tra le reti virtuali spoke e il _routing di transito non è supportato tramite il peering di reti virtuali_.
* Nell'architettura precedente, se fosse presente un gateway anche nella rete virtuale spoke, verrebbe persa la connettività al volume di Azure NetApp Files dal sistema locale che si connette tramite il gateway dell'hub. Per impostazione predefinita, infatti, viene assegnata la preferenza al gateway della rete virtuale spoke e, quindi, solo i computer che si connettono tramite quel gateway possono connettersi al volume di Azure NetApp Files.

## <a name="next-steps"></a>Passaggi successivi

[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
