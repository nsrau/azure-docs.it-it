---
title: Panoramica di nodi per la soluzione VMware da CloudSimple - Azure
description: Informazioni sui concetti e i nodi CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b3c8fca1dd93f379860cc3b084fbb14d4a0c6380
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577361"
---
# <a name="cloudsimple-nodes-overview"></a>Panoramica di nodi CloudSimple

Un nodo è:

* Un computer bare metal di dedicato in cui è installato hypervisor VMware ESXi host di calcolo  
* Un'unità di calcolo è possibile acquistare o da riservare per creare cloud privati  
* Disponibile per l'acquisto o riservare in un'area in cui è disponibile il servizio CloudSimple

I nodi sono blocchi predefiniti di un cloud privato.  Per creare un cloud privato, è necessario un minimo di tre nodi dello stesso SKU.  Per espandere un cloud privato, aggiungere altri nodi.  È possibile aggiungere nodi a un cluster esistente. In alternativa, è possibile creare un nuovo cluster, acquisto nodi nel portale di Azure e associandoli con il servizio CloudSimple.  Tutti i nodi acquistati sono visibili nel servizio CloudSimple.  Dai nodi acquistati nel portale CloudSimple è creare un cloud privato.

## <a name="purchased-nodes"></a>Nodi acquistati

Nodi acquistati offrono capacità di pagamento a consumo. Acquisto nodi consente di ridimensionare rapidamente il cluster VMware su richiesta. È possibile aggiungere i nodi in base alle necessità, o eliminare un nodo acquistato per ridurre i cluster VMware. Acquistate i nodi sono fatturati su base mensile e addebitati alla sottoscrizione in cui sono stati acquistati:

* Se effettua un pagamento della sottoscrizione di Azure tramite carta di credito, la scheda viene fatturata immediatamente.
* Se la fatturazione tramite fattura, gli addebiti vengono visualizzati nella fattura successiva.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Soluzione VMware dai nodi CloudSimple SKU

I tipi di nodi seguenti sono disponibili per l'acquisto o prenotazione.

| SKU | CS28 - nodo | CS36 - nodo |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz, 28 core (56 Hyper-Threading) | 2x2.3 GHz, 36 core (72 Hyper-Threading) |
| RAM | 256 GB | 512 GB |
| Disco della cache |  NVMe 1,6 TB | 3.2-TB NVMe |
| Capacità disco | 5.625 TB non elaborati | 11,25 TB non elaborati |
| Tipo di archiviazione | Tutti i Flash | Tutti i Flash |

## <a name="limits"></a>Limiti

I limiti di nodo seguenti si applicano ai cloud privati.

| Risorsa | Limite |
|----------|-------|
| Numero minimo di nodi per creare un cloud privato | 3 |
| Numero massimo di nodi in un Cluster in un cloud privato | 16 |
| Numero massimo di nodi in un cloud privato | 64 |
| Numero minimo di nodi in un nuovo Cluster | 3 |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [acquisto nodi](create-nodes.md)
* Informazioni su [del Cloud privato Microsoft](cloudsimple-private-cloud.md)