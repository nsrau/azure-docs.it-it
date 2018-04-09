---
title: Connettersi in remoto a un nodo di cluster di Azure Service Fabric | Microsoft Docs
description: Informazioni su come connettersi in remoto a un'istanza di set di scalabilità, ovvero a un nodo di cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 8c7d5446429089a0fc931175b55e81e1ad0c97a0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Connettersi in remoto a un'istanza di set di scalabilità di macchine virtuali o a un nodo di cluster
In un cluster di Service Fabric in esecuzione in Azure, ogni tipo di nodo del cluster definito [configura un'istanza separata di set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md).  È possibile connettersi in remoto a istanze specifiche di set di scalabilità (o nodi del cluster).  Diversamente dalle macchine virtuali a istanza singola, le istanze dei set di scalabilità non ottengono un proprio indirizzo IP virtuale. Può quindi essere difficile cercare un indirizzo IP e una porta da usare per connettersi in remoto a un'istanza specifica.

Per trovare un indirizzo IP e una porta da usare per connettersi in remoto a un'istanza specifica, completare questi passaggi.

1. Trovare l'indirizzo IP virtuale per il tipo di nodo ottenendo le regole NAT in ingresso per Remote Desktop Protocol (RDP).

    Innanzitutto, ottenere i valori delle regole NAT in ingresso definiti nell'ambito della definizione di risorse per `Microsoft.Network/loadBalancers`.
    
    Nella pagina del bilanciamento del carico nel portale di Azure selezionare **Impostazioni** > **Regole NAT in ingresso**. Si ottengono così l'indirizzo IP e la porta che è possibile usare per connettersi in remoto alla prima istanza del set di scalabilità. 
    
    ![Bilanciamento del carico][LBBlade]
    
    Nella figura seguente l'indirizzo IP e la porta sono **104.42.106.156** e **3389**.
    
    ![Regole NAT][NATRules]

2. Trovare la porta che è possibile usare per connettersi in remoto all'istanza del set di scalabilità o al nodo specifico.

    Mapping dei set di scalabilità ai nodi. Usare le informazioni del set di scalabilità per determinare la porta esatta da usare.
    
    Le porte vengono allocate in un ordine crescente che corrisponde all'istanza del set di scalabilità. Nell'esempio precedente del tipo di nodo FrontEnd, le porte per ognuna delle cinque istanze del nodo sono elencate nella tabella seguente. Applicare lo stesso mapping all'istanza del set di scalabilità.
    
    | **Istanza del set di scalabilità della macchina virtuale** | **Porta** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. Connettersi in remoto all'istanza specifica del set di scalabilità.

    Nella figura seguente viene usata Connessione Desktop remoto per connettersi all'istanza del set di scalabilità FrontEnd_1:
    
    ![Connessione Desktop remoto][RDP]


Per i passaggi successivi, leggere gli articoli seguenti:
* Vedere la [panoramica della funzionalità "Distribuzione in qualsiasi ambiente" e un confronto con i cluster gestiti da Azure](service-fabric-deploy-anywhere.md).
* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* [Aggiornare i valori dell'intervallo di porte RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nelle macchine virtuali del cluster dopo la distribuzione
* [Cambiare il nome utente e la password dell'amministratore](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) per le macchine virtuali del cluster

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
