<properties
   pageTitle="Raggruppamento proattivo basato sulle metriche | Microsoft Azure"
   description="Panoramica dell'uso del raggruppamento proattivo basato sulle metriche in Resource Balancer"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Raggruppamento proattivo basato sulle metriche

Una configurazione comune di Resource Balancer in Service Fabric prevede il raggiungimento di un utilizzo uniforme per ogni metrica su ciascun nodo. Resource Balancer ha anche il compito di trovare spazio per i servizi man mano che arrivano nuove richieste. Se non è disponibile spazio sufficiente per posizionare nuove repliche del servizio (tutte le repliche di tutte le partizioni del servizio), Resource Balancer proverà a creare lo spazio necessario spostando i carichi di lavoro esistenti. Si tratta di un meccanismo perfettamente normale, che tuttavia può richiedere molto tempo, in base alla densità del cluster e al livello di frammentazione dei carichi di lavoro.

Ad esempio, se il cluster viene usato in modo appropriato e il cliente vuole aggiungere un nuovo servizio con un carico predefinito di grandi dimensioni, ad esempio con capacità massima del nodo per una o più metriche, è possibile che Resource Balancer debba spostare un elevato numero di repliche per poter posizionare il nuovo servizio. Inoltre, se si tratta di servizi con stato che gestiscono carichi di grandi dimensioni, l’esecuzione dei necessari spostamenti può richiedere tempi lunghi poiché i dati devono essere copiati. Questi due aspetti possono determinare un prolungamento del tempo necessario per la creazione del nuovo servizio. Mentre in genere i servizi presentano un buon margine di tolleranza rispetto alla lentezza occasionale dei tempi di creazione, alcuni carichi di lavoro risultano essere meno tolleranti e vogliono essere creati nel più breve tempo possibile. In una condizione di stato stazionario, Resource Balancer deve assicurare che il cluster sia deframmentato per offrire una maggiore probabilità che sia disponibile lo spazio sufficiente per nuovi carichi di lavoro.

Il meccanismo di raggruppamento proattivo basato sulle metriche, noto anche come deframmentazione, viene eseguito nell'ambito della fase di bilanciamento di Resource Balancer e ha lo scopo di ridurre i tempi di creazione dei servizi mediante il raggruppamento dei carichi di lavoro in un numero inferiore di nodi, anziché mediante la loro distribuzione, come avviene con il bilanciamento. Quando una metrica è configurata per la deframmentazione, Resource Balancer mira a raggiungere la deviazione standard media massima, anziché quella minima usata per il bilanciamento.

Con la deviazione massima, Resource Balancer proverà a posizionare il maggior numero possibile di servizi su alcuni nodi, lasciando vuoti tutti gli altri. Uno dei vincoli di base per il posizionamento di nuovi servizi, inoltre, è che le repliche non possono trovarsi nello stesso dominio di aggiornamento o nello stesso dominio di errore. Poiché l'obiettivo è quello di poter aggiungere nuovi servizi in tempi rapidi, Resource Balancer deve mirare a raggiungere una deviazione standard minima della distribuzione dei carichi tra i domini di aggiornamento e quelli di errore (somma dei carichi dei servizi per ogni dominio di aggiornamento o dominio di errore). Il risultato sarà costituito dalla stessa quantità di spazio libero per ogni dominio di aggiornamento o di errore. La deframmentazione rispetta anche tutti gli altri vincoli del sistema, tra cui l'affinità, i vincoli di posizionamento e la capacità dei nodi in base alle metriche.

## Configurazione del cluster di Resource Balancer
Nel manifesto del cluster sono presenti i seguenti valori di configurazione, che definiscono il comportamento generale della funzionalità di raggruppamento in base alle metriche in Resource Balancer.

### DefragmentationMetrics

Metriche di frammentazione di cui deve tener conto Resource Balancer ai fini del raggruppamento proattivo o della deframmentazione. Tutte le metriche configurate devono essere specificate in questo elenco, come avviene negli elenchi relativi alle attività e alle soglie di bilanciamento. Se la metrica è specificata con il valore "true", verrà considerata come metrica di deframmentazione. Se invece è specificata con il valore "false", o se non è inclusa nell'elenco, non verrà presa in considerazione per la deframmentazione.

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalacingThreshholds

Le soglie di bilanciamento controllano il grado di frammentazione a cui può giungere il cluster in relazione a una determinata metrica prima che Resource Balancer esegua la fase bilanciamento, dove verrà applicata la logica di raggruppamento in base alle metriche. Se la metrica viene considerata come metrica di deframmentazione, la soglia di bilanciamento sarà costituita dal rapporto minimo consentito da Resource Balancer tra i nodi più usati e quelli meno usati per ogni dominio di aggiornamento o di errore, prima che venga eseguita la deframmentazione del cluster. Se in uno dei domini di aggiornamento o di errore questo rapporto è inferiore alla soglia, verrà avviata la fase di deframmentazione.

La figura seguente illustra due esempi in cui la soglia di bilanciamento per la metrica specificata è 10.

![Esempi di soglie di bilanciamento][Image1]

Si noti che, in questo caso, l'utilizzo su un nodo non prende in considerazione la dimensione del nodo, determinata dalla relativa capacità, ma solo l'uso assoluto attualmente riportato sul nodo per la metrica specificata.

Se la metrica non è specificata, il valore predefinito è 1. In questo caso, verrà eseguita la deframmentazione fino a quando il cluster non disporrà di almeno un nodo vuoto in ogni dominio di aggiornamento e di errore.

Il valore 0 indica che la metrica non deve essere presa in considerazione durante la fase di bilanciamento, anche se viene considerata ai fini della deframmentazione.

L'esempio di codice seguente mostra come le soglie di bilanciamento vengano configurate per ogni metrica tramite l'elemento FabricSettings nel manifesto del cluster.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per altre informazioni: [Architettura di Resource Balancer](service-fabric-resource-balancer-architecture.md)

[Image1]: media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png

<!---HONumber=AcomDC_1223_2015-->