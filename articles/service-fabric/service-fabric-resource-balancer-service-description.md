<properties
   pageTitle="Descrizioni del servizio di bilanciamento delle risorse"
   description="Una panoramica della configurazione delle descrizioni del servizio per il bilanciamento delle risorse da utilizzare"
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
   ms.date="09/15/2015"
   ms.author="masnider"/>

# Panoramica delle descrizioni del servizio

## Vincoli di posizionamento
I [vincoli di posizionamento](../service-fabric-placement-constraint) in un servizio rappresentano il meccanismo attraverso il quale una particolare istanza del servizio seleziona le proprietà del nodo che richiede. Analogamente alle proprietà del nodo, si tratta di coppie chiave/valore che descrivono il nome della proprietà e i requisiti del servizio per il valore. Le singole istruzioni possono essere raggruppate con logica booleana semplice per creare il vincolo necessario. Tenere presente che i vincoli vengono interpretati dal bilanciamento delle risorse di Infrastruttura di servizi.

I vincoli di posizionamento del servizio possono essere definiti tramite il manifesto dell'applicazione o del servizio o direttamente nel codice.

Il manifesto del servizio fornisce le definizioni ServiceTypes.

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

Il manifesto dell'applicazione fornisce le definizioni ServiceTemplates o DefaultServices.

``` xml
<ServiceTemplates>
    <StatefulService ServiceTypeName="QueueReplica">
      <SingletonPartition></SingletonPartition>
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulService>
  </ServiceTemplates>

  <DefaultServices>
    <Service Name="QR">
      <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueReplica" TargetReplicaSetSize="3">
        <SingletonPartition/>
        <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
      </StatefulService>
    </Service>
  </DefaultServices>
```

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true  && SpindleCount >= 4)";
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Nel diagramma seguente viene illustrato che nel cluster con nodi, il solo nodo valido per la selezione di questo servizio è N5.

![Vincoli di posizionamento][Image1]

Se il servizio specifica un vincolo per una proprietà non definita da un nodo specifico, tale nodo non è considerato una corrispondenza, a prescindere dal valore specificato.

## Affinità del servizio
Forma speciale di vincolo di posizionamento, l'affinità del servizio consente la descrizione dei diversi servizi che, per vari motivi, presentano una dipendenza locale reciprocamente, il che significa che entrambi i servizi devono essere eseguiti nello stesso nodo affinché funzionino. Se questo tipo di architettura può non essere comune nelle applicazioni di Infrastruttura di servizi, Infrastruttura di servizi riconosce che si tratta di un'architettura transitoria possibile per alcuni tipi di applicazioni legacy, rendendo quindi disponibile tale funzionalità.

Stabilire un'affinità del servizio tra due servizi garantisce che le repliche primarie di tali servizi vengano sempre collocate nello stesso nodo.

Le relazioni di affinità sono rappresentate da una gerarchia padre-figlio, vale a dire, dove sono presenti "padri" e "figli". Il servizio che viene stabilito per primo è l'elemento padre e il servizio stabilito per secondo è l’elemento figlio nella relazione. La relazione è modellata come vincolo "fisso".

L'affinità del servizio attualmente presenta le limitazioni seguenti:

- Non può essere utilizzata in tutti i servizi con e senza stato.
- Non può essere utilizzata in servizi senza stato con numeri di istanze diverse, ad esempio, entrambi i servizi senza stato dovrebbero avere la stessa proprietà InstanceCount quando vengono creati.
- Non può essere utilizzata in servizi volatili o persistenti con stato con numeri diversi di repliche, ad esempio, entrambi i servizi devono avere gli stessi valori ReplicaSetSizes Target e Min specificati.
- Non può essere utilizzata con servizi partizionati. Ogni servizio deve disporre di uno schema di partizione FABRIC\_PARTITION\_SCHEME\_SINGLETON.
- Le relazioni di affinità, analogamente alle altre proprietà della descrizione del servizio, vengono impostate quando il servizio viene creato e non possono essere modificate.
- Non sono consentite le catene di servizi. Se più servizi devono essere inseriti in una relazione di affinità, devono utilizzare un modello "a stella".

