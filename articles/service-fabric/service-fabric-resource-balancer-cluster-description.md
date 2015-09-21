<properties
   pageTitle="Descrizione del cluster di Resource Balancer"
   description="Specifica della descrizione del cluster per Resource Balancer"
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

# Descrizione del cluster

Service Fabric Resource Balancer offre diversi meccanismi per la descrizione di un cluster. In fase di runtime Resource Balancer usa queste informazioni per posizionare i servizi. In questo modo viene garantita la disponibilità elevata dei servizi in esecuzione sul cluster, assicurando al tempo stesso il massimo utilizzo delle risorse di quest'ultimo. Le funzionalità di Resource Balancer che descrivono un cluster sono domini di errore, domini di aggiornamento, proprietà del nodo e capacità del nodo. Resource Balancer offre inoltre di alcune opzioni di configurazione che consentono di modificarne le prestazioni.

## Concetti principali

### Domini di errore:

I domini di errore consentono agli amministratori del cluster di definire i nodi fisici su cui è probabile che si verifichino contemporaneamente degli errori a causa di dipendenze fisiche condivise, ad esempio fonti di alimentazione e origini di rete. I domini di errore rappresentano in genere gerarchie correlate a tali dipendenze condivise. Nel punto più alto dell'albero dei domini di errore sono presenti i nodi su cui con maggiore probabilità si verificheranno errori simultanei. La figura seguente mostra diversi nodi in cui i domini di errore sono organizzati gerarchicamente in ordine di data center, rack e blade.

![Domini di errore][Image1]

 Durante il runtime Service Fabric Resource Manager prende in considerazione i domini di errore e tenta di distribuire le repliche relative a un determinato servizio in modo che si trovino tutte in domini di errore separati. Questo processo assicura che, in caso di problemi in un dominio di errore, la disponibilità e lo stato del servizio non verranno compromessi. La figura seguente mostra le repliche di un servizio distribuite tra diversi domini di errore, anche se lo spazio disponibile è in realtà sufficiente per concentrarle su uno o due domini soltanto.

![Domini di errore][Image2]

I domini di errore vengono configurati nel manifesto del cluster. Ogni nodo viene definito in modo da trovarsi all'interno di un particolare dominio di errore. In fase di runtime, Resource Manager combina i report provenienti da tutti i nodi per ottenere una panoramica completa di tutti i domini di errore presenti nel sistema.

### Domini di aggiornamento

I domini di aggiornamento sono un'altra fonte di informazioni utilizzata da Resource Manager. Analogamente ai domini di errore, i domini di aggiornamento descrivono set di nodi che vengono arrestati per l'aggiornamento all'incirca alla stessa ora. I domini di aggiornamento non sono gerarchici e possono essere considerati dei tag.

Mentre i domini di errore sono definiti dal layout fisico dei nodi del cluster, i domini di aggiornamento vengono determinati dagli amministratori del cluster in base a criteri. I criteri sono correlati agli aggiornamenti all'interno del cluster. Più elevato è il numero di domini di aggiornamento, maggiore è il livello di granularità di aggiornamento e questo limita l'impatto sul cluster e sui servizi in esecuzione e impedisce che un qualsiasi errore interessi un elevato numero di servizi. In base ad altri criteri, ad esempio il tempo che Service Fabric deve attendere dopo l'aggiornamento di un dominio prima di passare a quello successivo, un numero elevato di domini di aggiornamento può aumentare anche la quantità di tempo necessaria per completare un aggiornamento nel cluster.

Per questi motivi, Gestione risorse aggiorna le informazioni sui domini e distribuisce le repliche tra i domini di aggiornamento di un cluster, come avviene per i domini di errore. I domini di aggiornamento possono presentare o meno una corrispondenza uno a uno con i domini di errore. In genere, tuttavia, questo tipo di corrispondenza non è previsto. La figura seguente mostra un livello di diversi domini di aggiornamento al di sopra dei domini di errore definiti in precedenza. Gestione risorse distribuisce ancora le repliche tra i domini in modo da evitare una concentrazione di domini di errore o aggiornamento e repliche, così da assicurare l'elevata disponibilità del servizio nonostante la presenza di aggiornamenti o errori nel cluster.

