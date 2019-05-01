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
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577001"
---
# <a name="azure-network-connection-overview"></a>Panoramica sulla connessione di rete di Azure

Quando si crea un servizio CloudSimple in un'area, è:

* Crea un circuito ExpressRoute e la collega al servizio in tale area
* Si connette alla rete di area CloudSimple a rete virtuale di Azure o alla rete locale tramite Azure ExpressRoute
* Fornisce servizi di accesso in esecuzione nella sottoscrizione di Azure o la rete locale, dall'ambiente cloud privato

Questa connessione è la larghezza di banda elevata con bassa latenza.

## <a name="benefits"></a>Vantaggi

Connessione di rete di Azure consente di:

* Usare Azure come destinazione di backup delle macchine virtuali del Cloud privato.
* Distribuire il server KMS nella sottoscrizione di Azure per crittografare l'archivio dati vSAN di Cloud privato.
* Usare le applicazioni ibride in cui il livello web dell'applicazione viene eseguita nel cloud pubblico mentre l'applicazione e i livelli di database eseguito nel Cloud privato.

## <a name="expressroute-connection-to-on-premises-network"></a>Connessione ExpressRoute alla rete locale

È possibile connettere il circuito ExpressRoute di Azure esistente all'area CloudSimple. Funzionalità ExpressRoute globale Reach viene usata per connettere i due circuiti tra loro.  Viene stabilita una connessione tra origini locali e i circuiti CloudSimple ExpressRoute.

Questo metodo consente di stabilire una connessione tra i due ambienti che è:

* Proteggere
* Privato
* Larghezza di banda elevata
* Bassa latenza

Per creare una connessione ExpressRoute per una rete locale [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

* [Impostazione connessione di rete virtuale](https://docs.azure.cloudsimple.com/virtual-network-connection)