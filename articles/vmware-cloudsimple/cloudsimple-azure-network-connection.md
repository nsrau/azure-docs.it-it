---
title: Soluzione VMware di CloudSimple-connessioni di rete di Azure
description: Informazioni sulla connessione della rete virtuale di Azure alla rete dell'area CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90e3121c3f036d1abc8ca372ee349aef3485d07b
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69625010"
---
# <a name="azure-network-connections-overview"></a>Panoramica delle connessioni di rete di Azure

Quando si crea un servizio CloudSimple in un'area e si creano nodi, è possibile:

* Richiedere un circuito ExpressRoute di Azure e collegarlo alla rete CloudSimple in tale area.
* Connettere la rete CloudSimple Region alla rete virtuale di Azure o alla rete locale usando Azure ExpressRoute.
* Fornire l'accesso ai servizi in esecuzione nella sottoscrizione di Azure o nella rete locale dall'ambiente cloud privato.

La connessione ExpressRoute è a larghezza di banda elevata con bassa latenza.

## <a name="benefits"></a>Vantaggi

La connessione di rete di Azure consente di:

* Usare Azure come destinazione di backup per le macchine virtuali nel cloud privato.
* Distribuire i server KMS nella sottoscrizione di Azure per crittografare l'archivio dati rete VSAN del cloud privato.
* Usare applicazioni ibride in cui il livello Web dell'applicazione viene eseguito nel cloud pubblico mentre i livelli dell'applicazione e del database vengono eseguiti nel cloud privato.

## <a name="azure-virtual-network-connection"></a>Connessione alla rete virtuale di Azure

I cloud privati possono essere connessi alle risorse di Azure usando ExpressRoute.  La connessione ExpressRoute consente di accedere alle risorse in esecuzione nella sottoscrizione di Azure dal cloud privato.  Questa connessione consente di estendere la rete cloud privata alla rete virtuale di Azure.  Le route dalla rete CloudSimple verranno scambiate con la rete virtuale di Azure tramite BGP.  Se è stato configurato il peering di rete virtuale, tutte le reti virtuali con peering saranno accessibili dalla rete CloudSimple.

![Connessione Azure ExpressRoute alla rete virtuale](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Connessione ExpressRoute alla rete locale

È possibile connettere il circuito ExpressRoute di Azure esistente all'area CloudSimple. ExpressRoute Copertura globale funzionalità viene usato per connettere i due circuiti tra loro.  Viene stabilita una connessione tra i circuiti ExpressRoute locali e CloudSimple.  Questa connessione consente di estendere le reti locali alla rete cloud privata. Le route dalla rete CloudSimple verranno scambiate tramite BGP con la rete locale.

![Connessione ExpressRoute locale-Copertura globale](media/cloudsimple-global-reach-connection.png)


## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Connessione alla rete locale e alla rete virtuale di Azure

Le connessioni alla rete locale e alla rete virtuale di Azure possono coesistere dalla rete CloudSimple.  La connessione USA BGP per scambiare le route tra la rete locale, la rete virtuale di Azure e la rete CloudSimple.  Quando si connette la rete CloudSimple alla rete virtuale di Azure in presenza di Copertura globale connessione, le route di rete virtuale di Azure saranno visibili nella rete locale.  Lo scambio delle route avviene in Azure tra i router perimetrali.

![Connessione ExpressRoute locale con la connessione di rete virtuale di Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Considerazioni importanti

La connessione alla rete CloudSimple dalla rete locale e dalla rete virtuale di Azure consente lo scambio di route tra tutte le reti.

* Rete virtuale di Azure sarà visibile sia dalla rete locale che dalla rete CloudSimple.
* Se si è connessi alla rete virtuale di Azure dalla rete locale, la connessione alla rete CloudSimple usando Copertura globale consentirà l'accesso alle reti virtuali dalla rete CloudSimple.
* Gli indirizzi delle subnet **non devono** sovrapporsi tra le reti connesse.
* CloudSimple **non** annuncia la route predefinita per le connessioni ExpressRoute
* Se il router locale annuncia la route predefinita, il traffico dalla rete CloudSimple e dalla rete virtuale di Azure utilizzerà la route predefinita annunciata.  Di conseguenza, non è possibile accedere alle macchine virtuali in Azure usando indirizzi IP pubblici.

## <a name="next-steps"></a>Passaggi successivi

* [Connettere la rete virtuale di Azure a CloudSimple usando ExpressRoute](virtual-network-connection.md)
* [Connettersi da locale a CloudSimple usando ExpressRoute](on-premises-connection.md)
