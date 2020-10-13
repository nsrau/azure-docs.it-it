---
title: Rimuovere un tipo di nodo in Azure Service Fabric | Microsoft Docs
description: Informazioni su come rimuovere un tipo di nodo da un cluster di Service Fabric in esecuzione in Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163578"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Come rimuovere un tipo di nodo Service Fabric
Questo articolo descrive come ridimensionare un cluster di Azure Service Fabric rimuovendo un tipo di nodo esistente da un cluster. Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una macchina virtuale che fa parte di un cluster viene detto nodo. I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Azure viene [configurato come set di scalabilità di macchine virtuali separato](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può essere gestito separatamente. Dopo aver creato un cluster di Service Fabric, è possibile ridimensionare un cluster orizzontalmente rimuovendo un tipo di nodo (set di scalabilità di macchine virtuali) e tutti i relativi nodi.  È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.  Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

> [!WARNING]
> L'uso di questo approccio per rimuovere un tipo di nodo da un cluster di produzione non è consigliato per l'uso frequente. È un comando molto pericoloso perché elimina la risorsa set di scalabilità di macchine virtuali presente dietro il tipo di nodo. 

## <a name="durability-characteristics"></a>Caratteristiche di durabilità
Quando si usa Remove-AzServiceFabricNodeType, viene assegnata la priorità alla sicurezza. Il tipo di nodo deve avere un [livello di durabilità](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Silver o Gold, perché:
- Bronze non offre alcuna garanzia in merito al salvataggio delle informazioni sullo stato.
- I livelli di durabilità Silver e Gold consentono di intercettare qualsiasi modifica al set di scalabilità.
- Il livello Gold offre anche il controllo degli aggiornamenti di Azure nell'ambito del set di scalabilità.

Service Fabric "orchestra" le modifiche e gli aggiornamenti sottostanti in modo che i dati non vadano persi. Tuttavia, quando si rimuove un tipo di nodo con durabilità Bronze, è possibile che si perdano le informazioni sullo stato. Se si sta rimuovendo un tipo di nodo primario e l'applicazione è senza stato, il bronzo è accettabile. Se si eseguono carichi di lavoro con stato in fase di produzione, la configurazione minima deve essere Silver. Analogamente, per gli scenari di produzione, il tipo di nodo primario deve essere sempre Silver o Gold.

### <a name="more-about-bronze-durability"></a>Altre informazioni sul livello di durabilità Bronze

Quando si rimuove un tipo di nodo Bronze, tutti i nodi appartenenti a quel tipo di nodo vengono immediatamente disattivati. Service Fabric non riesce a intercettare eventuali aggiornamenti al set di scalabilità dei nodi Bronze e tutte le macchine virtuali vengono immediatamente disattivate. Se nei nodi fossero presenti elementi con stato, i dati andrebbero persi. Poiché tutti i nodi di Service Fabric fanno parte dell'anello, anche se fossero presenti solo elementi senza stato, andrebbero comunque persi i dati di tutti i nodi vicini, con una destabilizzazione del cluster stesso.

## <a name="remove-a-node-type"></a>Rimuovere un tipo di nodo

1. Prima di iniziare il processo, prestare attenzione a questi prerequisiti.

    - Il cluster è integro.
    - Sarà ancora disponibile una capacità sufficiente dopo la rimozione del tipo di nodo, ad esempio. numero di nodi per inserire il numero di repliche necessarie.

2. Spostare tutti i servizi con vincoli di posizionamento per usare il tipo di nodo al di fuori del tipo di nodo.

    - Modificare il manifesto dell'applicazione o del servizio in modo che non faccia più riferimento al tipo di nodo.
    - Distribuire la modifica.

    Verificare quindi quanto segue:
    - Tutti i servizi modificati sopra non sono più in esecuzione nel nodo che appartiene al tipo di nodo.
    - Tutti i servizi sono integri.

3. Deseleziona il tipo di nodo come non primario (Ignora per i tipi di nodo non primari)

    - Individuare il modello di Azure Resource Manager usato per la distribuzione.
    - Trovare la sezione relativa al tipo di nodo nella sezione Service Fabric.
    - Modificare la proprietà noprimary in false. * * Non rimuovere la sezione correlata al tipo di nodo in questa attività.
    - Distribuire il modello di Azure Resource Manager modificato. * * A seconda della configurazione del cluster, questo passaggio può richiedere alcuni minuti.
    
    Verificare quindi quanto segue:
    - Service Fabric sezione nel portale indica che il cluster è pronto.
    - Il cluster è integro.
    - Nessuno dei nodi appartenenti al tipo di nodo è contrassegnato come nodo di inizializzazione.

4. Disabilitare ogni nodo nel tipo di nodo.

    Connettersi al cluster usando PowerShell, quindi eseguire il passaggio seguente.
    
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

    - Per la durabilità Bronze, attendere che tutti i nodi ottengano lo stato disabilitato
    - Per la durabilità Silver e Gold, alcuni nodi entrano in disabilitati e il resto si troverà nello stato disabilitato. Controllare la scheda Details (Dettagli) dei nodi nello stato disabilitato. se sono tutti bloccati per garantire il quorum per le partizioni del servizio di infrastruttura, è possibile continuare.

5. Arrestare i dati per il tipo di nodo.

    Connettersi al cluster usando PowerShell, quindi eseguire il passaggio seguente.
    
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
    
    Attendere fino a quando tutti i nodi per il tipo di nodo sono contrassegnati come inattivi.

6. Deallocare i nodi nel set di scalabilità di macchine virtuali originale
    
    Accedere alla sottoscrizione di Azure in cui è stato distribuito il set di scalabilità e rimuovere il set di scalabilità di macchine virtuali. 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. Rimuovere i dati per il tipo di nodo.

    Connettersi al cluster usando PowerShell, quindi eseguire il passaggio seguente.
    
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

    Attendere finché tutti i nodi non vengono rimossi dal cluster. I nodi non devono essere visualizzati in SFX.

8. Rimuovere il tipo di nodo dalla sezione Service Fabric.

    - Individuare il modello di Azure Resource Manager usato per la distribuzione.
    - Trovare la sezione relativa al tipo di nodo nella sezione Service Fabric.
    - Rimuovere la sezione corrispondente al tipo di nodo.
    - Solo per i cluster di durabilità Silver e versioni successive, aggiornare la risorsa cluster nel modello e configurare i criteri di integrità per ignorare l'integrità dell'applicazione Fabric:/System aggiungendo `applicationDeltaHealthPolicies` in risorsa cluster `properties` come indicato di seguito. Il criterio seguente deve ignorare gli errori esistenti ma non consentire nuovi errori di integrità. 
 
 
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

    - Distribuire il modello di Azure Resource Manager modificato. * * Questa operazione richiederà un po' di tempo, in genere fino a due ore. Con questo aggiornamento le impostazioni vengono modificate in InfrastructureService, pertanto è necessario riavviare il nodo. In questo caso `forceRestart` viene ignorato. 
    Il parametro `upgradeReplicaSetCheckTimeout` specifica il tempo massimo di attesa Service Fabric prima che una partizione sia in uno stato sicuro, se non è già in uno stato sicuro. Una volta superati i controlli di sicurezza per tutte le partizioni in un nodo, Service Fabric procede con l'aggiornamento su tale nodo.
    Il valore per il parametro `upgradeTimeout` può essere ridotto a 6 ore, ma è consigliabile usare per la massima sicurezza 12 ore.

    Verificare quindi quanto segue:
    - Service Fabric risorsa nel portale Visualizza pronto.

9. Rimuovere tutti i riferimenti alle risorse relative al tipo di nodo dal modello ARM.

    - Individuare il modello di Azure Resource Manager usato per la distribuzione.
    - Rimuovere il set di scalabilità di macchine virtuali e altre risorse correlate al tipo di nodo dal modello.
    - Distribuire le modifiche.

    Quindi:
    - Attendere il completamento della distribuzione.
    
10. Rimuovere le risorse correlate al tipo di nodo che non sono più in uso. Esempio Load Balancer e IP pubblico. 

    - Per rimuovere queste risorse è possibile usare lo stesso comando di PowerShell usato nel passaggio 6 specificando il tipo di risorsa specifico e la versione dell'API. 

> [!Note]
> Questo passaggio è facoltativo se lo stesso Load Balancer e IP viene riutilizzato tra i tipi di nodo.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [caratteristiche di durabilità](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) dei cluster.
- Altre informazioni sui [tipi di nodo e i set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md).
- Altre informazioni sul [ridimensionamento di un cluster di Service Fabric](service-fabric-cluster-scaling.md).
