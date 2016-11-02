<properties
   pageTitle="Cluster Resource Manager di Service Fabric - Costo dello spostamento | Microsoft Azure"
   description="Panoramica del costo di spostamento per i servizi di Service Fabric"
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

# Costo dello spostamento del servizio per influenzare le scelte di Cluster Resource Manager
Un fattore importante da considerare durante l'identificazione delle modifiche da apportare a un cluster e il punteggio di una soluzione è il costo complessivo dell'ottenimento di tale soluzione.

Lo spostamento delle istanze dei servizi o delle repliche comporta costi, come minimo in termini di tempo della CPU e ampiezza di banda della rete. Per i servizi con stati ci sarà anche il costo della quantità di spazio su disco necessaria per creare una copia dello stato prima dell'arresto delle vecchie repliche. Chiaramente è preferibile ridurre al minimo il costo delle soluzioni di Azure Service Fabric Cluster Resource Manager. Ma non bisogna neppure ignorare le soluzioni in grado di migliorare significativamente l'allocazione delle risorse del cluster.

Cluster Resource Manager prevede due modalità di calcolo dei costi e di relativo contenimento, anche quando tenta di gestire il cluster in base agli altri suoi obiettivi. Nella prima, durante la pianificazione di un nuovo layout per il cluster, Cluster Resource Manager conta ogni potenziale spostamento. In parole semplici, in presenza di due soluzioni con all'incirca lo stesso bilanciamento generale (punteggio) finale, è consigliabile scegliere quella con il costo minore (numero totale di spostamenti).

Questo approccio funziona bene. Ma come con i carichi predefiniti o statici, è improbabile che in qualsiasi sistema complesso tutti gli spostamenti siano uguali. Alcuni possono risultare molto più costosi.

## Modifica dei costi di spostamento di una replica e fattori da considerare
Analogamente ai report sul carico, un'altra funzionalità di Cluster Resource Manager, questo servizio offre un modo per analizzare il costo dello spostamento in un determinato momento.

Codice:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost ha quattro livelli: Zero, Low, Medium e High. Questi sono correlati, escluso il livello Zero. Zero indica che lo spostamento di una replica è gratuito e non deve influire sul punteggio della soluzione. Impostare il costo di spostamento su High *non* garantisce che la replica non verrà spostata, ma solo che non sarà spostata senza un valido motivo.

![Costo di spostamento come fattore da considerare per la selezione delle repliche da spostare][Image1]

MoveCost consente di trovare le soluzioni che causano un'interruzione complessivamente minima e che sono più facili da realizzare garantendo allo stesso tempo un bilanciamento equivalente. Il concetto di costo di un servizio può essere correlato a molti aspetti. I fattori più comuni per il calcolo del costo di spostamento sono:

- La quantità di stato o dati che il servizio deve spostare.
- Il costo di disconnessione dei client. Il costo di spostamento di una replica primaria è solitamente maggiore del costo di spostamento di una replica secondaria.
- Il costo di interruzione di un'operazione in corso. Alcune operazioni a livello di archivio dati o operazioni eseguite in risposta a una chiamata del client sono costose. Dopo un certo punto è preferibile non arrestarle se non è indispensabile. Quindi, per la durata dell'operazione, aumentare il costo per ridurre la probabilità che l'istanza o la replica del servizio si sposti. Al termine dell'operazione, si ripristinerà lo stato normale.

## Passaggi successivi
- Cluster Resource Manger di Service Fabric usa le repliche per gestire il consumo e la capacità del cluster. Per altre informazioni sulle metriche e sulla relativa configurazione, consultare [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md).
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere [Bilanciamento del carico nel cluster di Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0831_2016-->