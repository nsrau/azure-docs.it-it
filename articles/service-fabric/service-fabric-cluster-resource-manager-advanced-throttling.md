---
title: Limitazione in Cluster Resource Manager di Service Fabric | Microsoft Docs
description: Informazioni sulla configurazione delle limitazioni messe a disposizione da Cluster Resource Manager di Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Limitazione del comportamento di Cluster Resource Manager di Service Fabric
Anche se Cluster Resource Manager è stato configurato correttamente, il cluster può essere interrotto. Ad esempio potrebbero verificarsi errori di nodi simultanei o dominio di errore. Che cosa accadrebbe se ciò si verificasse durante l'aggiornamento? Resource Manager tenta di correggere tutti gli elementi nel modo migliore, ma in periodi come questo è consigliabile valutare l'adozione di una protezione che consenta al cluster stesso di stabilizzarsi (rientro dei nodi, ripristino automatico dell'integrità delle condizioni di rete, distribuzione di bit corretti). Per risolvere questi tipi di situazioni, Cluster Resource Manager di Service Fabric include diverse limitazioni. Si noti che queste limitazioni sono considerate "di disturbo" e, in genere, non devono essere usate senza che venga calcolata accuratamente la quantità di lavoro parallelo che può essere eseguito effettivamente nel cluster e la frequenza con cui è necessario rispondere a questo tipo di eventi imprevisti di riconfigurazione macroscopici (meglio noti come "giornate no").

In genere, è consigliabile evitare tali eventi tramite altre opzioni (ad esempio applicando aggiornamenti regolari del codice ed evitando prima di tutto di sovraccaricare il cluster) piuttosto che ricorrere alle limitazioni per impedire al cluster di utilizzare le risorse durante il tentativo di correzione automatica delle anomalie). Le limitazioni presentano i valori predefiniti che abbiamo trovato attraverso l'esperienza delle impostazioni predefinite, ma è probabilmente necessario darci un'occhiata e adeguarli al carico effettivo previsto. Sebbene non sia consigliabile imporre troppe limitazioni o carico al cluster, potrebbero esserci casi in cui (fino alla relativa correzione) è necessario adottare un paio di limitazioni, anche se ciò dovesse richiedere più tempo per la stabilizzazione del cluster.

## Configurazione delle limitazioni
Le limitazioni incluse per impostazione predefinita sono:

* GlobalMovementThrottleThreshold: consente di controllare il numero totale di movimenti nel cluster in un determinato intervallo di tempo (definito come GlobalMovementThrottleCountingInterval, valore in secondi)
* MovementPerPartitionThrottleThreshold: consente di controllare il numero totale di movimenti per qualsiasi partizione del servizio in un determinato intervallo di tempo (MovementPerPartitionThrottleCountingInterval, valore in secondi)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Si noti che la maggior parte dei casi d'uso di queste limitazioni ha interessato clienti che già si trovavano in un ambiente con limitazione delle risorse (ad esempio, larghezza della banda di rete limitata in singoli nodi o dischi che non soddisfacevano i requisiti per le compilazioni della replica parallela su esse imposte), con conseguente insuccesso o lentezza di tali operazioni. In queste situazioni, i clienti erano certi di stare potenzialmente estendendo l'intervallo di stabilizzazione richiesto dal cluster, con la consapevolezza che avrebbero potuto finire per eseguire operazioni a un'affidabilità complessiva ridotta durante il periodo di limitazione.

## Passaggi successivi
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
* Cluster Resource Manager dispone di molte opzioni per la descrizione del cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

<!---HONumber=AcomDC_0824_2016-->