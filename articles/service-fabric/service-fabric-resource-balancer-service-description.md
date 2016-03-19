<properties
   pageTitle="Descrizioni del servizio di bilanciamento delle risorse"
   description="Una panoramica della configurazione delle descrizioni dei servizi per il servizio di bilanciamento delle risorse da usare | Microsoft Azure"
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
In un servizio, i [vincoli di posizionamento](service-fabric-placement-constraint.md) rappresentano il meccanismo attraverso il quale una particolare istanza del servizio seleziona le proprietà del nodo che richiede. Analogamente alle proprietà del nodo, si tratta di coppie chiave/valore che descrivono il nome della proprietà e i requisiti del servizio per il valore. Le singole istruzioni possono essere raggruppate con logica booleana semplice per creare il vincolo necessario. I vincoli vengono interpretati dal servizio di bilanciamento delle risorse di Service Fabric.

I vincoli di posizionamento del servizio possono essere definiti tramite il manifesto dell'applicazione o del servizio o direttamente nel codice.

Il manifesto del servizio fornisce le definizioni di `ServiceTypes`.

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

Il manifesto dell'applicazione fornisce le definizioni di `ServiceTemplates` o `DefaultServices`.

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

Il diagramma seguente illustra come, nel cluster con nodi, il solo nodo valido per il posizionamento del servizio sia N5.

![Vincoli di posizionamento][Image1]

Se il servizio specifica un vincolo per una proprietà non definita da un nodo specifico, il nodo non è considerato una corrispondenza, a prescindere dal valore specificato.

## Affinità del servizio
Forma speciale di vincolo di posizionamento, l'affinità del servizio consente la descrizione dei diversi servizi che, per vari motivi, presentano una dipendenza locale reciproca, ovvero che devono essere eseguiti nello stesso nodo affinché possano funzionare. Se da un lato questo tipo di architettura non è comune nelle applicazioni di Service Fabric, dall'altro lato Service Fabric riconosce che si tratta di un'architettura transitoria possibile per alcuni tipi di applicazioni legacy, rendendo quindi disponibile tale funzionalità.

Stabilire un'affinità di servizio tra due servizi garantisce che le repliche primarie di tali servizi vengano sempre collocate nello stesso nodo.

Le relazioni di affinità sono rappresentate da una gerarchia padre-figlio; ovvero in esse sono presenti "padri" e "figli". Il servizio che viene stabilito per primo è l'elemento padre, mentre il servizio figlio è il secondo elemento stabilito nella relazione. La relazione è modellata come vincolo "fisso".

L'affinità del servizio attualmente presenta le limitazioni seguenti:

- Non può essere usata in tutti i servizi con e senza stato.
- Non può essere usata in servizi senza stato con numeri di istanze diverse. Entrambi i servizi senza stato, ad esempio, devono avere la stessa proprietà InstanceCount quando vengono creati.
- Non può essere usata in servizi con stato volatili o persistenti con numeri diversi di repliche. Entrambi i servizi, ad esempio, devono avere gli stessi valori ReplicaSetSizes Target e Min specificati.
- Non può essere usata con servizi partizionati. Ogni servizio deve disporre di uno schema di partizione FABRIC\_PARTITION\_SCHEME\_SINGLETON.
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

Questo esempio di codice illustra l'uso alternativo delle definizioni di DefaultServices.

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

Questo esempio di codice illustra come creare una relazione di affinità dopo aver creato l'applicazione contenitore.

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Metriche di bilanciamento delle risorse
Mentre i vincoli di posizionamento del servizio e le proprietà del nodo rappresentano le regole e i criteri fissi che descrivono quali disposizioni di servizi sono valide, le metriche del bilanciamento delle risorse consentono in genere di descrivere la disposizione ottimale dei servizi all'interno del cluster. Le metriche definite dal servizio dispongono di varie proprietà che descrivono come vengono usate all'interno del cluster.

