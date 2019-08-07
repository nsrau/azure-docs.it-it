---
title: Soluzione VMware di Azure per CloudSimple-quota del nodo CloudSimple
description: Descrive i limiti di quota per i nodi CloudSimple e come richiedere un aumento della quota
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e765d7c87f2f941a5e2d558b71c4e5a71d2df9b1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816682"
---
# <a name="cloudsimple-node-quota-limits"></a>Limiti della quota del nodo CloudSimple

Quattro nodi è la quantità predefinita disponibile per il provisioning, quando la sottoscrizione è abilitata per il servizio CloudSimple.  È possibile effettuare il provisioning di qualsiasi [tipo di nodo](cloudsimple-node.md) da portale di Azure.  Per creare un cloud privato, sono necessari almeno tre nodi dello stesso SKU.  Se è stato effettuato il provisioning dei nodi, è possibile che venga visualizzato un errore quando si tenta di eseguire il provisioning di nodi aggiuntivi.

## <a name="quota-increase"></a>Aumento della quota

È possibile aumentare la quota del nodo inviando una richiesta di supporto. Il team operativo del servizio valuterà la richiesta e collaborerà con l'utente per aumentare la quota del nodo.  Quando si apre un nuovo ticket, selezionare le opzioni seguenti:

* Tipo di problema: **Tecnico**
* Sottoscrizione: **ID sottoscrizione**
* Tipo di servizio: **Soluzione VMware per CloudSimple**
* Tipo di problema: **Quota di nodi dedicati**
* Sottotipo di problema: **Aumentare la quota di nodi dedicati**
* Soggetto: **Aumento della quota**

Nei dettagli del ticket di supporto specificare il numero di nodi e lo SKU del nodo necessari.

È anche possibile contattare il rappresentante del account Microsoft [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) in per aumentare la quota del nodo nella sottoscrizione.  È necessario fornire:

* ID sottoscrizione
* SKU del nodo
* Numero di nodi aggiuntivi per i quali si richiede l'aumento della quota

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il provisioning di nodi](create-nodes.md)
* [Panoramica sui nodi CloudSimple](cloudsimple-node.md)