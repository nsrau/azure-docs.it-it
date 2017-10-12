---
title: Cluster Resource Manager di Service Fabric - Criteri di posizionamento | Microsoft Docs
description: Panoramica su altre politiche e regole per i servizi di Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: c240643d2a7ce98ddd7f7871eeef654cced953f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="placement-policies-for-service-fabric-services"></a>Criteri di posizionamento per i servizi di Service Fabric
I criteri di posizionamento sono le regole aggiuntive che possono essere usate per definire la posizione del servizio in alcuni scenari specifici, meno comuni. Alcuni esempi di questi scenari sono:

- Il cluster di Service Fabric si estende nelle distanze geografiche, come più data center locali o in diverse aree di Azure
- L'ambiente si estende su più aree di controllo geopolitico o giuridico o in qualche altro caso in cui sono presenti limiti normativi che è necessario applicare
- Vi sono considerazioni sulle prestazioni o sulla latenza di comunicazione dovute a distanze di grandi dimensioni o all'uso di collegamenti di rete lenta o meno affidabili
- È necessario mantenere determinati carichi di lavoro collocati come un tentativo migliore, con altri carichi di lavoro o in prossimità dei clienti

La maggior parte di questi requisiti si allineano con il layout fisico del cluster, rappresentati come domini di errore del cluster. 

I criteri di posizionamento avanzati che consentono di indirizzare questi scenari sono:

1. Domini non validi
2. Domini obbligatori
3. Domini preferiti
4. Divieto di compressione della replica

La maggior parte dei comandi seguenti può essere configurata tramite le proprietà del nodo e i vincoli di posizionamento, ma alcuni sono più complessi. Per semplificare le cose, Cluster Resource Manager di Service Fabric offre questi criteri di posizionamento aggiuntivi. I criteri di posizionamento sono configurati in una base all'istanza singola del servizio. Possono anche essere aggiornati in modo dinamico.

## <a name="specifying-invalid-domains"></a>Specifica di domini non validi
Il criterio di posizionamento **InvalidDomain** consente di specificare che un particolare dominio di errore non è valido per un servizio specifico. Questo criterio garantisce che un particolare servizio non sia mai eseguito in una determinata area, ad esempio per motivi geopolitici o criteri aziendali. È possibile specificare più domini non validi tramite criteri separati.

<center>
![Esempio di dominio non valido][Image1]
</center>

Codice:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Specifica dei domini obbligatori
I criteri di selezione del dominio richiesto richiedono che il servizio sia presente solo nel dominio specificato. È possibile specificare più domini richiesti tramite criteri separati.

<center>
![Esempio di dominio obbligatorio][Image2]
</center>

Codice:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Specifica di un dominio preferito per le repliche primarie di un servizio con stato
Il dominio primario preferito specifica il dominio di errore per posizionare il server primario. Quando ogni elemento è integro, l'elemento primario finisce in questo dominio. Se il dominio o la replica primaria non riescono o vengono chiusi, il database primario si sposta in un'altra posizione, idealmente nello stesso dominio. Se questa nuova posizione non si trova nel dominio preferito, Cluster Resource Manager riporta l'elemento nel dominio preferito. Naturalmente questa impostazione è adatta solo ai servizi con stati. Questo criterio è particolarmente utile in cluster distribuiti tra più data center o aree di Azure, ma hanno dei data center che preferiscono la collocazione in una determinata posizione. Mantenendo i Primari vicini agli utenti o ad altri servizi si permette di garantire una latenza più bassa, in particolare per le letture, che sono gestite dai Primari, per impostazione predefinita.

<center>
![Failover e domini primari preferiti][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Necessità di distribuzione della replica e divieto di compressione
Le repliche sono _normalmente_ distribuite in domini di errore e di aggiornamento quando il cluster è integro. Tuttavia, vi sono casi in cui più di una replica per una determinata partizione rischia di essere temporaneamente compressa in un singolo dominio. Ad esempio, si supponga che il cluster abbia nove nodi in tre domini di errore (fd::/ 0, fd: / 1 e fd: / 2). Si supponga anche che il servizio disponga di tre repliche. Si supponga che i nodi che sono stati usati per tali repliche in fd: / 1 e fd: / 2 siano inattivi. In genere Cluster Resource Manager sceglie altri nodi negli stessi domini di errore. In questo caso, si supponga che nessuno degli altri nodi in questi domini sia valido a causa di problemi di capacità. Se Cluster Resource Manager genera sostituzioni per tali repliche, deve scegliere i nodi in fd::/ 0. Tuttavia, questa _operazione_ crea una situazione in cui viene violato il vincolo del dominio di errore. La compressione delle repliche aumenta anche la possibilità che l'intero set di repliche vada perso. 

> [!NOTE]
> Per altre informazioni sui vincoli e sulle priorità dei vincoli in generale, vedere [questo argomento](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Se viene visualizzato un messaggio di integrità come "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", allora si verifica questa condizione o una condizione simile. In genere solo una o due repliche vengono compresse insieme temporaneamente. Finché sono presenti meno di un quorum di repliche in un dominio specifico, si è sicuri. La compressione è rara, ma può capitare e in genere queste situazioni sono temporanee perché i nodi tornano attivi. Se i nodi rimangono inattivi e Cluster Resource Manager deve generare sostituzioni, in genere sono disponibili altri nodi nei domini di errore predefiniti.

Alcuni carichi di lavoro preferiscono avere sempre il numero di destinazione di repliche, anche se vengono raggruppati in un minor numero di domini. Questi carichi di lavoro puntano contro la presenza di errori di dominio permanenti totali e simultanei e in genere sono in grado di ripristinare lo stato locale. Altri carichi di lavoro preferiscono che il tempo di inattività si verifichi prima, piuttosto che rischiare di mettere a repentaglio i dati o perderli. La maggior parte dei carichi di lavoro di produzione viene eseguita con più di tre repliche, più di tre domini di errore e numerosi nodi validi per ogni dominio di errore. Per questo motivo, il comportamento predefinito consente una compressione del dominio per impostazione predefinita. Il comportamento predefinito consente un bilanciamento e un failover normali per gestire questi casi estremi, anche se ciò comporta una compressione del dominio temporanea.

Se si desidera disabilitare questo raggruppamento per un determinato carico di lavoro, è possibile specificare il criterio `RequireDomainDistribution` nel servizio. Quando questo criterio è impostato, Cluster Resource Manager garantisce che due repliche della stessa partizione non siano in esecuzione nello stesso dominio di errore o di aggiornamento.

Codice:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

È possibile dunque usare queste configurazioni per i servizi in un cluster non distribuito geograficamente? È possibile, ma non ce n'è motivo. A meno che non li richiedano gli scenari, è opportuno evitare le configurazioni di dominio richiesto, non valido e preferito. Non è opportuno provare a forzare l'esecuzione di un determinato carico di lavoro in un singolo rack né preferire un determinato segmento del cluster locale rispetto a un altro. È necessario distribuire diverse configurazioni hardware tra i domini di errore e gestirle tramite normali vincoli di posizionamento e proprietà dei nodi.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulla configurazione dei servizi, [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