### Uso delle metriche predefinite
La prima cosa da comprendere è che le metriche delle risorse sono completamente facoltative per i servizi e che, per impostazione predefinita, il servizio di bilanciamento delle risorse di Service Fabric usa alcune metriche di sistema "integrate" per eseguire il bilanciamento delle risorse di base senza che gli amministratori del cluster o del servizio debbano eseguire alcuna azione. Le metriche predefinite di cui viene tenuta traccia nel servizio di bilanciamento delle risorse di Service Fabric e che vengono bilanciate all'interno del cluster sono PrimaryCount, ReplicaCount e Count. PrimaryCount è il numero di repliche primarie del servizio con stato in un nodo. ReplicaCount è il numero di repliche con stato in un nodo. Count è il numero totale di tutti gli oggetti servizio nel nodo, compresi i servizi con e senza stato. In genere la metrica PrimaryCount viene considerata la più importante e, pertanto, ha il peso maggiore, come descritto nella sezione seguente. La metrica PrimaryCount è seguita dalla metrica ReplicaCount e quindi dalla metrica Count.

Il diagramma seguente illustra un esempio di bilanciamento predefinito di tre servizi con stato con tre repliche ciascuno.

![Metriche predefinite][Image2]

In genere, le metriche predefinite sono sufficienti per molti servizi e devono essere usate, a meno che non esista un requisito specifico relativo ad altre metriche o funzionalità.

### Definizione di metriche personalizzate
Se le metriche predefinite non sono sufficienti per un determinato servizio o il servizio presenta requisiti noti in merito ai requisiti relativi a una determinata risorsa, ad esempio memoria o spazio su disco, è necessario usare metriche personalizzate. Le metriche personalizzate sono utili quando un servizio dispone di una o più risorse che devono essere bilanciate correttamente per evitare conflitti. L'uso di queste metriche può variare in maniera significativa tra le repliche all'interno del cluster. Una replica primaria, ad esempio, può usare il 100 percento della metrica, mentre un'altra ne può usare solo il 20%. Le metriche personalizzate possono essere utili anche per acquisire una risorsa notevolmente limitata in un computer, ad esempio memoria, spazio su disco o connessioni. Ma non solo: in alcuni casi, è possibile creare altre metriche personalizzate in grado di acquisire o rappresentare metriche senza limiti superiori nei nodi, ma che rappresentano consumo di lavoro e risorse dalla prospettiva del servizio, ad esempio, una metrica delle "transazioni in sospeso correnti". Sebbene possa non essere disponibile un limite per questa "capacità" della metrica all'interno di un nodo, esiste comunque un vantaggio in termini di prestazioni, poiché la metrica viene distribuita uniformemente in tutto il cluster.

Tenere presente che se un servizio definisce una metrica personalizzata, il servizio non usa le metriche predefinite fornite dal sistema. Le metriche predefinite possono essere comunque utilizzate, ma devono essere esplicitamente incluse di nuovo nell'elenco di metriche del servizio al momento della creazione.

### Nomi delle metriche
Le metriche personalizzate possono essere create semplicemente definendo un nome di metrica quando si crea un servizio. Il servizio di bilanciamento delle risorse di Service Fabric associa le metriche per nome. Pertanto, se la metrica viene usata come una capacità all'interno di definizioni dei nodi o di servizi, i nomi delle metriche devono corrispondere esattamente affinché il servizio di bilanciamento delle risorse di Service Fabric possa associarli.

### Pesi metrici
Quando un servizio definisce più metriche, è utile anche definirne i pesi metrici. Diversi pesi metrici indicano al servizio di bilanciamento delle risorse di Service Fabric quale metrica è più importante per il funzionamento del servizio. Una coda in memoria, ad esempio, può essere condizionata dalla larghezza di banda di rete, ma probabilmente sarà ancora più vincolata dall'uso effettivo di memoria nel nodo. Pertanto, se la coda può avere più metriche, la metrica che rappresenta l'utilizzo della memoria ha il peso maggiore. In modo analogo, un database persistente dipende probabilmente dall'uso di memoria e disco. Tuttavia, se una quantità limitata di memoria riduce la possibilità di elaborare query complesse, l'esaurimento dello spazio su disco impedisce ulteriori operazioni di archiviazione, creando in questo modo una situazione ancora più critica. Pertanto, l'archivio permanente sceglie probabilmente lo spazio su disco come metrica con il peso maggiore.

Tenere presente che il peso viene usato dal servizio di bilanciamento delle risorse di Service Fabric solo quando non è in grado di bilanciare completamente un set di metriche ed è quindi necessario trovare soluzioni per cui una metrica viene bilanciata di meno rispetto a un'altra. In questo caso, il bilanciamento della metrica con il peso inferiore è considerato di priorità inferiore rispetto alla metrica con il peso maggiore e, quindi, il servizio di bilanciamento delle risorse di Service Fabric genera proposte che prediligono il bilanciamento della metrica con il peso maggiore.

