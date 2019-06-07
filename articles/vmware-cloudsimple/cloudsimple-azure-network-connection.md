---
title: Soluzione VMware da CloudSimple - connessioni di rete di Azure
description: Altre informazioni sulla connessione di rete virtuale di Azure alla rete CloudSimple area
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497611"
---
# <a name="azure-network-connections-overview"></a>Panoramica delle connessioni di rete di Azure

Quando si crea un servizio CloudSimple in un'area, è:

* Crea un circuito Azure ExpressRoute e la collega al servizio in tale area
* Consente la connessione dalla rete area CloudSimple a rete virtuale di Azure o alla rete locale tramite Azure ExpressRoute
* Fornisce servizi di accesso in esecuzione nella sottoscrizione di Azure o la rete locale, dall'ambiente Cloud privato

La connessione è:

* Proteggere
* Privato
* Larghezza di banda elevata
* Bassa latenza

## <a name="benefits"></a>Vantaggi

Connessione di rete di Azure consente di:

* Usare Azure come destinazione di backup delle macchine virtuali del Cloud privato.
* Distribuire il server KMS nella sottoscrizione di Azure per crittografare l'archivio dati vSAN di Cloud privato.
* Usare le applicazioni ibride in cui il livello web dell'applicazione viene eseguita nel cloud pubblico mentre l'applicazione e i livelli di database eseguito nel Cloud privato.

## <a name="azure-virtual-network-connection"></a>Connessione di rete virtuale di Azure

Cloud privati possono essere connesse alle risorse di Azure tramite ExpressRoute.  È possibile usare la connessione per accedere alle diverse risorse in esecuzione nella sottoscrizione di Azure dal Cloud privato.  Questa connessione consente di estendere la rete del Cloud privato per la rete virtuale di Azure.

![Connessione ExpressRoute alla rete virtuale di Azure](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Connessione ExpressRoute alla rete locale

È possibile connettere il circuito ExpressRoute di Azure esistente all'area CloudSimple. Funzionalità ExpressRoute globale Reach viene usata per connettere i due circuiti tra loro.  Viene stabilita una connessione tra le origini locali e i circuiti CloudSimple ExpressRoute.  Questa connessione consente di estendere le reti locali alla rete del Cloud privato.

![Connessione ExpressRoute da sito locale - portata globale](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere informazioni relative al peering di rete virtuale di Azure alla connessione CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Connettersi da locale a CloudSimple tramite ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
