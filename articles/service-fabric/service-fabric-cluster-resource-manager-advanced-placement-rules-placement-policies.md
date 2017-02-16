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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 7fa35de8b99132ad1c10229a9bb2231f11fdbaa0


---
# <a name="placement-policies-for-service-fabric-services"></a>Criteri di posizionamento per i servizi di Service Fabric
Esistono molte diverse regole aggiuntive che potrebbe essere necessario configurare in alcuni rari scenari. Alcuni esempi di questi scenari sono:
* Se il cluster di Service Fabric è distribuito in un'area geografica, come più data center locali o in diverse aree di Azure
* Se l'ambiente si estende su più aree di controllo geopolitico (o in qualche altro caso in cui sono presenti limiti di legge o criteri importanti)
* Ci sono considerazioni effettive su prestazioni o latenza dal momento che la comunicazione nel cluster percorre lunghe distanze o transita su determinate reti più lente o meno affidabili.

In queste situazioni, può essere importante che un servizio specifico venga sempre eseguito o non venga eseguito del tutto in determinate aree. In modo analogo può essere importante tentare di inserire l'elemento primario in una determinata area per ridurre al minimo la latenza degli utenti finali.

I criteri di posizionamento avanzati sono:

1. Domini non validi
2. Domini obbligatori
3. Domini preferiti
4. Divieto di compressione della replica

La maggior parte dei comandi seguenti può essere configurata tramite le proprietà del nodo e i vincoli di posizionamento, ma alcuni sono più complessi. Per semplificare le cose, Cluster Resource Manager di Service Fabric offre questi criteri di posizionamento aggiuntivi. Proprio come gli altri vincoli di posizionamento, i criteri di posizionamento possono essere configurati in base all'istanza del servizio e aggiornati dinamicamente.

## <a name="specifying-invalid-domains"></a>Specifica di domini non validi
Il criterio di posizionamento InvalidDomain consente di specificare che un particolare dominio di errore non è valido per il carico di lavoro interessato. Questo criterio garantisce che un particolare servizio non sia mai eseguito in una determinata area, ad esempio per motivi geopolitici o criteri aziendali. È possibile specificare più domini non validi tramite criteri separati.

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
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Specifica dei domini obbligatori
Il criterio di posizionamento del dominio obbligatorio prevede che tutte le repliche con stato o le istanze del servizio senza stato per il servizio siano presenti nel dominio specificato. È possibile specificare più domini richiesti tramite criteri separati.

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
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Specifica di un dominio preferito per le repliche primarie
Il dominio primario preferito è un controllo interessante che consente di selezionare il dominio di errore in cui deve essere collocato l'elemento primario, se possibile. Quando ogni elemento è integro, l'elemento primario finisce in questo dominio. In caso di errore del dominio o della replica primaria oppure di arresto inaspettato, l'elemento primario viene migrato a un'altra posizione. Se questa nuova posizione non si trova nel dominio preferito, Cluster Resource Manager riporta l'elemento nel dominio preferito. Naturalmente questa impostazione è adatta solo ai servizi con stati. Questo criterio è particolarmente utile in cluster distribuiti tra più data center o aree di Azure, ma è preferibile collocare le repliche primarie in una determinata posizione. Mantenendo gli elementi in prossimità degli utenti si ottiene una latenza più bassa, soprattutto per le operazioni di lettura.

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
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Richiesta di distribuzione delle repliche tra tutti i domini e divieto di raggruppamento
Le repliche vengono _normalmente_ distribuite nei domini in cui il cluster è integro, ma in alcuni casi le repliche per una determinata partizione potrebbero essere temporaneamente raggruppate in un singolo dominio. Ad esempio, si supponga che il cluster abbia nove nodi in tre domini di errore (fd::/&0;, fd: /&1; e fd: /&2;) e che il servizio disponga di tre repliche. Si supponga che i nodi che sono stati usati per tali repliche in fd: /&1; e fd: /&2; siano inattivi. A questo punto, in genere Cluster Resource Manager sceglie altri nodi negli stessi domini di errore. In questo caso, si supponga che nessuno degli altri nodi in questi domini sia valido a causa di problemi di capacità. Se Cluster Resource Manager genera sostituzioni per tali repliche, deve scegliere i nodi in fd::/&0;. Tuttavia, questa _operazione_ crea una situazione in cui viene violato il vincolo del dominio di errore. Aumenta anche la possibilità che l'intero set di repliche vada perso, se FD0 dovesse essere perso in modo definitivo. Per altre informazioni sui vincoli e sulle priorità dei vincoli in generale, vedere [questo argomento](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).

Se viene visualizzato un avviso di integrità come `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` si verifica questa condizione o una condizione simile. È raro ma può capitare e in genere queste situazioni sono temporanee perché i nodi tornano attivi. Se i nodi rimangono inattivi e Cluster Resource Manager deve generare sostituzioni, in genere sono disponibili altri nodi nei domini di errore predefiniti.

Alcuni carichi di lavoro preferiscono avere sempre il numero di destinazione di repliche, anche se vengono raggruppati in un minor numero di domini. Questi carichi di lavoro puntano contro la presenza di errori di dominio permanenti totali e simultanei e in genere sono in grado di ripristinare lo stato locale. Altri carichi di lavoro preferiscono che il tempo di inattività si verifichi prima, piuttosto che rischiare di mettere a repentaglio i dati o perderli. Poiché la maggior parte dei carichi di lavoro di produzione viene eseguita con più di tre repliche, più di tre domini di errore e numerosi nodi validi per ogni dominio di errore, per impostazione predefinita non viene richiesta la distribuzione del dominio. In questo modo il normale bilanciamento del carico e il failover gestiscono questi casi, anche se ciò significa che in un dominio possono essere temporaneamente raggruppate più repliche.

Se si desidera disabilitare questo raggruppamento per un determinato carico di lavoro, è possibile specificare il criterio "RequireDomainDistribution" nel servizio. Quando questo criterio è impostato, Cluster Resource Manager garantisce che non siano mai consentite due repliche della stessa partizione nello stesso dominio di errore o di aggiornamento.

Codice:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

È possibile dunque usare queste configurazioni per i servizi in un cluster non distribuito geograficamente? Certamente. Ma non ce n'è motivo. In particolare è consigliabile evitare le configurazioni di dominio obbligatorio, non valido e preferito a meno che non venga eseguito effettivamente un cluster distribuito geograficamente. Non è opportuno provare a forzare l'esecuzione di un determinato carico di lavoro in un singolo rack né preferire un determinato segmento del cluster locale rispetto a un altro. È necessario distribuire diverse configurazioni hardware tra i domini e gestirle tramite normali vincoli di posizionamento e proprietà dei nodi.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni aggiuntive sulle altre opzioni disponibili per la configurazione dei servizi, vedere [Altre informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Jan17_HO1-->


