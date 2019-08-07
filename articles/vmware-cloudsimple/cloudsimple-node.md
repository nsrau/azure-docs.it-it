---
title: Panoramica dei nodi per la soluzione VMware di CloudSimple-Azure
description: Informazioni sui nodi e concetti relativi a CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 346bd046810ebae5142bc23400419857000d0c8e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812584"
---
# <a name="cloudsimple-nodes-overview"></a>Panoramica sui nodi CloudSimple

Un nodo è:

* Un host di calcolo bare metal dedicato in cui è installato VMware ESXi hypervisor  
* Unità di calcolo di cui è possibile effettuare il provisioning o la riserva per la creazione di cloud privati  
* Disponibile per il provisioning o la riserva in un'area in cui è disponibile il servizio CloudSimple

I nodi sono blocchi predefiniti di un cloud privato.  Per creare un cloud privato, sono necessari almeno tre nodi dello stesso SKU.  Per espandere un cloud privato, aggiungere altri nodi.  È possibile aggiungere nodi a un cluster esistente. In alternativa, è possibile creare un nuovo cluster eseguendo il provisioning di nodi nel portale di Azure e associando i nodi al servizio CloudSimple.  Tutti i nodi di cui è stato effettuato il provisioning sono visibili nel servizio CloudSimple.  Si crea un cloud privato dai nodi sottoposti a provisioning nel portale di CloudSimple.

## <a name="provisioned-nodes"></a>Nodi sottoposti a provisioning

I nodi di cui è stato effettuato il provisioning forniscono capacità con pagamento in base al consumo. I nodi di provisioning consentono di ridimensionare rapidamente il cluster VMware su richiesta. È possibile aggiungere nodi in base alle esigenze o eliminare un nodo di cui è stato effettuato il provisioning per ridurre il cluster VMware. i nodi di cui è stato effettuato il provisioning vengono fatturati su base mensile e addebitati alla sottoscrizione in cui viene effettuato il provisioning:

* Se si paga per la sottoscrizione di Azure tramite carta di credito, la carta verrà fatturata immediatamente.
* Se la fatturazione è fatturata, i costi vengono visualizzati nella fattura successiva.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>SKU della soluzione VMware per nodi CloudSimple

I nodi dei tipi seguenti sono disponibili per il provisioning o la prenotazione.

| SKU | CS28-nodo | CS36-nodo |
|-----|-------------|-------------|
| CPU | 2x 2.2 GHz, 28 Core (56 HT) | 2x 2,3 GHz, 36 core (72 HT) |
| RAM | 256 GB | 512 GB |
| Disco della cache |  1,6-TB NVMe | 3,2-TB NVMe |
| Disco di capacità | 5,625 TB raw | 11,25 TB raw |
| Tipo di archiviazione | Tutti i flash | Tutti i flash |

## <a name="limits"></a>Limiti

I limiti dei nodi seguenti si applicano ai cloud privati.

| Risorsa | Limite |
|----------|-------|
| Numero minimo di nodi per la creazione di un cloud privato | 3 |
| Numero massimo di nodi in un cluster in un cloud privato | 16 |
| Numero massimo di nodi in un cloud privato | 64 |
| Numero minimo di nodi in un nuovo cluster | 3 |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il provisioning di [nodi](create-nodes.md)
* Informazioni sul [cloud privato](cloudsimple-private-cloud.md)