![Domini di aggiornamento][Image3]

I domini di aggiornamento e i domini di errore sono entrambi configurati come parte della definizione del nodo all'interno del manifesto del cluster, come mostrato di seguito:

``` xml
<Infrastructure>
    <WindowsServer>
      <NodeList>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node1" FaultDomain="fd:/RACK1/BLADE1" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node2" FaultDomain="fd:/RACK2/BLADE1" UpgradeDomain="ud:/UD2"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node3" FaultDomain="fd:/RACK3/BLADE2" UpgradeDomain="ud:/UD3"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node4" FaultDomain="fd:/RACK2/BLADE2" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node5" FaultDomain="fd:/RACK1/BLADE2" UpgradeDomain="ud:/UD2"/>
        </NodeList>
    </WindowsServer>
</Infrastructure>
```
- Nelle distribuzioni di Azure, i domini di errore e quelli di aggiornamento vengono assegnati da Azure. Di conseguenza, la definizione dei nodi e dei ruoli all'interno dell'opzione Infrastruttura di Azure non include informazioni sui domini di errore o aggiornamento.

### Proprietà del nodo
Le proprietà del nodo consentono di definire le coppie chiave/valore che forniscono metadati aggiuntivi per un determinato nodo. Tra le proprietà del nodo è possibile elencare la presenza di un'unità disco rigido o di una scheda video, il numero di spindle del disco rigido, i core e altre caratteristiche fisiche.

È possibile usare le proprietà del nodo anche per specificare proprietà più astratte per facilitare le decisioni relative ai criteri. È possibile, ad esempio, assegnare un "colore" a diversi nodi all'interno di un cluster per segmentare quest'ultimo in sezioni differenti. L'esempio di codice mostra che le proprietà del nodo sono definite tramite il manifesto del cluster come parte delle definizioni del tipo di nodo, che possono essere applicate a più nodi all'interno del cluster.

Le proprietà di posizionamento NodeName, NodeType, FaultDomain e UpgradeDomain hanno valori predefiniti. Service Fabric fornirà automaticamente i valori predefiniti per consentire all'utente di usarli al momento della creazione del servizio. Gli utenti non devono specificare proprietà di posizionamento personalizzate usando questi nomi.

``` xml
<NodeTypes>
  <NodeType Name="NodeType1">
    <PlacementProperties>
      <Property Name="HasDisk" Value="true"/>
      <Property Name="SpindleCount" Value="4"/>
      <Property Name="HasGPU" Value="true"/>
      <Property Name="NodeColor" Value="blue"/>
      <Property Name="NodeName" Value="Node1"/>
      <Property Name="NodeType" Value="NodeType1"/>
      <Property Name="FaultDomain" Value="fd:/RACK1/BLADE1"/>
      <Property Name="UpgradeDomain" Value="ud:/UD1"/>
    </PlacementProperties>
  </NodeType>
    <NodeType Name="NodeType2">
    <PlacementProperties>
      <Property Name="HasDisk" Value="false"/>
      <Property Name="SpindleCount" Value="-1"/>
      <Property Name="HasGPU" Value="false"/>
      <Property Name="NodeColor" Value="green"/>
    </PlacementProperties>
  </NodeType>
</NodeTypes>
```

Durante il runtime, Resource Balancer usa le informazioni sulle proprietà del nodo per assicurare che i servizi che richiedono funzionalità specifiche siano posizionati sui nodi appropriati.

### Capacità del nodo
Le capacità del nodo sono coppie chiave/valore che definiscono il nome e la quantità di una particolare risorsa utilizzabile da parte di un determinato nodo. L'esempio di codice mostra un nodo che dispone di capacità per una metrica denominata "MemoryInMb" con un valore predefinito di 2048 MB di memoria disponibile. Analogamente alle proprietà del nodo, le capacità vengono definite tramite il manifesto del cluster.

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![Capacità del nodo][Image4]

