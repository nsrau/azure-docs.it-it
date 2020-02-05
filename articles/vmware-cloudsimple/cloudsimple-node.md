---
title: Azure VMware Solutions (AVS)-Panoramica sui nodi
description: Informazioni sui nodi e i concetti di AVS.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024926"
---
# <a name="avs-nodes-overview"></a>Panoramica sui nodi AVS

I nodi sono i blocchi predefiniti di un cloud privato AVS. Un nodo è:

* Un host di calcolo bare metal dedicato in cui è installato un hypervisor VMware ESXi  
* Unità di calcolo che è possibile acquistare o riservare per creare cloud privati AVS
* Disponibile per l'acquisto o la riserva in un'area in cui è disponibile il servizio AVS

È possibile creare un cloud privato AVS dai nodi acquistati. Per creare un cloud privato AVS, sono necessari almeno tre nodi dello stesso SKU. Per espandere un cloud privato AVS, aggiungere altri nodi. È possibile aggiungere nodi a un cluster esistente o creare un nuovo cluster acquistando nodi nel portale di Azure e associando i nodi al servizio AVS. Tutti i nodi acquistati sono visibili nel servizio AVS. 

## <a name="provisioned-nodes"></a>Nodi sottoposti a provisioning

I nodi di cui è stato effettuato il provisioning forniscono capacità con pagamento in base al consumo. I nodi di provisioning consentono di ridimensionare rapidamente il cluster VMware su richiesta. È possibile aggiungere nodi in base alle esigenze o eliminare un nodo di cui è stato effettuato il provisioning per ridurre il cluster VMware. I nodi di cui è stato effettuato il provisioning vengono fatturati su base mensile e addebitati alla sottoscrizione in cui viene effettuato il provisioning.

* Se si paga per la sottoscrizione di Azure tramite carta di credito, la carta verrà fatturata immediatamente.
* Se la fatturazione è fatturata, i costi vengono visualizzati nella fattura successiva.

## <a name="vmware-solution-by-avs-nodes-sku"></a>SKU della soluzione VMware per i nodi AVS

I tipi di nodi seguenti sono disponibili per il provisioning o la prenotazione.

| SKU           | CS28-nodo                 | CS36-nodo                 | CS36m-nodo                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Area        | Stati Uniti orientali, Stati Uniti occidentali            | Stati Uniti orientali, Stati Uniti occidentali            | Europa occidentale                 |
| CPU           | 2x 2.2 GHz, 28 Core (56 HT) | 2x 2,3 GHz, 36 core (72 HT) | 2x 2,3 GHz, 36 core (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disco della cache    | 1,6-TB NVMe                 | 3,2-TB NVMe                 | 3,2-TB NVMe                 |
| Disco di capacità | 5,625 TB raw                | 11,25 TB raw                | 15,36 TB raw                |
| Tipo di archiviazione  | Tutti i flash                   | Tutti i flash                   | Tutti i flash                   |

## <a name="limits"></a>Limiti

I limiti dei nodi seguenti si applicano ai cloud privati AVS.

| Gruppi | Limite |
|----------|-------|
| Numero minimo di nodi per la creazione di un cloud privato AVS | 3 |
| Numero massimo di nodi in un cluster in un cloud privato AVS | 16 |
| Numero massimo di nodi in un cloud privato AVS | 64 |
| Numero minimo di nodi in un nuovo cluster | 3 |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [acquistare nodi](create-nodes.md)
* Informazioni sui [cloud privati AVS](cloudsimple-private-cloud.md)
* Informazioni su come eseguire il [provisioning di nodi](create-nodes.md)
* Informazioni sui [cloud privati](cloudsimple-private-cloud.md)
