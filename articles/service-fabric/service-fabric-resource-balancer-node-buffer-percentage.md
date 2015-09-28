<properties
   pageTitle="Percentuale di buffer dei nodi"
   description="Panoramica del ruolo della percentuale buffer dei nodi in Resource Balancer"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# Panoramica della percentuale buffer dei nodi

Attualmente il cliente può specificare il limite di capacità dei nodi come vincolo che deve essere rispettato da Resource Balancer in base alla priorità definita. Se la priorità del vincolo di capacità è alta (il vincolo non può essere violato) e se i nodi cluster sono utilizzati in modo consistente, è possibile che il failover non avvenga immediatamente o che la capacità dei nodi non venga rispettata.

Possono verificarsi problemi se i nodi con le repliche secondarie sono prossimi al limite di capacità quando un nodo con la replica primaria viene disattivato. In tal caso, se il carico primario è maggiore di quello secondario, la replica secondaria può essere alzata di livello solo dopo aver eseguito l'overcommit del nodo o la copia della replica.

Con la logica del raggruppamento proattivo in esecuzione, sarà prossimo al limite di capacità un maggior numero di nodi cluster. La percentuale buffer dei nodi è una funzionalità che impedisce il prolungamento del tempo di failover o l'overcommit dei nodi durante il failover, offrendo ai clienti la possibilità di specificare la percentuale del nodo che deve rimanere libera. La repliche dei nuovi servizi non devono essere aggiunte allo spazio buffer dei nodi, ma Resource Balancer deve essere capace di usare la capacità totale dei nodi (considerando lo spazio buffer) per i failover e l'aggiunta di repliche mancanti.

È possibile usare questa funzionalità anche nel caso in cui il raggruppamento proattivo in base alle metriche sia disattivato.

L'esempio di codice illustra come le percentuali buffer dei nodi per le metriche vengano configurate per ogni metrica tramite l'elemento FabricSettings nel manifesto del cluster.

``` xml
<FabricSettings>
  <Section Name=" NodeBufferPercentage">
    <Parameter Name="MetricName" Value="0.1"/>
  </Section>
</FabricSettings>

```

Il valore 0,1 della metrica denominata MetricName indica che il 10% della capacità di ogni nodo per MetricName deve rimanere libero.

Se il valore non è specificato in questa sezione, come valore predefinito verrà usato 0.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per altre informazioni: [Architettura di Resource Balancer](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=Sept15_HO3-->