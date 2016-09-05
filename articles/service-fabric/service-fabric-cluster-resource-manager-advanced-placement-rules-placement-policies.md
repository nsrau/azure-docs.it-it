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
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Criteri di posizionamento per i servizi di Service Fabric
Esistono numerose e diverse regole aggiuntive di cui potrebbe essere necessario tener conto in caso di cluster di Service Fabric distribuito su una vasta area geografica, ad esempio tra più data center o aree di Azure oppure in caso di ambiente distribuito su più aree di controllo geopolitico, altre circostanze in cui sono previsti limiti legali o politici oppure distanze hanno un impatto effettivo su prestazioni e latenza. Molte di queste regole possono essere configurate tramite le proprietà dei nodi e i vincoli di posizionamento, ma alcune sono più complesse. Per semplificare il processo, forniamo questi comandi aggiuntivi. Proprio come gli altri vincoli di posizionamento, i criteri di posizionamento possono essere configurati in base all'istanza del servizio.

## Specifica di domini non validi
Il criterio di posizionamento InvalidDomain consente di specificare che un particolare dominio di errore non è valido per il carico di lavoro interessato. Questo criterio garantisce che un particolare servizio non sia mai eseguito in una determinata area, ad esempio per motivi geopolitici o criteri aziendali. È possibile specificare più domini non validi tramite criteri separati.

![Invalid Domain Example][Image1]  

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
## Specifica dei domini obbligatori
Il criterio di posizionamento del dominio obbligatorio prevede che tutte le repliche con stato o le istanze del servizio senza stato per il servizio siano presenti nel dominio specificato. È possibile specificare più domini richiesti tramite criteri separati.

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
Il dominio primario preferito è un controllo interessante che consente di selezionare il dominio di errore in cui deve essere presente il primario, se possibile. Quando tutto è integro, il primario finirà in questo dominio. In caso di anomalia del dominio o della replica primaria, oppure di arresto inaspettato, il primario verrà migrato in un'altra posizione. Se questa posizione non si trova nel dominio preferito, se possibile Cluster Resource Manager lo riporterà nel dominio preferito. Naturalmente questa impostazione è adatta solo ai servizi con stati. Questo criterio è particolarmente utile nei cluster distribuiti tra aree di Azure o più data center. In queste situazioni si stanno usando tutte le posizioni per la ridondanza ma si preferirebbe inserire le repliche primarie in una determinata posizione per ridurre la latenza delle operazioni verso il primario. Per impostazione predefinita le scritture e anche tutte le letture vengono servite dal primario.

![Preferred Primary Domains and Failover][Image3]  

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## Richiesta di distribuzione delle repliche tra tutti i domini e divieto di raggruppamento
Un altro criterio che si può specificare riguarda la richiesta di distribuzione delle repliche tra i domini di errore disponibili. Ciò si verifica per impostazione predefinita nella maggior parte dei casi in cui il cluster è integro. Tuttavia esistono casi degenerati in cui le repliche per una determinata partizione potrebbero essere temporaneamente compresse in un singolo dominio di errore o aggiornamento. Ad esempio, si supponga che anche se il cluster dispone di 9 nodi in 3 domini di errore (0, 1 e 2) e il servizio dispone di 3 repliche, i nodi che sono stati usati per tali repliche nei domini di errore 1 e 2 sono inattivi e, a causa di problemi di capacità nessuno degli altri nodi in tali domini è risultato valido. Se Service Fabric intendeva compilare le sostituzioni per tali repliche, Cluster Resource Manager dovrebbe inserirle nel dominio di errore 0, ma che crea una situazione in cui viene violato il vincolo di dominio di errore. Aumenta anche la probabilità che l'intero set di repliche potrebbe andare perso, se FD0 dovesse essere permanentemente perso. Per altre informazioni sui vincoli e in generale sulle priorità dei vincoli, vedere [questo argomento](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).

Se è stato visualizzato un avviso di sicurezza come "Il servizio di bilanciamento del carico ha rilevato una violazione di vincolo per questa replica:fabric:/<nome servizio> Partizione secondaria <ID partizione> sta violando il vincolo: dominio di errore", ci si è trovati in questa situazione o in una situazione simile. In genere queste situazioni sono temporanee, i nodi non rimangono inattivi a lungo o, se lo fanno, è necessario compilare le sostituzioni dove sono presenti altri nodi nei domini di errore corretti che sono validi, ma esistono alcuni carichi di lavoro che scambierebbero la disponibilità per il rischio di perdita di tutte le repliche. È possibile eseguire questa operazione specificando il criterio "RequireDomainDistribution", che garantisce che due repliche della stessa partizione non siano mai consentite nello stesso dominio di errore o aggiornamento.

Alcuni carichi di lavoro avranno invece il numero di repliche target (copie dello stato) in qualsiasi momento (scommettendo contro gli errori di dominio totale e sapendo che in genere è possibile ripristinare lo stato locale), mentre altri richiederebbero un tempo di inattività precedente al rischio di problemi di correttezza e perdita di dati. Poiché nella maggior parte dei casi i carichi di lavoro di produzione vengono eseguiti con più di 3 repliche, per impostazione predefinita non sarà richiesta la distribuzione di dominio consentendo a bilanciamento e failover di gestire i casi normalmente, anche se ciò comportasse il temporaneo raggruppamento di più repliche all'interno di un dominio.

Codice:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Dunque è possibile utilizzare queste configurazioni per i servizi in un cluster non distribuito geograficamente? Certamente. Ma non vi è neanche un ottimo motivo. In particolare le configurazioni di dominio necessarie, non valide e preferite devono essere evitate, a meno che l'esecuzione avvenga effettivamente in un cluster diffuso a livello geografico. Non ha senso forzare un determinato carico di lavoro per l'esecuzione in un singolo rack o preferire alcuni segmenti del cluster locale rispetto ad altri, a meno che non esistano diversi tipi di hardware o non stia avvenendo la segmentazione del carico di lavoro: questi casi possono essere gestiti tramite i vincoli normali di posizionamento.

## Passaggi successivi
- Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili per Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0824_2016-->