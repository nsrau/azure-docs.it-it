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
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165792"
---
# <a name="cloudsimple-nodes-overview"></a>Panoramica di nodi CloudSimple

Un nodo è:

* Un computer bare metal di dedicato in cui è installato hypervisor VMware ESXi host di calcolo  
* Un'unità di calcolo è possibile eseguire il provisioning o da riservare per creare cloud privati  
* Disponibile per il provisioning o da riservare in un'area in cui è disponibile il servizio CloudSimple

I nodi sono blocchi predefiniti di un cloud privato.  Per creare un cloud privato, è necessario un minimo di tre nodi dello stesso SKU.  Per espandere un cloud privato, aggiungere altri nodi.  È possibile aggiungere nodi a un cluster esistente. In alternativa, è possibile creare un nuovo cluster, il provisioning di nodi nel portale di Azure e associandoli con il servizio CloudSimple.  Tutti i nodi il provisioning sono visibili nel servizio CloudSimple.  Dai nodi con provisioning nel portale CloudSimple è creare un cloud privato.

## <a name="provisioned-nodes"></a>Nodi con provisioning

Nodi con provisioning offrono capacità di pagamento a consumo. Il provisioning di nodi consente di ridimensionare rapidamente il cluster VMware su richiesta. È possibile aggiungere i nodi in base alle necessità, o eliminare un nodo con provisioning per ridurre i cluster VMware. nodi con provisioning sono fatturati su base mensile e addebitati alla sottoscrizione in cui si esegue il provisioning:

* Se effettua un pagamento della sottoscrizione di Azure tramite carta di credito, la scheda viene fatturata immediatamente.
* Se la fatturazione tramite fattura, gli addebiti vengono visualizzati nella fattura successiva.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Soluzione VMware dai nodi CloudSimple SKU

I tipi di nodi seguenti sono disponibili per il provisioning o prenotazione.

| SKU | CS28 - nodo | CS36 - nodo |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz, 28 core (56 Hyper-Threading) | 2x2.3 GHz, 36 core (72 Hyper-Threading) |
| RAM | 256 GB | 512 GB |
| Disco della cache |  NVMe 1,6 TB | 3.2-TB NVMe |
| Capacità disco | 5.625 TB non elaborati | 11,25 TB non elaborati |
| Tipo di archiviazione | Tutti i Flash | Tutti i Flash |

## <a name="limits"></a>Limiti

I limiti di nodo seguenti si applicano ai cloud privati.

| Resource | Limite |
|----------|-------|
| Numero minimo di nodi per creare un cloud privato | 3 |
| Numero massimo di nodi in un Cluster in un cloud privato | 16 |
| Numero massimo di nodi in un cloud privato | 64 |
| Numero minimo di nodi in un nuovo Cluster | 3 |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [il provisioning dei nodi](create-nodes.md)
* Informazioni su [del Cloud privato Microsoft](cloudsimple-private-cloud.md)