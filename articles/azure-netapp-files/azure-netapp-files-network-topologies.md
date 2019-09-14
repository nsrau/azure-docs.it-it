---
title: Linee guida per la pianificazione della rete Azure NetApp Files | Microsoft Docs
description: Vengono descritte le linee guida che consentono di progettare un'architettura di rete efficace utilizzando Azure NetApp Files.
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
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 02852b325a22f274b4aa6e793b03c733c38bb9aa
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984129"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Linee guida per la pianificazione della rete per Azure NetApp Files

La pianificazione dell'architettura di rete è un elemento fondamentale della progettazione di un'infrastruttura di applicazioni. Questo articolo consente di progettare un'architettura di rete efficace per i carichi di lavoro per trarre vantaggio dalle funzionalità avanzate di Azure NetApp Files.

Azure NetApp Files volumi sono progettati per essere contenuti in una subnet per scopi specifici denominata [subnet delegata](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) all'interno della rete virtuale di Azure. È quindi possibile accedere ai volumi direttamente dalla VNet, da reti virtuali con peering nella stessa area o da locale su un gateway di rete virtuale (ExpressRoute o gateway VPN) in base alle esigenze. La subnet è dedicata alla Azure NetApp Files e non esiste connettività ad altri servizi di Azure o a Internet.

## <a name="considerations"></a>Considerazioni  

È necessario comprendere alcune considerazioni quando si pianifica la Azure NetApp Files rete.

### <a name="constraints"></a>Vincoli

Le funzionalità seguenti non sono attualmente supportate per Azure NetApp Files: 

* Gruppi di sicurezza di rete (gruppi) applicati alla subnet delegata
* Route definite dall'utente (UDR) con prefisso dell'indirizzo come subnet di file di Azure NetApp
* Criteri di Azure (ad esempio, criteri di denominazione personalizzati) nell'interfaccia Azure NetApp Files
* Bilanciamento del carico per il traffico Azure NetApp Files

Per Azure NetApp Files vengono applicate le restrizioni di rete seguenti:

* Il numero di indirizzi IP in uso in un VNet con Azure NetApp Files (incluso reti virtuali con peering) non può essere maggiore di 1000. Stiamo lavorando per aumentare questo limite per soddisfare le esigenze di scalabilità dei clienti. Nel frattempo, se è necessario un numero maggiore di indirizzi IP, contattare il team di supporto con il caso d'uso e il limite richiesto.
* In ogni rete virtuale di Azure (VNet), è possibile delegare solo una subnet in Azure NetApp Files.


### <a name="supported-network-topologies"></a>Topologie di rete supportate

Nella tabella seguente vengono descritte le topologie di rete supportate da Azure NetApp Files.  Vengono inoltre descritte le soluzioni alternative per le topologie non supportate. 

|    Topologie    |    È supportato    |     Soluzione alternativa    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Connettività al volume in un VNet locale    |    Sì    |         |
|    Connettività al volume in un VNet con peering (stessa area)    |    Sì    |         |
|    Connettività al volume in una VNet con peering (tra aree o peering globale)    |    No    |    Nessuna    |
|    Connettività a un volume sul gateway ExpressRoute    |    Yes    |         |
|    Connettività da locale a volume in una VNet spoke tramite gateway ExpressRoute e peering VNet con transito gateway    |    Yes    |        |
|    Connettività da locale a volume in una VNet spoke tramite gateway VPN    |    Sì    |         |
|    Connettività da locale a volume in una VNet spoke sul gateway VPN e il peering VNet con transito gateway    |    Sì    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Rete virtuale per volumi Azure NetApp Files

In questa sezione vengono illustrati i concetti che consentono di pianificare la rete virtuale.

### <a name="azure-virtual-networks"></a>Reti virtuali di Azure