Poiché i servizi in esecuzione su un nodo possono aggiornare i propri requisiti di capacità tramite la funzionalità per la creazione di report sul carico, Resource Balancer verifica periodicamente se un nodo ha una capacità adeguata o superiore rispetto alle metriche. Nel secondo caso, Resource Balancer può spostare i servizi su nodi meno carichi per ridurre i conflitti di risorse e migliorare le prestazioni e l'utilizzo complessivi.

Si noti che, sebbene una determinata metrica possa essere elencata anche nella sezione delle proprietà del tipo di nodo, se si tratta di una proprietà del nodo che può essere utilizzata in fase di runtime, è preferibile elencarla come capacità. Elencare la metrica anche come proprietà consente a un servizio che dipende da "requisiti hardware minimi" di eseguire query per il nodo con vincoli di posizionamento. Questo può essere necessario se la risorsa viene utilizzata da altri servizi durante il runtime. Si consiglia di includere la metrica anche come capacità, in modo che Resource Balancer possa eseguire azioni aggiuntive.

Quando si creano nuovi servizi, Service Fabric Cluster Resource Balancer usa le informazioni sulla capacità dei nodi esistenti e sull'utilizzo dei servizi esistenti per determinare se la capacità disponibile è sufficiente per posizionare l'intero nuovo servizio. Se la capacità non è sufficiente, la richiesta di creazione del servizio viene rifiutata e viene visualizzato un messaggio di errore in cui si comunica che la restante capacità disponibile sul cluster è insufficiente.


### Configurazioni di Resource Balancer

Nel manifesto del cluster sono presenti i diversi valori di configurazione riportati di seguito che consentono di definire il comportamento complessivo di Resource Balancer:

- Le soglie di bilanciamento determinano il grado di sbilanciamento a cui può giungere un cluster riguardo a una particolare metrica prima che Resource Balancer venga eseguito. Una soglia di bilanciamento è il rapporto massimo tra i nodi utilizzati al massimo e quelli utilizzati al minimo che Resource Balancer può accettare prima di eseguire un ribilanciamento dei cluster.

La figura seguente mostra due esempi in cui la soglia di bilanciamento per la metrica specificata è 10.

![Soglia di bilanciamento][Image5]

Si noti che, in questo caso, l’utilizzo su un nodo non prende in considerazione la dimensione del nodo determinata dalla relativa capacità, ma prende in considerazione solo l’uso assoluto attualmente segnalato sul nodo per la metrica specificata.

L'esempio di codice illustra che le soglie di bilanciamento delle metriche vengono configurate per ogni metrica tramite l'elemento FabricSettings nel manifesto del cluster.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

- Le soglie di attività agiscono come un gate sulla frequenza di esecuzione di Resource Balancer limitando i casi in cui quest'ultimo viene eseguito in presenza di una significativa quantità assoluta di carico. In questo modo, se il cluster non è troppo occupato per una particolare metrica, Resource Balancer non viene eseguito, anche se una piccola parte di tale metrica è notevolmente sbilanciata all'interno del cluster. Questa misura impedisce lo spreco di risorse derivante da un ribilanciamento del cluster per un guadagno trascurabile. La figura seguente mostra che la soglia di bilanciamento per la metrica è 4, mentre quella per l'attività è 1536.

![Soglia di attività][Image6] Si noti anche che, per causare l'esecuzione di Resource Balancer, la soglia dell'attività e quella di bilanciamento devono essere entrambe superate per la stessa metrica. Il superamento di una soltanto delle due soglie non determina l'esecuzione di Resource Balancer.

