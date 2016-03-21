<properties
   pageTitle="Service Fabric Cluster Resource Manager - Placement Policies"
   description="Overview of additional placement policies and rules for Service Fabric Services"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Criteri di posizionamento
Esistono numerose e diverse regole aggiuntive di cui potrebbe essere necessario tener conto in caso di cluster di Service Fabric distribuito su una vasta area geografica, ad esempio tra più centri dati o aree di Azure oppure in caso di ambiente distribuito su più aree di controllo geopolitico (o altre circostanze in cui sono previsti limiti legali o politici). Molte di queste potrebbero essere configurate tramite le proprietà dei nodi e i vincoli di posizionamento (descritti in precedenza), ma alcune sono più complesse. In ogni caso, sono previste queste scelte rapide, proprio come i vincoli di posizionamento, i criteri di posizionamento possono essere configurati in base al servizio

-	InvalidDomain: consente di specificare che un particolare dominio di errore non è valido per il carico di lavoro interessato. Ideale per garantire che un particolare servizio non sia mai eseguito in una determinata area, ad esempio per motivi geopolitici o criteri aziendali.

![Invalid Domain Example][Image1]

Codice:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```

-	RequiredDomain: piuttosto semplice, richiede che tutte le repliche siano presenti nel dominio specificato.

![Required Domain Example][Image2]

Codice:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

-	PreferPrimaryDomain: questo interessante controllo consente di selezionare il dominio di errore in cui deve essere presente il primario, se possibile. Quando tutto è integro, il primario finirà in questo dominio. In caso di anomalia del dominio o della replica primaria, oppure di arresto inaspettato, il primario verrà migrato in un'altra posizione. Quando possibile, tornerà nel dominio preferito. Naturalmente questa impostazione è adatta solo ai servizi con stati.

![Preferred Primary Domains and Failover][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

-	RequireDomainDistribution: questa ulteriore opzione può essere utilizzata per impedire determinate situazioni. Si noti che nell'esempio precedente è possibile che più repliche (di fatto un quorum) siano raggruppate all'interno dello stesso centro dati. In questo caso non dovrebbero esserci problemi dal momento che il primario non si trova nello stesso centro dati della maggior parte delle repliche. Tuttavia, questo scenario sottolinea come sia possibile essere temporaneamente soggetti a una configurazione che si vorrebbe evitare. Ciò dipenderà da altre condizioni del cluster presenti al momento, ma è comunque possibile evitare completamente tale circostanza garantendo che le repliche si trovino sempre in domini separati. A tale scopo è possibile utilizzare il flag RequireDomainDistribution, ma ciò potrebbe significare che, in caso di possibile raggruppamento, le repliche non sarebbero affatto posizionate. Di conseguenza il numero di repliche durante l'esecuzione sarà temporaneamente inferiore alla soglia target fino al ritorno di nodi in un dominio idoneo. Alcuni clienti preferiscono mantenere il numero di repliche target (copie dello stato) in ogni momento, mentre altri non vogliono correre rischi legati ai problemi di disponibilità. Se le repliche vengono raggruppate in un dominio di errore sul quale si verifica un'anomalia, verranno tutte perse contemporaneamente. Per un numero limitato di repliche ciò potrebbe determinare la perdita di quorum. Poiché nella maggior parte dei casi si tratta di esecuzioni con più di 3 repliche, per impostazione predefinita non sarà richiesta la distribuzione di dominio consentendo a bilanciamento e failover di gestire i casi normalmente, anche se ciò comportasse il temporaneo raggruppamento di più repliche all’interno di un dominio.

Codice:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

È possibile configurare più domini non validi o richiesti in un singolo servizio, tuttavia le altre due opzioni (domini preferiti e distribuzione di dominio) sono configurabili solo una volta per ogni servizio.

Dunque è possibile utilizzare queste configurazioni per i servizi in un cluster non distribuito geograficamente? Certamente. Ma non ce n’è motivo. In particolare è consigliabile evitare le configurazioni di dominio richiesto, non valido e preferito a meno che non si utilizzi effettivamente un cluster distribuito geograficamente.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)



[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0309_2016-->