---
title: Panoramica della soluzione VMware di Azure di CloudSimple-nodes
description: Informazioni sui nodi e concetti relativi a CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 58af69b401400c7b2f663a91de8bf38bc9a296a3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877694"
---
# <a name="cloudsimple-nodes-overview"></a>Panoramica sui nodi CloudSimple

I nodi sono i blocchi predefiniti di un cloud privato. Un nodo è:

* Un host di calcolo bare metal dedicato in cui è installato un hypervisor VMware ESXi  
* Unità di calcolo che è possibile acquistare o riservare per creare cloud privati
* Disponibile per l'acquisto o la riserva in un'area in cui è disponibile il servizio CloudSimple

Si crea un cloud privato dai nodi acquistati. Per creare un cloud privato, sono necessari almeno tre nodi dello stesso SKU. Per espandere un cloud privato, aggiungere altri nodi.  È possibile aggiungere nodi a un cluster esistente o creare un nuovo cluster acquistando nodi nel portale di Azure e associando i nodi al servizio CloudSimple.  Tutti i nodi acquistati sono visibili nel servizio CloudSimple.  

## <a name="purchased-nodes"></a>Nodi acquistati

I nodi acquistati forniscono capacità con pagamento in base al consumo. I nodi di acquisto consentono di ridimensionare rapidamente il cluster VMware su richiesta. È possibile aggiungere nodi in base alle esigenze o eliminare un nodo acquistato per ridurre il cluster VMware. I nodi acquistati vengono fatturati su base mensile e addebitati alla sottoscrizione in cui sono stati acquistati.

* Se si paga per la sottoscrizione di Azure tramite carta di credito, la carta verrà fatturata immediatamente.
* Se la fatturazione è fatturata, i costi vengono visualizzati nella fattura successiva.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>SKU della soluzione VMware per nodi CloudSimple

I tipi di nodi seguenti sono disponibili per l'acquisto o la prenotazione.

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

* Informazioni su come [acquistare nodi](create-nodes.md)
* Informazioni sui [cloud privati](cloudsimple-private-cloud.md)