L'esempio di codice mostra che, analogamente a quanto avviene per le soglie di bilanciamento, le soglie di attività vengono configurate per metrica tramite l'elemento FabricSettings nel manifesto del cluster.

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval: determina la frequenza con cui Resource Balancer analizza le informazioni che deve controllare per rilevare eventuali violazioni dei vincoli. Le violazioni dei vincoli includono vincoli di posizionamento non rispettati, servizi con un numero di istanze o repliche minore del necessario, nodi con una capacità eccessiva per determinate metriche, domini di errore o aggiornamento sovraccarichi e sbilanciamenti del cluster. Resource Balancer esegue questa attività di analisi in base alle soglie di bilanciamento e attività e alla visualizzazione corrente del carico sui nodi del cluster. L'intervallo di aggiornamento viene definito in secondi. Il valore predefinito è 1. La frequenza di controllo e posizionamento dei vincoli può essere controllata alternativamente da due nuovi intervalli, MinConstraintCheckInterval e MinPlacementInterval. Se vengono definiti nuovi parametri, PLBRefreshInterval non verrà usato e non sarà possibile definirlo.

- MinConstraintCheckInterval: determina la frequenza con cui Resource Balancer analizza le informazioni che deve verificare per rilevare eventuali violazioni dei vincoli. Le violazioni dei vincoli includono vincoli di posizionamento non rispettati, servizi con un numero di istanze o repliche minore del necessario, nodi con una capacità eccessiva per determinate metriche, domini di errore o aggiornamento sovraccarichi e sbilanciamenti del cluster. Resource Balancer esegue questa attività di analisi in base alle soglie di bilanciamento e attività e alla visualizzazione corrente del carico sui nodi del cluster. L'intervallo di controllo dei vincoli viene definito in secondi. Se l'intervallo di controllo dei vincoli non è definito, il relativo valore predefinito sarà uguale al valore di PLBRefreshInterval. Non è possibile specificare entrambi i valori contemporaneamente.

- MinPlacementInterval: determina la frequenza con cui Resource Balancer verifica se esistono nuove istanze o repliche da posizionare e tenta di posizionarle. L'intervallo di posizionamento è definito in secondi. Se l'intervallo di posizionamento non è definito, il valore predefinito sarà uguale al valore di PLBRefreshInterval. Non è possibile specificare entrambi i valori contemporaneamente.

- MinLoadBalancingInterval: stabilisce l'intervallo di tempo minimo tra i cicli di bilanciamento delle risorse. Se Resource Balancer ha effettuato un'azione sulla base delle informazioni ricavate dall'analisi eseguita durante l'ultimo intervallo PLBRefreshInterval, non verrà eseguito nuovamente per almeno la stessa quantità di tempo. Tale periodo di tempo è definito in secondi. Il valore predefinito è 5.

Si noti che questi valori sono rigidi. Il bilanciamento complessivo del carico, tuttavia, viene eseguito nel cluster solo se le soglie di bilanciamento e attività sono rispettate per una determinata metrica. L'esempio di codice mostra che, se non è necessario eseguire un bilanciamento del carico accurato e attivo per un particolare cluster, questi valori possono essere resi meno rigidi usando la configurazione seguente all'interno dell'elemento FabricSettings. In questa configurazione di esempio l'intervallo di tempo minimo tra due controlli dei vincoli è 10 secondi, la frequenza di posizionamento è 5 secondi mentre il bilanciamento del carico viene eseguito ogni 5 minuti. Si noti che in questo caso PLBRefreshInterval non è definito.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

Nella seconda configurazione di esempio PLBRefreshInterval e MinLoadBalancingInterval sono definiti. Poiché il valore di PLBRefreshInterval è impostato su 2 secondi, anche il valore di MinPlacementInterval e MinConstraintCheckInterval sarà 2 secondi.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per altre informazioni: [Architettura di Resource Balancer](service-fabric-resource-balancer-architecture.md)


[Image1]: media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: media/service-fabric-resource-balancer-cluster-description/Thresholds.png
 

<!---HONumber=Sept15_HO2-->