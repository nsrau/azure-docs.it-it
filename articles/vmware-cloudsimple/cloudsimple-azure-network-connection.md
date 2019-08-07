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
ms.openlocfilehash: a8e99da05f71cb01744111b41c301b11a0969057
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812719"
---
# <a name="azure-network-connections-overview"></a>Panoramica delle connessioni di rete di Azure

Quando si crea un servizio CloudSimple in un'area, viene:

* Crea un circuito ExpressRoute di Azure e lo collega al servizio in tale area
* Consente la connessione dalla rete dell'area CloudSimple alla rete virtuale di Azure o alla rete locale usando Azure ExpressRoute
* Fornisce servizi di accesso in esecuzione nella sottoscrizione di Azure o nella rete locale dall'ambiente cloud privato

La connessione è:

* Proteggere
* Privato
* Larghezza di banda elevata
* Bassa latenza

## <a name="benefits"></a>Vantaggi

La connessione di rete di Azure consente di:

* Usare Azure come destinazione di backup per le macchine virtuali nel cloud privato.
* Distribuire i server KMS nella sottoscrizione di Azure per crittografare l'archivio dati rete VSAN del cloud privato.
* Usare applicazioni ibride in cui il livello Web dell'applicazione viene eseguito nel cloud pubblico mentre i livelli dell'applicazione e del database vengono eseguiti nel cloud privato.

## <a name="azure-virtual-network-connection"></a>Connessione alla rete virtuale di Azure

I cloud privati possono essere connessi alle risorse di Azure usando ExpressRoute.  È possibile usare questa connessione per accedere a risorse diverse in esecuzione nella sottoscrizione di Azure dal cloud privato.  Questa connessione consente di estendere la rete cloud privata alla rete virtuale di Azure.

![Connessione Azure ExpressRoute alla rete virtuale](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Connessione ExpressRoute alla rete locale

È possibile connettere il circuito ExpressRoute di Azure esistente all'area CloudSimple. ExpressRoute Copertura globale funzionalità viene usato per connettere i due circuiti tra loro.  Viene stabilita una connessione tra i circuiti ExpressRoute locali e CloudSimple.  Questa connessione consente di estendere le reti locali alla rete cloud privata.

![Connessione ExpressRoute locale-Copertura globale](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere informazioni sul peering per la rete virtuale di Azure per la connessione a CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Connettersi da locale a CloudSimple usando ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
