---
title: Soluzioni VMware (AVS)-connessioni di rete di Azure
description: Informazioni sulla connessione della rete virtuale di Azure alla rete dell'area AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025079"
---
# <a name="azure-network-connections-overview"></a>Panoramica delle connessioni di rete di Azure

Quando si crea un servizio AVS in un'area e si creano nodi, è possibile:

* Richiedere un circuito ExpressRoute di Azure e collegarlo alla rete AVS in tale area.
* Connettere la rete dell'area di AVS alla rete virtuale di Azure o alla rete locale usando Azure ExpressRoute.
* Fornire l'accesso ai servizi in esecuzione nella sottoscrizione di Azure o nella rete locale dall'ambiente cloud privato.

La connessione ExpressRoute è a larghezza di banda elevata con bassa latenza.

## <a name="benefits"></a>Vantaggi

La connessione di rete di Azure consente di:

* Usare Azure come destinazione di backup per le macchine virtuali nel cloud privato.
* Distribuire i server KMS nella sottoscrizione di Azure per crittografare l'archivio dati rete VSAN del cloud privato.
* Usare applicazioni ibride in cui il livello Web dell'applicazione viene eseguito nel cloud pubblico AVS mentre i livelli applicazione e database vengono eseguiti nel cloud privato.

## <a name="azure-virtual-network-connection"></a>Connessione alla rete virtuale di Azure

I cloud privati AVS possono essere connessi alle risorse di Azure usando ExpressRoute. La connessione ExpressRoute consente di accedere alle risorse in esecuzione nella sottoscrizione di Azure dal cloud privato AVS. Questa connessione consente di estendere la rete cloud privato AVS alla rete virtuale di Azure. Le route da una rete AVS verranno scambiate con la rete virtuale di Azure tramite BGP. Se è stato configurato il peering di rete virtuale, tutte le reti virtuali con peering saranno accessibili dalla rete AVS.

![Connessione Azure ExpressRoute alla rete virtuale](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Connessione ExpressRoute alla rete locale

È possibile connettere il circuito ExpressRoute di Azure esistente all'area AVS. ExpressRoute Copertura globale funzionalità viene usato per connettere i due circuiti tra loro. Viene stabilita una connessione tra i circuiti ExpressRoute locale e AVS. Questa connessione consente di estendere le reti locali a una rete di cloud privato AVS. Le route dalla rete AVS verranno scambiate tramite BGP con la rete locale.

![Connessione ExpressRoute locale-Copertura globale](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Connessione alla rete locale e alla rete virtuale di Azure

Le connessioni alla rete locale e alla rete virtuale di Azure possono coesistere dalla rete AVS. La connessione USA BGP per scambiare le route tra una rete locale, una rete virtuale di Azure e una rete AVS. Quando si connette la rete AVS alla rete virtuale di Azure in presenza di una connessione Copertura globale, le route di rete virtuale di Azure saranno visibili nella rete locale. Lo scambio delle route avviene in Azure tra i router perimetrali.

![Connessione ExpressRoute locale con la connessione di rete virtuale di Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Considerazioni importanti

La connessione a una rete AVS da una rete locale e/o da una rete virtuale di Azure consente lo scambio di route tra tutte le reti.

* Una rete virtuale di Azure sarà visibile sia da una rete locale che da una rete AVS.
* Se si è connessi alla rete virtuale di Azure da una rete locale, la connessione a una rete AVS usando Copertura globale consentirà l'accesso alle reti virtuali dalla rete AVS.
* Gli indirizzi delle subnet **non devono** sovrapporsi tra le reti connesse.
* AVS **non** annuncia una route predefinita per le connessioni ExpressRoute
* Se il router locale annuncia la route predefinita, il traffico proveniente dalla rete AVS e dalla rete virtuale di Azure utilizzerà la route predefinita annunciata. Di conseguenza, non è possibile accedere alle macchine virtuali in Azure usando indirizzi IP pubblici.

## <a name="next-steps"></a>Passaggi successivi

* [Connettere la rete virtuale di Azure ad AVS usando ExpressRoute](virtual-network-connection.md)
* [Connettersi da locale a AVS usando ExpressRoute](on-premises-connection.md)
