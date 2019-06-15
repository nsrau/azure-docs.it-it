---
title: Connettersi in remoto a un nodo di cluster di Azure Service Fabric | Microsoft Docs
description: Informazioni su come connettersi in remoto a un'istanza di set di scalabilità, ovvero a un nodo di cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62110924"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Connessione remota a un'istanza di set di scalabilità di macchine virtuali o a un nodo del cluster
In un cluster di Service Fabric in esecuzione in Azure, ogni tipo di nodo del cluster definito [configura un'istanza separata di set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md).  È possibile connettersi in remoto a istanze specifiche di set di scalabilità (nodi del cluster).  Diversamente dalle macchine virtuali a istanza singola, le istanze dei set di scalabilità non ottengono un proprio indirizzo IP virtuale. Può quindi essere difficile cercare un indirizzo IP e una porta da usare per connettersi in remoto a un'istanza specifica.

Per trovare un indirizzo IP e una porta da usare per connettersi in remoto a un'istanza specifica, completare questi passaggi.

1. Ottenere le regole NAT in ingresso per Remote Desktop Protocol (RDP).

    In genere, ogni tipo di nodo definito nel cluster ha il proprio indirizzo IP virtuale e un bilanciamento del carico dedicato. Per impostazione predefinita, il servizio di bilanciamento del carico per un tipo di nodo è denominato con il formato seguente: *Bilanciamento del carico-{nome del cluster}-{nodo-type}* , ad esempio *mycluster-LB-FrontEnd*. 
    
    Nella pagina del bilanciamento del carico nel portale di Azure selezionare **Impostazioni** > **Regole NAT in ingresso**: 

    ![Regole NAT in ingresso di bilanciamento del carico](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Lo screenshot seguente mostra le regole NAT in ingresso per un tipo di nodo denominato FrontEnd: 

    ![Regole NAT in ingresso di bilanciamento del carico](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Per ogni nodo, vengono indicati l'indirizzo IP nella colonna **IP DESTINAZIONE**, l'istanza del set di scalabilità nella colonna **DESTINAZIONE** e il numero di porta nella colonna **SERVIZIO**. Per la connessione remota, le porte vengono allocate a ciascun nodo in ordine crescente a partire dalla porta 3389.

    È anche possibile trovare le regole NAT in ingresso nella sezione `Microsoft.Network/loadBalancers` del modello di Resource Manager per il cluster.
    
2. Per verificare il mapping tra la porta in ingresso e la porta di destinazione per un nodo, è possibile fare clic sulla relativa regola e osservare il valore **Porta di destinazione**. Lo screenshot seguente mostra la regola NAT in ingresso per il nodo **FrontEnd (Instance 1)** nel passaggio precedente. Si noti che, anche se il numero di porta (in ingresso) è 3390, la porta di destinazione è mappata alla porta 3389, ovvero la porta per il servizio RDP nella destinazione.  

    ![Mapping delle porte di destinazione](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Per impostazione predefinita, per i cluster di Windows la porta di destinazione è la porta 3389, che è mappata al servizio RDP nel nodo di destinazione. Per i cluster Linux, la porta di destinazione è la porta 22, che è mappata al servizio SSH (Secure Shell).

3. Connettersi in remoto allo specifico nodo (istanza del set di scalabilità). È possibile usare il nome utente e la password impostati durante la creazione del cluster o eventuali altre credenziali configurate. 

    Lo screenshot seguente mostra l'uso di Connessione Desktop remoto per la connessione al nodo **FrontEnd (Instance 1)** in un cluster Windows:
    
    ![Connessione Desktop remoto](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Nei nodi Linux è possibile connettersi con SSH (nell'esempio seguente vengono riutilizzati lo stesso indirizzo IP e la stessa porta per brevità):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Per i passaggi successivi, leggere gli articoli seguenti:
* Vedere la [panoramica della funzionalità "Distribuzione in qualsiasi ambiente" e un confronto con i cluster gestiti da Azure](service-fabric-deploy-anywhere.md).
* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* [Aggiornare i valori dell'intervallo di porte RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nelle macchine virtuali del cluster dopo la distribuzione
* [Cambiare il nome utente e la password dell'amministratore](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) per le macchine virtuali del cluster

