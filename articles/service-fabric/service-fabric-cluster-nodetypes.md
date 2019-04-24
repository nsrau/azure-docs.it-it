---
title: Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali | Microsoft Docs
description: Informazioni su come i tipi di nodo di Azure Service Fabric sono correlati ai set di scalabilità di macchine virtuali e su come connettersi in remoto a un'istanza del set di scalabilità o a un nodo del cluster.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 7f9397ee21f74fe6a776881940e5721264216b0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386126"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali
I [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets) sono una risorsa di calcolo di Azure. I set di scalabilità possono essere usati per distribuire e gestire una raccolta di macchine virtuali come un set. Ogni tipo di nodo definito in un cluster di Azure Service Fabric configura un set di scalabilità separato.  Il runtime di Service Fabric viene installato in ogni macchina virtuale del set di scalabilità. È possibile aumentare o ridurre in modo indipendente ogni nodo, cambiare lo SKU del sistema operativo in esecuzione in ogni nodo del cluster, avere diversi set di porte aperte e usare metriche per la capacità diverse.

La figura seguente mostra un cluster con due tipi di nodo denominati FrontEnd e BackEnd. Ogni tipo di nodo ha cinque nodi.

![Un cluster con due tipi di nodo][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Eseguire il mapping delle istanze dei set di scalabilità di macchine virtuali ai nodi
Come illustrato nella figura precedente, le istanze dei set di scalabilità iniziano con l'istanza 0 per poi aumentare di 1. I nomi dei nodi corrispondono alla numerazione. Ad esempio, il nodo BackEnd_0 è l'istanza 0 del set di scalabilità BackEnd. Questo particolare set di scalabilità ha cinque istanze, denominate BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando si aumenta un set di scalabilità, viene creata una nuova istanza. Il nome della nuova istanza del set di scalabilità sarà in genere il nome del set di scalabilità + il successivo numero di istanza. Nell'esempio sarà BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Eseguire il mapping dei servizi di bilanciamento del carico dei set di scalabilità a tipi di nodo e set di scalabilità
Se è stato distribuito il cluster dal portale Azure o è stato usato il modello di Azure Resource Manager di esempio, si otterrà un elenco di tutte le risorse in un gruppo di risorse. Verranno visualizzati i bilanciamenti del carico per ogni set di scalabilità o tipo di nodo. Il nome del servizio di bilanciamento del carico Usa il formato seguente: **-LB&lt;nome del tipo di nodo&gt;**. Ad esempio, LB-sfcluster4doc-0, come in questa figura:

![Risorse][Resources]


## <a name="next-steps"></a>Passaggi successivi
* Vedere la [panoramica della funzionalità "Distribuzione in qualsiasi ambiente" e un confronto con i cluster gestiti da Azure](service-fabric-deploy-anywhere.md).
* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* [Connettersi in remoto](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) a un'istanza specifica del set di scalabilità
* [Aggiornare i valori dell'intervallo di porte RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nelle macchine virtuali del cluster dopo la distribuzione
* [Cambiare il nome utente e la password dell'amministratore](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) per le macchine virtuali del cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
