<properties
   pageTitle="Raggruppamento proattivo basato sulle metriche"
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

Una configurazione comune di Service Fabric Resource Balancer prevede il raggiungimento di un utilizzo uniforme per ogni metrica su ciascun nodo. Resource Balancer ha anche il compito di trovare spazio per un servizio quando arrivano nuove richieste. Se lo spazio disponibile per posizionare nuove repliche del servizio non è sufficiente (tutte le repliche di tutte le partizioni del servizio), Resource Balancer proverà a creare spazio spostando i carichi di lavoro esistenti. Si tratta di un meccanismo assolutamente normale, che varia in base alla densità del cluster e al livello di frammentazione dei carichi di lavoro, ma può richiedere tempi lunghi.

Ad esempio, se il cluster è utilizzato in modo appropriato e il cliente vuole aggiungere un nuovo servizio con un carico predefinito di grandi dimensioni, ad esempio con capacità massima del nodo per una o più metriche, è possibile che Resource Balancer debba spostare un elevato numero di repliche per poter posizionare il nuovo servizio. Inoltre, se si tratta di servizi con stato che gestiscono carichi di grandi dimensioni, l’esecuzione dei necessari spostamenti può richiedere tempi lunghi poiché i dati devono essere copiati. Questi due aspetti possono determinare un prolungamento del tempo necessario per creare il nuovo servizio. Mentre in genere i servizi presentano un buon margine di tolleranza rispetto alla lentezza occasionale dei tempi di creazione, alcuni carichi di lavoro risultano essere meno tolleranti e vogliono essere creati immediatamente. In altre parole, in una condizione di stato stazionario Resource Balancer deve assicurare che il cluster sia deframmentato per consentire di avere a disposizione lo spazio sufficiente per nuovi carichi di lavoro.

Il meccanismo di raggruppamento proattivo basato sulle metriche, noto anche come deframmentazione, è parte integrante della fase di bilanciamento di Resource Balancer che viene eseguita allo scopo di ridurre i tempi necessari per la creazione del servizio mediante il raggruppamento dei carichi di lavoro su un numero inferiore di nodi, anziché mediante la distribuzione di tali carichi come avviene durante il bilanciamento. Quando una metrica è configurata per la deframmentazione, Resource Balancer punta a raggiungere la deviazione standard media massima anziché quella minima usata durante il bilanciamento.

Con la deviazione massima, Resource Balancer proverà a posizionare il maggior numero di servizi possibile su alcuni nodi lasciandone altri vuoti. Inoltre, uno dei vincoli di base per l'inserimento di nuovi servizi è che le repliche non possono trovarsi nello stesso dominio di aggiornamento o nello stesso dominio di errore. Poiché l'obiettivo è quello di poter aggiungere nuovi servizi rapidamente, Resource Balancer deve puntare a raggiungere una deviazione standard minima della distribuzione dei carichi tra i domini di aggiornamento e quelli di errore (somma dei carichi dei servizi per ogni dominio di aggiornamento o dominio di errore). Il risultato sarà costituito dalla stessa quantità di spazio libero per ogni dominio di aggiornamento o dominio di errore. La deframmentazione rispetta anche tutti gli altri vincoli del sistema, ad esempio quelli di affinità, di posizionamento e la capacità in base alle metriche dei nodi.

## Configurazione del cluster di Resource Balancer
Nel manifesto del cluster sono presenti valori di configurazione diversi che definiscono il comportamento generale della funzionalità di raggruppamento in base alle metriche in Resource Balancer:

### DefragmentationMetrics - Metriche che devono essere prese in considerazione da Resource Balancer ai fini del raggruppamento proattivo o deframmentazione.

Tutte le metriche configurate devono essere specificate in questo elenco, come avviene negli elenchi relativi alle soglie di attività e bilanciamento. Se la metrica è specificata con il valore "true", verrà considerata come metrica di deframmentazione. Se invece è specificata con il valore "false", o se non è specificata nell’elenco, non verrà presa in considerazione per la deframmentazione.

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalacingThreshholds

Le soglie di bilanciamento controllano il grado di frammentazione a cui può giungere il cluster in relazione a una determinata metrica prima che Resource Balancer esegua la fase bilanciamento, dove verrà applicata la logica di raggruppamento in base alle metriche. Se la metrica viene considerata come metrica di deframmentazione, la soglia di bilanciamento sarà costituita dal rapporto minimo consentito da Resource Balancer tra i nodi più usati e quelli meno usati per ogni dominio di aggiornamento o di errore prima che venga eseguita la deframmentazione del cluster. Se in uno dei domini di aggiornamento o di errore questo rapporto è inferiore rispetto alla soglia, verrà avviata la fase di deframmentazione.

La figura seguente illustra due esempi in cui la soglia di bilanciamento della metrica specificata è 10.

![Soglia di bilanciamento][Image1]

Si noti che, in questo caso, l’utilizzo su un nodo non prende in considerazione la dimensione del nodo determinata dalla relativa capacità, ma prende in considerazione solo l’uso assoluto attualmente segnalato sul nodo per la metrica specificata.

Se la metrica non è specificata, il valore predefinito è 1. In tal caso, verrà eseguita la deframmentazione fino a quando il cluster non disporrà di almeno un nodo vuoto in ogni dominio di aggiornamento e in ogni dominio di errore.

Il valore 0 indica che la metrica non deve essere presa in considerazione durante la fase di bilanciamento, sia che venga o che non venga considerata ai fini della deframmentazione.

L'esempio di codice illustra che le soglie di bilanciamento delle metriche vengono configurate per ogni metrica tramite l'elemento FabricSettings nel manifesto del cluster.

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
 

<!---HONumber=Oct15_HO3-->