Prima di eseguire il provisioning di un volume di Azure NetApp Files, è necessario creare una rete virtuale di Azure (VNet) o utilizzarne una già esistente nella sottoscrizione. VNet definisce il limite di rete del volume.  Per ulteriori informazioni sulla creazione di reti virtuali, vedere la [documentazione relativa alla rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Subnet

Le subnet segmentano la rete virtuale in spazi di indirizzi distinti che possono essere usati dalle risorse di Azure.  Azure NetApp Files volumi sono contenuti in una subnet per scopi specifici denominata [subnet delegata](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

La delega della subnet fornisce le autorizzazioni esplicite al servizio Azure NetApp Files per creare risorse specifiche del servizio nella subnet.  Usa un identificatore univoco per la distribuzione del servizio. In questo caso, viene creata un'interfaccia di rete per abilitare la connettività ai Azure NetApp Files.

Se si usa un nuovo VNet, è possibile creare una subnet e delegare la subnet a Azure NetApp Files seguendo le istruzioni in [delegare una subnet al Azure NetApp files](azure-netapp-files-delegate-subnet.md). È anche possibile delegare una subnet vuota esistente che non è già stata delegata ad altri servizi.

Se il VNet viene sottoposto a peering con un'altra VNet, non è possibile espandere lo spazio di indirizzi del VNet. Per questo motivo, è necessario creare la nuova subnet delegata nello spazio di indirizzi della VNet. Se è necessario estendere lo spazio di indirizzi, è necessario eliminare il peering di VNet prima di espandere lo spazio degli indirizzi.

### <a name="udrs-and-nsgs"></a>UdR e gruppi

Le route definite dall'utente (UDR) e i gruppi di sicurezza di rete (gruppi) non sono supportati nelle subnet delegate per Azure NetApp Files.

Come soluzione alternativa, è possibile applicare gruppi ad altre subnet che consentono o negano il traffico da e verso la Azure NetApp Files subnet delegata.  

## <a name="azure-native-environments"></a>Ambienti nativi di Azure

Il diagramma seguente illustra un ambiente nativo di Azure:

![Ambiente di rete nativo di Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>VNet locale

Uno scenario di base consiste nel creare o connettersi a un volume Azure NetApp Files da una macchina virtuale (VM) nella stessa VNet. Per VNet 2 nel diagramma precedente, il volume 1 viene creato in una subnet delegata e può essere montato sulla macchina virtuale 1 nella subnet predefinita.

### <a name="vnet-peering"></a>Peering di rete virtuale

Se si dispone di reti virtuali aggiuntive nella stessa area che necessitano dell'accesso alle risorse dell'altro, reti virtuali può essere connesso usando il [peering VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) per abilitare la connettività sicura tramite l'infrastruttura di Azure. 

Prendere in considerazione VNet 2 e VNet 3 nel diagramma precedente. Se la VM 2 deve connettersi alla macchina virtuale 3 o al volume 2 oppure se la macchina virtuale 3 deve connettersi alla VM 2 o al volume 1, è necessario abilitare il peering VNet tra VNet 2 e VNet 3. 

Si consideri inoltre uno scenario in cui VNet 1 è associato a VNet 2 e VNet 2 è peered con VNet 3 nella stessa area. Le risorse di VNet 1 possono connettersi alle risorse in VNet 2 ma non possono connettersi alle risorse in VNet 3, a meno che non sia stato specificato il peering di VNet 1 e VNet 3. 

Nel diagramma precedente, anche se la macchina virtuale 3 è in grado di connettersi al volume 1, la VM 4 non è in grado di connettersi al volume 2.  Questo è il motivo per cui non è stato effettuato il peering del reti virtuali spoke e il _routing di transito non è supportato dal peering VNet_.

## <a name="hybrid-environments"></a>Ambienti ibridi

Il diagramma seguente illustra un ambiente ibrido: 

![Ambiente di rete ibrido](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

Nello scenario ibrido, le applicazioni dei data center locali devono accedere alle risorse in Azure.  Questo è il caso se si vuole estendere il Data Center ad Azure o si vuole usare i servizi nativi di Azure o per il ripristino di emergenza. Per informazioni su come connettere più risorse locali alle risorse in Azure tramite una VPN da sito a sito o ExpressRoute, vedere [Opzioni di pianificazione del gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) .

In una topologia hub-spoke ibrida, l'hub VNet in Azure funge da punto di connessione centrale alla rete locale. I spoke sono reti virtuali con peering con l'hub e possono essere usati per isolare i carichi di lavoro.

A seconda della configurazione, è possibile connettere le risorse locali alle risorse nell'hub e negli spoke.

Nella topologia illustrata in precedenza la rete locale è connessa a un hub VNet in Azure e sono presenti 2 reti virtuali spoke nella stessa area con peering con l'hub VNet.  In questo scenario, le opzioni di connettività supportate per i volumi Azure NetApp Files sono le seguenti:

* Le risorse locali VM 1 e VM 2 possono connettersi al volume 1 nell'hub tramite una VPN da sito a sito o un circuito ExpressRoute. 
* Le risorse locali VM 1 e VM 2 possono connettersi al volume 2 o al volume 3 tramite una VPN da sito a sito e il peering VNET a livello di area.
* La macchina virtuale 3 nell'hub VNet può connettersi al volume 2 in spoke VNet 1 e volume 3 in spoke VNet 2.
* La VM 4 da spoke VNet 1 e VM 5 da spoke VNet 2 possono connettersi al volume 1 nell'hub VNet.

La VM 4 in spoke VNet 1 non è in grado di connettersi al volume 3 in spoke VNet 2. Inoltre, la macchina virtuale 5 in spoke VNet2 non è in grado di connettersi al volume 2 in spoke VNet 1. Questa situazione si verifica perché non è stato effettuato il peering del reti virtuali spoke e il _routing di transito non è supportato sul peering VNet_.

## <a name="next-steps"></a>Passaggi successivi

[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
