<properties
   pageTitle="Introducing the Service Fabric Cluster Resource Manager"
   description="An introduction to the Service Fabric Cluster Resource Manager."
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


# Limitazioni
In alcuni casi, anche se resource manager è stato configurato correttamente, il cluster può subire interruzioni (numerose anomalie di nodi o domini di errore mentre sono in corso gli upgrade, durante l'applicazione degli aggiornamenti e così via). Resource Manager tenta di correggere tutti gli elementi nel modo migliore, ma in periodi come questo è consigliabile valutare l'adozione di una protezione che consenta al cluster stesso di stabilizzarsi (rientro dei nodi, ripristino automatico dell'integrità delle condizioni di rete, distribuzione di bit corretti). Per risolvere questi tipi di situazioni, Resource Manager include diverse limitazioni. Si noti che queste sono considerate "grattacapi" e, in genere, non devono essere utilizzate senza che venga calcolata accuratamente la quantità di lavoro parallelo che può essere eseguito effettivamente nel cluster e la frequenza con cui è necessario rispondere a questo tipo di eventi imprevisti di riconfigurazione macroscopici (meglio noti come "giornate no").

In genere, è consigliabile evitare tali eventi tramite altre opzioni (ad esempio applicando aggiornamenti regolari del codice ed evitando prima di tutto di sovraccaricare il cluster) piuttosto che ricorrere alle limitazioni per impedire al cluster di utilizzare le risorse durante il tentativo di correzione automatica delle anomalie). Detto ciò potrebbero esserci casi in cui (fino alla relativa correzione) è necessario adottare un paio di limitazioni, anche se ciò dovesse richiedere più tempo per la stabilizzazione del cluster.

Le limitazioni incluse per impostazione predefinita sono:
-	GlobalMovementThrottleThreshold: consente di controllare il numero totale di movimenti nel cluster in un determinato intervallo di tempo (definito come GlobalMovementThrottleCountingInterval, valore in secondi)
-	MovementPerPartitionThrottleThreshold: consente di controllare il numero totale di movimenti per qualsiasi partizione del servizio in un determinato intervallo di tempo (MovementPerPartitionThrottleCountingInterval, valore in secondi)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Si noti che la maggior parte dei casi di utilizzo di queste limitazioni ha interessato clienti
-	già in un ambiente a risorse limitate (ad esempio, larghezza di banda di rete limitata in singoli nodi), il che significava che tali operazioni non avrebbero comunque avuto esito positivo o sarebbero state in ogni caso lente E
-	che i clienti erano certi di stare potenzialmente estendendo l'intervallo di stabilizzazione richiesto dal cluster, con la consapevolezza che avrebbero potuto finire per eseguire operazioni a un'affidabilità complessiva ridotta durante il periodo di limitazione.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Informazioni sul bilanciamento del carico nel cluster di Cluster Resource Manager](service-fabric-cluster-resource-manager-balancing.md)

<!---HONumber=AcomDC_0309_2016-->