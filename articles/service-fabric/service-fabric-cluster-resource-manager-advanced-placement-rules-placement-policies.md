<properties
   pageTitle="Cluster Resource Manager di Service Fabric - Criteri di posizionamento | Microsoft Azure"
   description="Panoramica su altre politiche e regole per i servizi di Service Fabric"
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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Criteri di posizionamento per i servizi di Service Fabric
Esistono numerose e diverse regole aggiuntive di cui potrebbe essere necessario tener conto in caso di cluster di Service Fabric distribuito su una vasta area geografica, ad esempio tra più centri dati o aree di Azure oppure in caso di ambiente distribuito su più aree di controllo geopolitico (o altre circostanze in cui sono previsti limiti legali o politici). Molte di queste potrebbero essere configurate tramite le proprietà dei nodi e i vincoli di posizionamento (descritti in precedenza), ma alcune sono più complesse. In ogni caso, sono previste queste scelte rapide. Proprio come i vincoli di posizionamento, i criteri di posizionamento possono essere configurati in base al servizio.

## Specifica di domini non validi
Il criterio di posizionamento InvalidDomain consente di specificare che un particolare dominio di errore non è valido per il carico di lavoro interessato. Questo criterio garantisce che un particolare servizio non sia mai eseguito in una determinata area, ad esempio per motivi geopolitici o criteri aziendali. È possibile specificare più domini non validi

![Invalid Domain Example][Image1]

Codice:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```
## Specifica dei domini obbligatori
Il criterio di posizionamento del dominio obbligatorio prevede che tutte le repliche siano presenti nel dominio specificato. È possibile specificare più domini obbligatori.

![Required Domain Example][Image2]

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

## Specifica di un dominio preferito per le repliche primarie
Il dominio primario preferito è un controllo interessante che consente di selezionare il dominio di errore in cui deve essere presente il primario, se possibile. Quando tutto è integro, il primario finirà in questo dominio. In caso di anomalia del dominio o della replica primaria, oppure di arresto inaspettato, il primario verrà migrato in un'altra posizione. Quando possibile, tornerà nel dominio preferito. Naturalmente questa impostazione è adatta solo ai servizi con stati. Questo criterio è particolarmente utile nei cluster distribuiti geograficamente in cui si usano altre posizioni per la ridondanza ma si preferirebbe inserire le repliche primarie in una determinata posizione per ridurre la latenza delle operazioni verso il primario. Per impostazione predefinita le scritture e anche tutte le letture vengono servite dal primario.

![Preferred Primary Domains and Failover][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

## Richiesta di distribuzione delle repliche tra tutti i domini e divieto di raggruppamento
Un altro criterio che si può specificare riguarda la richiesta di distribuzione delle repliche tra i domini disponibili. Si noti che nell'esempio precedente è possibile che più repliche (di fatto un quorum) siano raggruppate all'interno dello stesso centro dati. In questo caso non dovrebbero esserci problemi dal momento che il primario non si trova nello stesso data center della maggior parte delle repliche. Tuttavia, questo scenario sottolinea come sia possibile essere temporaneamente soggetti a una configurazione che si vorrebbe evitare. Ciò dipenderà da altre condizioni del cluster presenti al momento, ma è comunque possibile evitare completamente tale circostanza garantendo che le repliche si trovino sempre in domini separati. Tenere presente che, selezionando quest'opzione, si verificheranno tempi di inattività ed errori invece di consentire al servizio di continuare a funzionare nei nodi rimanenti. Il criterio RequireDomainDistribution configura questo comportamento, ma ciò potrebbe significare che, in caso di possibile raggruppamento, le repliche non sarebbero affatto posizionate. Di conseguenza il numero di repliche durante l'esecuzione sarà temporaneamente inferiore alla soglia target fino al ritorno di nodi in un dominio idoneo. Alcuni clienti preferiscono mantenere il numero di repliche target (copie dello stato) in ogni momento, mentre altri non vogliono correre rischi legati ai problemi di disponibilità. Se le repliche vengono raggruppate in un dominio di errore sul quale si verifica un'anomalia, verranno tutte perse contemporaneamente. Per un numero limitato di repliche ciò potrebbe determinare la perdita di quorum. Nell'improbabile evento che il dominio non venga ripristinato, ciò potrebbe causare la perdita di dati. Poiché nella maggior parte dei casi si tratta di esecuzioni con più di 3 repliche, per impostazione predefinita non sarà richiesta la distribuzione di dominio consentendo a bilanciamento e failover di gestire i casi normalmente, anche se ciò comportasse il temporaneo raggruppamento di più repliche all'interno di un dominio.

Codice:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Dunque è possibile utilizzare queste configurazioni per i servizi in un cluster non distribuito geograficamente? Certamente. Ma non ce n’è motivo. In particolare è consigliabile evitare le configurazioni di dominio richiesto, non valido e preferito a meno che non si utilizzi effettivamente un cluster distribuito geograficamente.

## Passaggi successivi
- Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili di Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0316_2016-->