``` xml
<ServiceTemplates>
  <StatelessService ServiceTypeName="StatelessCalculatorService" InstanceCount="5">
    <SingletonPartition></SingletonPartition>
      <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
  </StatelessService>
</ServiceTemplates>
```

Nell'esempio di codice viene illustrato l'utilizzo alternativo delle definizioni DefaultServices.

``` xml
<DefaultServices>
  <Service Name="childService">
    <StatelessService InstanceCount="3" ServiceTypeName="calculatorService">
      <SingletonPartition/>
     <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
    </StatelessService>
  </Service>
</DefaultServices>
```

Nell'esempio di codice viene illustrato come creare una relazione di affinità dopo aver creato l'applicazione contenitore.

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Metriche di bilanciamento delle risorse
Mentre i vincoli di posizionamento del servizio e le proprietà del nodo rappresentano le regole e i criteri fissi che descrivono quali disposizioni di servizi sono valide, le metriche del bilanciamento delle risorse consentono in genere di descrivere la disposizione ottimale dei servizi all'interno del cluster. Le metriche definite dal servizio dispongono di varie proprietà che descrivono come vengono utilizzate all'interno del cluster.

### Utilizzo delle metriche predefinite
La prima cosa da comprendere è che le metriche delle risorse sono completamente facoltative per i servizi e che, per impostazione predefinita, il bilanciamento delle risorse di Infrastruttura di servizi utilizza alcune metriche di sistema "integrate" per eseguire il bilanciamento delle risorse di base senza che gli amministratori del cluster o del servizio debbano eseguire alcuna azione. Le metriche predefinite di cui viene tenuta traccia nel bilanciamento delle risorse di Infrastruttura di servizi e che vengono bilanciate da questa funzionalità sono PrimaryCount, ReplicaCount e Count. PrimaryCount è il numero di repliche del servizio con stato primario in un nodo. ReplicaCount è il numero di repliche con stato in un nodo. Count è il numero totale di tutti gli oggetti servizio nel nodo, compresi i servizi con e senza stato. In genere la metrica PrimaryCount viene considerata la più importante e, pertanto, ha il maggior peso, come descritto nella sezione seguente. La metrica PrimaryCount è seguita dalla metrica ReplicaCount e infine dalla metrica Count.

Nel diagramma seguente viene illustrato un esempio di bilanciamento predefinito di tre servizi con stato con tre repliche ciascuno.

![Metriche predefinite][Image2]

In genere, le metriche predefinite sono sufficienti per molti servizi e devono essere utilizzate a meno che non esista un requisito specifico per altre metriche o funzionalità.

### Definizione delle metriche personalizzate
Se le metriche predefinite non sono sufficienti per un determinato servizio o tale servizio presenta requisiti noti per i requisiti relativi a una determinata risorsa, ad esempio memoria o spazio su disco, è necessario utilizzare metriche personalizzate. Le metriche personalizzate sono utili quando un servizio dispone di una o più risorse che devono essere bilanciate correttamente per evitare conflitti. Quando l'utilizzo di queste metriche può variare notevolmente tra le repliche all'interno del cluster, ad esempio, uno primario potrebbe utilizzare il 100 percento della metrica, mentre un altro potrebbe utilizzare solo il 20%. È inoltre possibile utilizzare metriche personalizzate per acquisire una risorsa notevolmente limitata in un computer, ad esempio memoria, spazio su disco o connessioni. A volte, è inoltre possibile creare altre metriche personalizzate in grado di acquisire o rappresentare metriche senza limiti superiori nei nodi, ma che rappresentano consumo di lavoro e risorse dalla prospettiva del servizio, ad esempio, una metrica delle "transazioni in sospeso correnti". Potrebbe non essere disponibile un limite per questa metrica "capacità" in un determinato nodo. Esiste comunque un vantaggio in fatto di prestazioni garantendo che la metrica venga distribuita uniformemente in tutto il cluster.

