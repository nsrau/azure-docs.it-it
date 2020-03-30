---
title: Panoramica della soluzione Azure VMware by CloudSimple - Panoramica dei nodiAzure VMware Solution by CloudSimple - Nodes overview
description: Informazioni sui nodi e sui concetti di CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024926"
---
# <a name="cloudsimple-nodes-overview"></a>Panoramica dei nodi CloudSimple

I nodi sono gli elementi costitutivi di un cloud privato. Un nodo è:A node is:

* Un host di elaborazione bare metal dedicato in cui è installato un hypervisor VMware ESXi  
* Un'unità di calcolo di cui è possibile effettuare il provisioning o prenotare per creare
* Disponibile per il provisioning o la prenotazione in un'area in cui è disponibile il servizio CloudSimple

Creare un cloud privato dai nodi di cui è stato eseguito il provisioning. Per creare un cloud privato, sono necessari almeno tre nodi dello stesso SKU. Per espandere un cloud privato, aggiungere altri nodi.  È possibile aggiungere nodi a un cluster esistente o creare un nuovo cluster eseguendo il provisioning dei nodi nel portale di Azure e associandoli al servizio CloudSimple.You can add nodes to an existing cluster or create a new cluster by provisioning nodes in the Azure portal and associating them with the CloudSimple service.  Tutti i nodi di cui è stato eseguito il provisioning sono visibili nel servizio CloudSimple.All provisioned nodes are visible under the CloudSimple service.  

## <a name="provisioned-nodes"></a>Nodi di cui è stato eseguito il provisioning

I nodi di cui è stato eseguito il provisioning forniscono capacità con pagamento in base al profilo. Il provisioning dei nodi consente di scalare rapidamente il cluster VMware su richiesta. È possibile aggiungere nodi in base alle esigenze o eliminare un nodo di cui è stato eseguito il provisioning per ridurre il ridimensionamento del cluster VMware.You can add nodes as needed or delete a provisioned node to scale down your VMware cluster. I nodi di cui è stato eseguito il provisioning vengono fatturati su base mensile e addebitati alla sottoscrizione in cui ne viene eseguito il provisioning.

* Se si paga la sottoscrizione di Azure con carta di credito, la carta viene fatturata immediatamente.
* Se la fattura viene fatturata tramite fattura, gli addebiti vengono visualizzati nella fattura successiva.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>SKU dei nodi VMware Solution by CloudSimple

I seguenti tipi di nodi sono disponibili per il provisioning o la prenotazione.

| SKU           | CS28 - Nodo                 | CS36 - Nodo                 | CS36m - Nodo                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Stati Uniti orientali, Stati Uniti occidentali            | Stati Uniti orientali, Stati Uniti occidentali            | Europa occidentale                 |
| CPU           | 2x2,2 GHz, 28 Core (56 HT) | 2x2,3 GHz, 36 Core (72 HT) | 2x2,3 GHz, 36 Core (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disco cache    | NVMe da 1,6 TB                 | 3,2 TB NVMe                 | 3,2 TB NVMe                 |
| Disco di capacità | 5.625 TB Raw                | 11,25 TB Raw                | 15,36 TB Raw                |
| Tipo di archiviazione  | Tutto Flash                   | Tutto Flash                   | Tutto Flash                   |

## <a name="limits"></a>Limiti

I limiti di nodo seguenti si applicano ai cloud privati.

| Risorsa | Limite |
|----------|-------|
| Numero minimo di nodi per creare un cloud privato | 3 |
| Numero massimo di nodi in un cluster in un cloud privatoMaximum number of nodes in a cluster on a Private Cloud | 16 |
| Numero massimo di nodi in un cloud privatoMaximum number of nodes in a Private Cloud | 64 |
| Numero minimo di nodi in un nuovo cluster | 3 |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il provisioning dei [nodi](create-nodes.md)
* Scopri di più sui [cloud privati](cloudsimple-private-cloud.md)