Anche quando il servizio di bilanciamento delle risorse di Service Fabric riconosce che più servizi hanno definito la stessa metrica per nome, considera tali metriche come la stessa metrica. Nei casi in cui la metrica è la stessa ma i pesi sono diversi, il servizio di bilanciamento delle risorse calcola la media dei pesi specifici per determinare i pesi effettivi da usare in fase di esecuzione. Pertanto, se un servizio definisce un peso pari a zero, che indica che non è richiesto bilanciamento, ma un altro servizio definisce la stessa metrica con un peso pari a High, il peso reale si colloca nel mezzo.

![Pesi metrici][Image3]

Questo esempio illustra come due diversi scenari di pesi metrici creino due diversi risultati di bilanciamento, in cui la metrica con la priorità più elevata è complessivamente meglio distribuita. Nell'esempio di sinistra B è bilanciato meglio, mentre in quello di destra è bilanciato meglio A. Poiché in questo esempio non esiste una soluzione di bilanciamento che consenta il bilanciamento di entrambe le metriche, il servizio bilanciamento delle risorse di Service Fabric usa il peso delle metriche per determinare quella più importante e, quindi, la metrica da preferire durante il bilanciamento.

Il peso metrico "zero" viene specificato nei casi in cui un servizio tiene traccia di una metrica ma non richiede alcun tipo di bilanciamento basato sulla metrica. Si consideri, ad esempio, una metrica con un limite esplicito in nodi diversi, ma per la quale "anche l'uso tra i nodi" (ciò che viene generalmente garantito dal servizio di bilanciamento delle risorse) non è importante. Per questi tipi di metriche, che non richiedono un bilanciamento bensì l'applicazione di limiti impostati nei nodi, è possibile definire il peso di bilanciamento "zero". Nella fase di esecuzione, il servizio di bilanciamento delle risorse di Service Fabric impone la capacità, ma non tenta di bilanciare la metrica in modo proattivo, anche se l'uso di tale metrica è molto sbilanciato tra i nodi.

### Carico predefinito della metrica per un ruolo primario o secondario
Quando si definisce una metrica per un servizio, è necessario specificare il consumo previsto per la metrica se il servizio si trova nel ruolo primario o secondario. Queste informazioni non solo consentono il bilanciamento delle risorse per posizionare inizialmente il servizio in modo efficiente, ma possono essere usate anche come una buona approssimazione dell'uso effettivo delle metriche da parte del servizio al quale sono correlate per tutta la sua durata. Il servizio di bilanciamento delle risorse di Service Fabric considera automaticamente il consumo metrico non solo quando si posiziona il servizio, ma anche in qualsiasi momento in cui deve spostare le repliche a causa del bilanciamento o di altre modifiche nel cluster. Se il carico di un servizio è abbastanza stabile e prevedibile, impostando questi valori il servizio può rifiutare esplicitamente di creare report sul carico durante la fase di esecuzione.

Questo esempio di codice illustra un servizio che definisce completamente due metriche personalizzate, una per l'utilizzo della memoria e l'altra per lo spazio su disco.

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

Questo esempio di codice illustra le definizioni di ServiceTypes tramite il manifesto del servizio.

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

Questo esempio di codice illustra le definizioni di ServiceTemplates tramite il manifesto del servizio.

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
Questo esempio di codice illustra le definizioni di DefaultServices tramite il manifesto dell'applicazione.

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

Poiché i valori di carico predefiniti non vengono aggiornati, a meno che i servizi non optino per la creazione di report sul carico in fase di esecuzione tramite codice, i valori possono essere usati anche per istituire più di un modello di bilanciamento della risorsa "prenotazione capacità". Ad esempio, se i carichi di lavoro rientrano generalmente in diversi intervalli di dimensioni e un numero noto di unità di lavoro può essere inserito in un nodo in qualsiasi momento, in questo caso è possibile creare una metrica personalizzata di "unità", in modo che la capacità del nodo e il carico predefinito del servizio vengano entrambi definiti in termini di unità.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per altre informazioni, vedere [Panoramica dell'architettura di Resource Balancer](service-fabric-resource-balancer-architecture.md), [Panoramica dei vincoli di posizionamento](service-fabric-placement-constraint.md).

[Image1]: media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: media/service-fabric-resource-balancer-service-description/MW.png

<!---HONumber=AcomDC_0114_2016-->