Tenere presente che se un servizio definisce qualsiasi metrica, il servizio non usa le metriche predefinite fornite dal sistema. Le metriche predefinite possono essere comunque utilizzate, ma devono essere esplicitamente incluse di nuovo nell'elenco di metriche del servizio al momento della creazione.

### Nomi delle metriche
Le metriche personalizzate possono essere create solo mediante la definizione di un nome metrica quando si crea un servizio. Tenere presente che il bilanciamento delle risorse di Infrastruttura di servizi associa le metriche mediante il loro nome, pertanto se la metrica viene utilizzata come una capacità all'interno delle definizioni del nodo o nei servizi, allora i nomi delle metriche devono corrispondere esattamente in modo che il bilanciamento delle risorse di Infrastruttura di servizi possa associarli.

### Pesi metrici
Quando un servizio definisce più metriche, è utile anche definire i pesi metrici di tali metriche. Diversi pesi metrici indicano al bilanciamento delle risorse di Infrastruttura di servizi quale metrica è più importante per il funzionamento del servizio. Ad esempio, una coda in memoria può essere influenzata dalla larghezza di banda di rete, ma è probabilmente per lo più vincolata dall'uso di memoria effettivo nel nodo. Pertanto, se la coda può avere più metriche, la metrica che rappresenta l'utilizzo della memoria ha il peso maggiore. Analogamente, un database persistente probabilmente dipende dall'utilizzo di memoria e disco, ma mentre una quantità limitata di memoria riduce la possibilità di elaborare query complesse, l'esaurimento dello spazio su disco può impedire ulteriori operazioni di archiviazione, il che può probabilmente rappresentare una situazione più critica. Pertanto, l'archivio permanente sceglie probabilmente lo spazio su disco come metrica con il peso maggiore.

Tenere presente che il peso viene effettivamente utilizzato all'interno del bilanciamento delle risorse di Infrastruttura di servizi solo quando non è in grado di bilanciare completamente un set di metriche ed è pertanto necessario trovare soluzioni per cui una metrica viene bilanciata di meno rispetto a un'altra. In questo caso, il bilanciamento della metrica con il peso inferiore è considerato di priorità inferiore rispetto alla metrica con il peso maggiore, pertanto il bilanciamento delle risorse di Infrastruttura di servizi è dotato di proposte che prediligono il bilanciamento della metrica con il peso maggiore.

Anche quando il bilanciamento delle risorse di Infrastruttura di servizi riconosce che più servizi hanno definito la stessa metrica per nome, considera tali metriche come la stessa metrica. Nei casi in cui la metrica è la stessa ma i pesi sono diversi, il bilanciamento delle risorse calcola la media dei pesi specifici per determinare i pesi effettivi da utilizzare in fase di esecuzione. Pertanto, se un servizio definisce un peso pari a zero, che indica che non è richiesto bilanciamento, ma un altro servizio definisce la stessa metrica con un peso pari a High, il peso reale si colloca nel mezzo.

![Pesi metrici][Image3]

In questo esempio viene illustrato come due diversi scenari di pesi metrici creino due diversi risultati di bilanciamento in cui la metrica con la maggiore priorità è complessivamente meglio distribuita. Nell'esempio a sinistra, B è bilanciato meglio, mentre a destra, A è bilanciato meglio. Poiché in questo esempio non esiste alcuna soluzione di bilanciamento che comporta il bilanciamento di entrambe le metriche, il bilanciamento delle risorse di Infrastruttura di servizi utilizza il peso delle metriche per determinare quella più importante e, pertanto, la metrica da preferire durante il bilanciamento.

Tenere presente che il peso metrico pari a zero è fornito nei casi in cui un servizio tiene traccia di una metrica ma non richiede bilanciamento basato su tale metrica. Ad esempio, si consideri una metrica con un limite esplicito in nodi diversi, ma per la quale "anche l'utilizzo tra i nodi", ovvero ciò che garantisce generalmente la risorsa di bilanciamento, non è importante. Per questi tipi di metriche che non richiedono bilanciamento, ma richiedono l'applicazione di limiti impostati nei nodi, è possibile definire il peso di bilanciamento pari a zero. Durante la fase di esecuzione, il bilanciamento delle risorse di Infrastruttura di servizi impone la capacità, ma non tenta di bilanciare la metrica in modo proattivo, anche se l'utilizzo di tale metrica è molto sbilanciato tra i nodi.

