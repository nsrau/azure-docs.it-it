<properties
   pageTitle="Cluster Resource Manager di Service Fabric - Costo dello spostamento | Microsoft Azure"
   description="Overview of Movement Cost for Service Fabric Services"
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

# Costo dello spostamento del servizio per influenzare le scelte di Resource Manager
Un altro fattore importante da prendere in considerazione durante l'identificazione delle modifiche da apportare a un cluster e il punteggio di una determinata soluzione è il costo complessivo del raggiungimento di tale soluzione.

Lo spostamento di istanze o repliche del servizio presenta come minimo dei costi in termini di tempo CPU e larghezza di banda della rete mentre per i servizi con stato il costo riguarda la quantità di spazio su disco necessario per creare una copia dello stato prima dell'arresto di vecchie repliche. Ovviamente si desidera ridurre al minimo il costo di qualsiasi soluzione proposta da Cluster Resource Manager, ma allo stesso tempo non si vogliono ignorare soluzioni in grado di migliorare significativamente l'allocazione delle risorse del cluster.

Cluster Resource Manager prevede due modalità di calcolo dei costi e di relativo contenimento, anche quando si tenta di gestire il cluster in base ai suoi altri obiettivi. Nella prima, durante la pianificazione di un nuovo layout per il cluster, Cluster Resource Manager conta ogni potenziale spostamento. In parole semplici, in presenza di due soluzioni con all'incirca lo stesso bilanciamento generale (punteggio) finale, è consigliabile scegliere quella con il costo minore (numero totale di spostamenti).

Questa struttura funziona abbastanza bene, fino a quando non si presenta lo stesso problema riscontrato con i carichi predefiniti o statici. È improbabile che in un sistema complesso tutti gli spostamenti siano uguali. Alcuni sono probabilmente molto più costosi.

## Modifica dei costi di spostamento di una replica e fattori da considerare
Analogamente ai report sul carico, un'altra funzionalità di Cluster Resource Manager, questo servizio offre un modo per analizzare il costo dello spostamento in qualsiasi momento.

Codice

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost ha quattro livelli: Zero, Low, Medium e High. Ancora una volta questi sono correlati, ad eccezione di Zero, che indica che lo spostamento di una replica è gratuito e non deve influire sul punteggio della soluzione. Impostare il costo di spostamento su High *non* garantisce che la replica non verrà spostata, ma solo che non sarà spostata senza un valido motivo.

![Move Cost as a Factor in Selecting Replicas for Movement][Image1]

MoveCost consente di trovare le soluzioni che complessivamente causano un'interruzione minima e che sono più facili da realizzare garantendo allo stesso tempo un bilanciamento equivalente. Il concetto di costo di un servizio può essere correlato a molti aspetti. Di seguito sono riportati i fattori principali per il calcolo del costo di spostamento:

1.	La quantità di stato o dati che il servizio deve spostare
2.	Il costo di disconnessione dei client (quindi il costo dello spostamento di una replica primaria sarà in genere maggiore del costo di spostamento di una replica secondaria)
3.	Il costo di interruzione di un'operazione in corso (alcune operazioni a livello di archivio dati o operazioni eseguite in risposta alla chiamata di un client sono costose e, superato un certo punto, non si vuole interromperle se non è necessario). Quindi, per la durata dell'operazione, aumentare il costo per ridurre la probabilità che l'istanza o la replica del servizio si sposti, ripristinando la normale impostazione a operazione conclusa.

## Passaggi successivi
- Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0824_2016-->