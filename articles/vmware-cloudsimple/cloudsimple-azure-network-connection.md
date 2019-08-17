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
ms.openlocfilehash: eca3e316d866814f6727dd8ef2c3fa490a551383
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563156"
---
# <a name="azure-network-connections-overview"></a>Panoramica delle connessioni di rete di Azure

Quando si crea un servizio CloudSimple in un'area, viene:

* Crea un circuito ExpressRoute di Azure e lo collega al servizio in tale area.
* Connette la rete dell'area CloudSimple alla rete virtuale di Azure o alla rete locale usando Azure ExpressRoute.
* Fornisce l'accesso ai servizi in esecuzione nella sottoscrizione di Azure o nella rete locale dall'ambiente cloud privato.

La connessione ExpressRoute è a larghezza di banda elevata con bassa latenza.

## <a name="benefits"></a>Vantaggi

La connessione di rete di Azure consente di:

* Usare Azure come destinazione di backup per le macchine virtuali nel cloud privato.
* Distribuire i server KMS nella sottoscrizione di Azure per crittografare l'archivio dati rete VSAN del cloud privato.
* Usare applicazioni ibride in cui il livello Web dell'applicazione viene eseguito nel cloud pubblico mentre i livelli dell'applicazione e del database vengono eseguiti nel cloud privato.

## <a name="azure-virtual-network-connection"></a>Connessione alla rete virtuale di Azure

I cloud privati possono essere connessi alle risorse di Azure usando ExpressRoute.  La connessione ExpressRoute consente di accedere alle risorse in esecuzione nella sottoscrizione di Azure dal cloud privato.  Questa connessione consente di estendere la rete cloud privata alla rete virtuale di Azure.

[![Connessione Azure ExpressRoute alla rete virtuale](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Connessione ExpressRoute alla rete locale

È possibile connettere il circuito ExpressRoute di Azure esistente all'area CloudSimple. ExpressRoute Copertura globale funzionalità viene usato per connettere i due circuiti tra loro.  Viene stabilita una connessione tra i circuiti ExpressRoute locali e CloudSimple.  Questa connessione consente di estendere le reti locali alla rete cloud privata.

![Connessione ExpressRoute locale-Copertura globale](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Passaggi successivi

* [Connettere la rete virtuale di Azure a CloudSimple usando ExpressRoute](virtual-network-connection.md)
* [Connettersi da locale a CloudSimple usando ExpressRoute](on-premises-connection.md)