### Carico predefinito della metrica per un ruolo primario o secondario
Quando si definisce una metrica per un servizio, è necessario fornire il consumo previsto per tale metrica quando il servizio si trova nel ruolo primario o secondario. Queste informazioni non solo consentono il bilanciamento delle risorse per posizionare inizialmente il servizio in modo efficiente, ma possono essere utilizzate come una buona approssimazione dell'utilizzo del servizio effettivo delle metriche alle quali è correlato per tutta la sua durata. Il bilanciamento delle risorse di Infrastruttura di servizi considera automaticamente il consumo metrico non solo quando si posiziona il servizio, ma in qualsiasi momento in cui deve spostare le repliche a causa del bilanciamento o di altre modifiche nel cluster. Se il carico di un servizio è abbastanza stabile e prevedibile, ciò significa che impostando questi valori, il servizio può rifiutare esplicitamente il caricamento di report durante la fase di esecuzione.

Nel seguente esempio di codice viene mostrato un servizio che definisce completamente due metriche personalizzate, uno per l'utilizzo della memoria e l'altro per lo spazio su disco.

``` cpp
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.Weight = ServiceLoadMetricWeight.High;
memoryMetric.PrimaryDefaultLoad = 100;
memoryMetric.SecondaryDefaultLoad = 50;

ServiceLoadMetricDescription diskMetric = new ServiceLoadMetricDescription();
diskMetric.Name = "DiskInMb";
diskMetric.Weight = ServiceLoadMetricWeight.Medium;
diskMetric.PrimaryDefaultLoad = 1024;
diskMetric.SecondaryDefaultLoad = 750;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(diskMetric);

Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Nell'esempio di codice vengono illustrate le definizioni ServiceTypes tramite il manifesto del servizio.

``` xml
<ServiceTypes>
  <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="true">
    <LoadMetrics>
      <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
      <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
    </LoadMetrics>
  </StatefulServiceType>
</ServiceTypes>
```

Nell'esempio di codice vengono illustrate le definizioni ServiceTemplates tramite il manifesto del servizio.

``` xml
<ServiceTemplates>
   <StatefulService ServiceTypeName="QueueReplica">
     <SingletonPartition></SingletonPartition>
     <LoadMetrics>
       <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
       <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
     </LoadMetrics>
   </StatefulService>
 </ServiceTemplates>
```
Nell'esempio di codice vengono illustrate le definizioni DefaultServices tramite il manifesto dell'applicazione.

``` xml
<DefaultServices>
  <Service Name="QueueServiceInstance">
    <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueService" TargetReplicaSetSize="3">
      <SingletonPartition/>
      <LoadMetrics>
        <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
        <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
      </LoadMetrics>
    </StatefulService>
  </Service>
</DefaultServices>
```

Poiché i valori di carico predefiniti non vengono aggiornati, a meno che i servizi non optino per il caricamento di report in fase di esecuzione tramite codice, i valori possono essere inoltre utilizzati per istituire più di un modello di bilanciamento della risorsa "prenotazione capacità". Ad esempio, se i carichi di lavoro in genere rientrano in diversi intervalli di dimensioni e un numero noto di unità di lavoro può essere inserito in un nodo in qualsiasi momento, è possibile creare una metrica personalizzata di "unità", dopo di che la capacità del nodo e il carico predefinito del servizio vengono definiti in termini di unità.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per ulteriori informazioni: [Architettura del bilanciamento delle risorse](service-fabric-resource-balancer-architecture.md), [Vincoli di posizionamento](service-fabric-placement-constraint.md)

[Image1]: media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: media/service-fabric-resource-balancer-service-description/MW.png
 

<!---HONumber=Sept15_HO3-->