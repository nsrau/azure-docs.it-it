---
title: Rimuovere un tipo di nodo in Azure Service Fabric | Microsoft Docs
description: Informazioni su come rimuovere un tipo di nodo da un cluster di Service Fabric in esecuzione in Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969412"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Come rimuovere un tipo di nodo Service FabricHow to remove a Service Fabric node type
Questo articolo descrive come ridimensionare un cluster di Azure Service Fabric rimuovendo un tipo di nodo esistente da un cluster. Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una macchina virtuale che fa parte di un cluster viene detto nodo. I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Azure viene [configurato come set di scalabilità di macchine virtuali separato](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può essere gestito separatamente. Dopo aver creato un cluster di Service Fabric, è possibile ridimensionare un cluster orizzontalmente rimuovendo un tipo di nodo (set di scalabilità di macchine virtuali) e tutti i relativi nodi.  È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.  Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

> [!WARNING]
> L'utilizzo di questo approccio per rimuovere un tipo di nodo da un cluster di produzione non è consigliabile per l'utilizzo frequente. È un comando molto pericoloso perché elimina la risorsa set di scalabilità di macchine virtuali presente dietro il tipo di nodo. 

## <a name="durability-characteristics"></a>Caratteristiche di durabilità
La sicurezza ha la priorità sulla velocità quando si usa Remove-AzServiceFabricNodeType.Safety is prioritized over speed when using Remove-AzServiceFabricNodeType. Il tipo di nodo deve avere un [livello di durabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Silver o Gold, perché:
- Bronze non offre alcuna garanzia in merito al salvataggio delle informazioni sullo stato.
- I livelli di durabilità Silver e Gold consentono di intercettare qualsiasi modifica al set di scalabilità.
- Il livello Gold offre anche il controllo degli aggiornamenti di Azure nell'ambito del set di scalabilità.

Service Fabric "orchestra" le modifiche e gli aggiornamenti sottostanti in modo che i dati non vadano persi. Tuttavia, quando si rimuove un tipo di nodo con durabilità Bronze, è possibile perdere informazioni sullo stato. Se si rimuove un tipo di nodo primario e l'applicazione è senza stato, Bronze è accettabile. Se si eseguono carichi di lavoro con stato in fase di produzione, la configurazione minima deve essere Silver. Analogamente, per gli scenari di produzione, il tipo di nodo primario deve essere sempre Silver o Gold.

### <a name="more-about-bronze-durability"></a>Altre informazioni sul livello di durabilità Bronze

Quando si rimuove un tipo di nodo Bronze, tutti i nodi appartenenti a quel tipo di nodo vengono immediatamente disattivati. Service Fabric non riesce a intercettare eventuali aggiornamenti al set di scalabilità dei nodi Bronze e tutte le macchine virtuali vengono immediatamente disattivate. Se nei nodi fossero presenti elementi con stato, i dati andrebbero persi. Poiché tutti i nodi di Service Fabric fanno parte dell'anello, anche se fossero presenti solo elementi senza stato, andrebbero comunque persi i dati di tutti i nodi vicini, con una destabilizzazione del cluster stesso.

## <a name="remove-a-node-type"></a>Rimuovere un tipo di nodo

1. Si prega di prendere cura di questi prerequisiti prima di iniziare il processo.

    - Il cluster è integro.
    - La capacità sarà ancora sufficiente dopo la rimozione del tipo di nodo, ad esempio numero di nodi in cui inserire il numero di repliche richiesto.

2. Spostare tutti i servizi con vincoli di posizionamento per utilizzare il tipo di nodo all'esterno del tipo di nodo.

    - Modificare L'applicazione /Manifesto del servizio per non fare più riferimento al tipo di nodo.
    - Distribuire la modifica.

    Quindi convalidare che:
    - Tutti i servizi modificati in precedenza non sono più in esecuzione sul nodo appartenente al tipo di nodo.
    - Tutti i servizi sono sani.

3. Deselezionare il tipo di nodo come non primario (Ignora per i tipi di nodo non primari)Unmark the node-type as non-primary (Skip for non-primary node types)

    - Individuare il modello di Azure Resource Manager usato per la distribuzione.
    - Individuare la sezione correlata al tipo di nodo nella sezione Service Fabric.
    - Modificare la proprietà isPrimary su false. Non rimuovere la sezione relativa al tipo di nodo in questa attività.
    - Distribuire il modello di Azure Resource Manager modificato. A seconda della configurazione del cluster, questo passaggio potrebbe richiedere un po' di tempo.
    
    Quindi convalidare che:
    - La sezione Service Fabric nel portale indica che il cluster è pronto.
    - Cluster è integro.
    - Nessuno dei nodi appartenenti al tipo di nodo è contrassegnato come Seed Node.

4. Disabilitare i dati per il tipo di nodo.

    Connettersi al cluster tramite PowerShell e quindi eseguire il passaggio seguente.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - Per la durata del bronzo, attendere che tutti i nodi scliscino allo stato disabilitato
    - Per la durata dell'argento e dell'oro, alcuni nodi andranno in disabilitato e il resto sarà in stato di disabilitazione. Controllare la scheda dei dettagli dei nodi nello stato di disabilitazione, se sono tutti bloccati a garantire il quorum per le partizioni del servizio di infrastruttura, quindi è sicuro continuare.

5. Interrompere i dati per il tipo di nodo.

    Connettersi al cluster tramite PowerShell e quindi eseguire il passaggio seguente.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Attendere che tutti i nodi per il tipo di nodo siano contrassegnati come premuti.
    
6. Rimuovere i dati per il tipo di nodo.

    Connettersi al cluster tramite PowerShell e quindi eseguire il passaggio seguente.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Attendere che tutti i nodi vengano rimossi dal cluster. I nodi non devono essere visualizzati in SFX.

7. Rimuovere il tipo di nodo dalla sezione Service Fabric.Remove node type from Service Fabric section.

    - Individuare il modello di Azure Resource Manager usato per la distribuzione.
    - Individuare la sezione correlata al tipo di nodo nella sezione Service Fabric.
    - Rimuovere la sezione corrispondente al tipo di nodo.
    - Solo per i cluster Silver e di durabilità superiore, aggiornare la risorsa cluster nel `applicationDeltaHealthPolicies` modello e `properties` configurare i criteri di integrità per ignorare l'integrità:/Integrità dell'applicazione di sistema aggiungendo in base alla risorsa cluster come indicato di seguito. I criteri seguenti devono ignorare gli errori esistenti ma non consentire nuovi errori di integrità. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Distribuire il modello di Azure Resource Manager modificato. Questo passaggio richiederà un po' di tempo, di solito fino a due ore. Questo aggiornamento modificherà le impostazioni per il InfrastructureService, pertanto è necessario un riavvio del nodo. In questo `forceRestart` caso viene ignorato. 
    Il `upgradeReplicaSetCheckTimeout` parametro specifica il tempo massimo di attesa di una partizione in uno stato sicuro, se non è già in uno stato sicuro. Una volta superati i controlli di sicurezza per tutte le partizioni in un nodo, Service Fabric procede con l'aggiornamento su tale nodo.
    Il valore per `upgradeTimeout` il parametro può essere ridotto a 6 ore, ma per la massima sicurezza 12 ore devono essere utilizzate.

    Quindi convalidare che:
    - La risorsa dell'infrastruttura del servizio nel portale mostra ready.

8. Rimuovere tutti i riferimenti alle risorse relative al tipo di nodo.

    - Individuare il modello di Azure Resource Manager usato per la distribuzione.
    - Rimuovere il set di scalabilità della macchina virtuale e altre risorse correlate al tipo di nodo dal modello.
    - Distribuire le modifiche.

    Quindi:
    - Attendere il completamento della distribuzione.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [caratteristiche di durabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) dei cluster.
- Altre informazioni sui [tipi di nodo e i set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md).
- Altre informazioni sul [ridimensionamento di un cluster di Service Fabric](service-fabric-cluster-scaling.md).
