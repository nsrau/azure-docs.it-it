<properties
   pageTitle="Descrizione del cluster di Resource Balancer | Microsoft Azure"
   description="Descrizione di un cluster di Service Fabric specificando i domini di errore, i domini di aggiornamento, le proprietà del nodo e le capacità del nodo per Cluster Resource Manager."
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
   ms.date="05/20/2016"
   ms.author="masnider"/>

# Descrizione di un cluster di Service Fabric
Cluster Resource Manager di Service Fabric fornisce alcuni meccanismi per descrivere un cluster. Durante la fase di esecuzione, Gestione risorse usa queste informazioni per assicurare la disponibilità elevata dei servizi in esecuzione sul cluster, assicurando anche che le risorse del cluster vengano usate correttamente.

## Concetti chiave
Le funzionalità di Resource Manager per i cluster che descrivono un cluster sono le seguenti:

- Domini di errore
- Domini di aggiornamento
- Proprietà del nodo
- Capacità del nodo

## Domini di errore
Un dominio di errore (FD, Fault Domain) è un'area di errore coordinato. Un singolo computer è un dominio di errore, perché il solo computer si può arrestare per molti motivi diversi, da interruzioni dell'alimentazione a errori delle unità o firmware NIC non valido. Alcuni computer connessi allo stesso commutatore Ethernet si trovano nello stesso dominio di errore, analogamente ai computer connessi a una singola fonte di alimentazione.

Se si configura un cluster personalizzato, è necessario prendere in considerazione tutte queste diverse aree di errore e assicurarsi che i domini di errore siano configurati correttamente, in modo che Service Fabric sia in grado di individuare una posizione sicura per i servizi. Per "sicura" si intende efficiente. Si vuole infatti evitare di posizionare i servizi in modo che la perdita di un dominio di errore provochi l'interruzione del servizio. Nell'ambiente di Azure si sfruttano le informazioni del dominio di errore fornite dal controller di infrastruttura/Resource Manager per configurare in modo corretto i nodi nel cluster per conto dell'utente. Nel grafico seguente (Figura 7) vengono colorate tutte le entità che restituiscono ragionevolmente come risultato un dominio di errore come semplice esempio e vengono elencati tutti i diversi domini di errore risultanti. In questo esempio sono presenti data center (DC), rack (R) e pannelli (B). Se ogni pannello include più macchine virtuali, è possibile che la gerarchia del dominio di errore includa un altro livello.

![Nodi organizzati tramite domini di errore][Image1]

 Durante la fase di esecuzione, Cluster Resource Manager di Service Fabric prende in considerazione i domini di errore e prova a distribuire le repliche relative a un determinato servizio in modo che si trovino tutte in domini di errore separati. Questo processo assicura che, in caso di problemi in un dominio di errore, la disponibilità del servizio non verrà compromessa.

 Cluster Resource Manager di Service Fabric non è influenzato dal numero di livelli presenti nella gerarchia. Dal momento che prova ad assicurare che la perdita di una parte della gerarchia non influisca sul cluster o sui servizi in esecuzione su di esso, è in genere consigliabile inserire lo stesso numero di computer in ogni livello di profondità del dominio di errore. In questo modo si evita che una parte della gerarchia finisca con l'includere più servizi rispetto agli altri.

 La configurazione del cluster in modo che la "struttura" dei domini di errore non sia bilanciata rende difficile a Resource Manager determinare l'allocazione ottimale delle repliche, in particolare perché ciò significa che la perdita di un dominio specifico potrebbe avere un impatto eccessivo sulla disponibilità del cluster. Resource Manager deve decidere se usare in modo efficiente i computer nel dominio con un numero elevato di servizi oppure se posizionare i servizi in modo che la perdita del dominio non provochi problemi.

 Nel diagramma seguente vengono configurati due cluster diversi, uno in cui i nodi sono distribuiti in modo ottimale nei domini di errore e uno in cui un dominio di errore include un numero più elevato di nodi. Si noti che in Azure le scelte relative a quali nodi vengono inseriti nei domini specifici vengono gestite automaticamente, in modo che non si verifichino mai situazioni di squilibrio di questo tipo. Se tuttavia si crea un cluster personalizzato in locale o in un altro ambiente, è necessario prendere in considerazione questo problema.

 ![Due layout diversi per i cluster][Image2]

## Domini di aggiornamento
I domini di aggiornamento (UD, Upgrade Domain) sono un'altra funzionalità che consente a Resource Manager di Service Fabric di comprendere il layout del cluster, in modo da pianificare una strategia per la gestione degli errori. I domini di aggiornamento definiscono aree, in realtà set di nodi, che risulteranno contemporaneamente inattive durante un aggiornamento.

I domini di aggiornamento sono molto simili ai domini di errore, ma con un paio di differenze essenziali. I domini di aggiornamento, prima di tutto, vengono in genere definiti tramite criteri, mentre i domini di errore vengono rigorosamente definiti dalle aree di errori coordinati e quindi in genere dal layout hardware dell'ambiente. Nel caso dei domini di aggiornamento, tuttavia, è possibile deciderne il numero. Un'altra differenza consiste nel fatto che, almeno attualmente, i domini di aggiornamento non sono gerarchici. Sono molto più simili a un semplice tag che a una gerarchia.

La figura seguente illustra una configurazione fittizia, in cui tre domini di aggiornamento sono suddivisi in tre domini di errore. Illustra anche una posizione possibile per tre repliche diverse di un servizio con stato. Si noti che si trovano tutte in diversi domini di errore e di aggiornamento. È quindi possibile perdere un dominio di errore nel corso di un aggiornamento del servizio, mantenendo comunque una copia in esecuzione del codice e dei dati nel cluster. In base alle esigenze specifiche, questa situazione potrebbe essere adeguata. Occorre tuttavia notare che questa copia potrebbe essere vecchia, perché Service Fabric usa la replica basata su quorum. Per non subire effettivamente le conseguenze di due errori, sono necessarie più repliche (almeno cinque).

![Posizionamento con domini di errore e di aggiornamento][Image3]

L'uso di un numero elevato di domini di aggiornamento presenta vantaggi e svantaggi. I vantaggi consistono nel fatto che ogni passaggio dell'aggiornamento è più dettagliato e quindi influisce su un numero minore di nodi o servizi. Risulta quindi necessario spostare un numero minore di servizi contemporaneamente, riducendo la varianza nel sistema e migliorando complessivamente l'affidabilità, perché una parte minore del servizio sarà interessata da eventuali problemi. Lo svantaggio dell'uso di un numero elevato di domini di aggiornamento consiste nel fatto che Service Fabric verifica l'integrità di ogni dominio di aggiornamento durante l'aggiornamento e assicura che il dominio di aggiornamento sia integro prima del passaggio al dominio di aggiornamento successivo. L'obiettivo di questa verifica consiste nell'assicurare che i servizi abbiano la possibilità di stabilizzarsi e che la rispettiva integrità venga convalidata prima della continuazione dell'aggiornamento, in modo da rilevare eventuali problemi. Questo compromesso è accettabile perché evita che modifiche non valide abbiano un impatto eccessivo sul servizio in un determinato momento.

Anche l'uso di un numero troppo ridotto di domini di aggiornamento ha conseguenze specifiche: mentre ogni singolo dominio di aggiornamento è inattivo e in fase di aggiornamento, una parte elevata della capacità complessiva non risulta disponibile. Ad esempio, se sono presenti solo tre domini di aggiornamento, 1/3 circa della capacità complessiva del servizio o del cluster non sarà disponibile in un determinato momento. Si tratta di una situazione non ottimale, perché è necessario che la capacità del resto del cluster sia sufficiente per soddisfare le esigenze del carico di lavoro. Ciò significa che in una situazione normale questi nodi presentano un carico inferiore al previsto, aumentando il valore COGS.

Non è previsto alcun limite effettivo per il numero totale di domini di errore o di aggiornamento in un ambiente e non sono previsti vincoli per le relative sovrapposizioni. Le strutture comuni sono in genere di tipo 1:1, in cui ogni dominio di errore univoco è mappato al rispettivo dominio di aggiornamento. Queste strutture includono un dominio di aggiornamento per ogni nodo, ovvero istanza fisica o virtuale del sistema operativo, e un modello "con striping" o a "matrice", in cui i domini di errore e i domini di aggiornamento formano una matrice con computer, in genere disposti lungo la diagonale.

![Layout dei domini di errore e di aggiornamento][Image4]

Non esiste un layout ottimale, ogni layout presenta vantaggi e svantaggi. Ad esempio, il modello di tipo 1FD:1UD è abbastanza semplice da configurare, mentre il modello con 1 UD per nodo è probabilmente quello più usato in passato per la gestione di insiemi ridotti di computer, in cui ogni computer viene disattivato in modo indipendente.

Il modello più comune, usato anche per i cluster ospitati di Azure Service Fabric, è basato sulla matrice FD/UD, in cui i domini di errore e i domini di aggiornamento formano una tabella e i nodi vengono posizionati a partire dalla diagonale. La presenza di un numero elevato o meno di elementi in ogni dominio dipende dal numero totale di nodi rispetto al numero di domini di errore e domini di aggiornamento. In altri termini, per cluster di dimensioni sufficientemente elevate, quasi tutte le situazioni risultano analoghe al modello di matrice a densità elevata, visualizzato nell'opzione in basso a destra della figura 10.

## Configurazione di domini di errore e di aggiornamento
La definizione dei domini di errore e dei domini di aggiornamento viene eseguita automaticamente nelle distribuzioni di Service Fabric ospitate in Azure. Service Fabric recupera semplicemente le informazioni sull'ambiente da Azure. L'utente può a sua volta scegliere il numero di domini desiderato. In Azure le informazioni del dominio di errore e del dominio di aggiornamento hanno un aspetto di "singolo livello", ma in realtà vengono incapsulate informazioni dai livelli inferiori dello stack di Azure e i domini di errore e i domini di aggiornamento logici vengono semplicemente presentati dal punto di vista dell'utente.

Se si configura il proprio cluster o se si vuole semplicemente provare a eseguire una topologia specifica nel computer di sviluppo, sarà necessario fornire manualmente le informazioni sul dominio di errore e sul dominio di aggiornamento. In questo esempio viene definito un cluster a 9 nodi che si estende su tre "data center", ognuno con tre rack, e tre domini di aggiornamento distribuiti in questi tre data center. Nel manifesto del cluster questa configurazione ha un aspetto analogo al seguente:

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```
> [AZURE.NOTE] Nelle distribuzioni di Azure, i domini di errore e quelli di aggiornamento vengono assegnati da Azure. Di conseguenza, la definizione dei nodi e dei ruoli all'interno dell'opzione di infrastruttura di Azure non include informazioni sui domini di errore o aggiornamento.

## Vincoli di posizionamento e proprietà dei nodi
In alcuni casi, essenzialmente nella maggior parte dei casi, si vuole assicurare che determinati carichi di lavoro vengano eseguiti solo su determinati nodi o set di nodi. Ad esempio, è possibile che alcuni carichi di lavoro richiedano GPU o SSD, mentre altri no. Un ottimo esempio è costituito essenzialmente da quasi tutte le architetture con n livelli, in cui determinati computer fungono da front-end/lato di gestione di interfacce dell'applicazione mentre un altro set, spesso con risorse hardware diverse, gestisce il lavoro dei livelli di calcolo o archiviazione. Service Fabric si aspetta che anche in uno scenario con microservizi si verifichino situazioni in cui determinati carichi di lavoro dovranno essere eseguiti in configurazioni hardware specifiche, ad esempio:

- Un'applicazione esistente con n livelli è stata "elevata e spostata" in un ambiente Service Fabric.
- Un carico di lavoro deve essere eseguito su hardware specifico per finalità di prestazioni, scalabilità o isolamento di sicurezza.
-	Un carico di lavoro deve essere isolato da altri carichi di lavoro per motivi relativi ai criteri o all'utilizzo delle risorse.

Per supportare questi tipi di configurazione, Service Fabric gestisce in modo eccellente i vincoli di posizionamento. I vincoli possono essere usati per indicare dove occorre eseguire determinati servizi. Il set di vincoli può essere esteso dagli utenti, ovvero le persone possono contrassegnare i nodi con proprietà personalizzate e quindi basare su di esse le selezioni.

![Layout di cluster con carichi di lavoro diversi][Image5]

I diversi tag di tipo chiave/valore sui nodi sono definiti proprietà di posizionamento dei nodi o semplicemente proprietà dei nodi, mentre l'istruzione sul servizio viene definita vincolo di posizionamento. Il valore specificato nella proprietà del nodo può essere una stringa, un valore booleano o un valore lungo firmato. Il vincolo può essere qualsiasi istruzione booleana che opera sulle diverse proprietà del nodo nel cluster e il servizio può essere posizionato solo nei nodi in cui l'istruzione restituisce "True". I nodi in cui non sono state definite proprietà non corrispondono ad alcun vincolo di posizionamento che contiene proprietà. Service Fabric definisce anche alcune proprietà predefinite, che possono essere successivamente usate automaticamente, senza alcun intervento da parte dell'utente. Al momento della stesura di questo articolo, le proprietà predefinite specificate a livello di ogni nodo sono NodeType e NodeName. In genere, NodeType è una delle proprietà più usate e corrisponde solitamente esattamente a un tipo di computer, che a sua volta corrisponde a un tipo di carico di lavoro in un'architettura di applicazione tradizionale a n livelli.

![Vincoli di posizionamento e proprietà dei nodi][Image6]

Si supponga che le proprietà seguenti del nodo siano state definite per un tipo di nodo specifico: ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasDisk" Value="true"/>
        <Property Name="Value" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

È possibile creare vincoli di posizionamento del servizio per un servizio analogo al seguente:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true && Value >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasDisk == true && Value >= 4"
```

Se si è sicuri che tutti i nodi di NodeType01 siano validi, è anche possibile selezionare semplicemente il tipo di nodo, usando vincoli di posizionamento analoghi a quelli mostrati nelle figure precedenti.

Uno dei vantaggi dei vincoli di posizionamento di un servizio consiste nel fatto che possono essere aggiornati in modo dinamico durante la fase di esecuzione. Se necessario, è quindi possibile spostare un servizio nel cluster, aggiungere e rimuovere requisiti e così via. Service Fabric assicura che il servizio sia sempre attivo e disponibile, anche durante l'applicazione di questi tipi di modifiche.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

I vincoli di posizionamento, oltre a molte altre proprietà che verranno illustrate, vengono specificati per ogni istanza diversa del servizio. Gli aggiornamenti sostituiscono (sovrascrivono) sempre quanto specificato in precedenza.

È anche importante notare che a questo punto le proprietà in un nodo vengono definite tramite la definizione del cluster e quindi non possono essere aggiornate senza aggiornare il cluster.

## Capacità
Uno dei processi più importanti di un agente di orchestrazione consiste nel semplificare la gestione dell'utilizzo delle risorse del cluster. Per eseguire in modo efficiente i servizi, occorre evitare una serie di nodi attivi, con conseguente conflitto per le risorse e prestazioni ridotte, mentre altri nodi sono inattivi, con conseguente spreco di risorse. Il bilanciamento verrà illustrato più avanti, ma occorre prima di tutto assicurarsi che i nodi non esauriscano le risorse.

Service Fabric rappresenta le risorse come "metriche". Per metrica si intende qualsiasi risorsa logica o fisica che deve essere descritta per Service Fabric, ad esempio "WorkQueueDepth" o "MemoryInMb". Le metriche sono diverse dai vincoli e dalle proprietà del nodo, perché le proprietà del nodo sono in genere descrittori statici dei nodi stessi, mentre le metriche sono relative alle risorse fisiche utilizzate dai servizi durante l'esecuzione su un nodo. Una proprietà può quindi essere ad esempio HasSSD e può essere impostata su True o False, ma la quantità di spazio disponibile sull'unità SSD e utilizzato dai servizi sarà indicata da una metrica come "DriveSpaceInMb". La capacità del nodo imposterebbe la metrica "DriveSpaceInMb" sulla quantità di spazio totale non riservato sull'unità e i servizi segnalerebbero la quantità di metrica usata durante la fase di esecuzione.

Se si disattivasse completamente il bilanciamento di tutte le risorse, Resource Manager di Service Fabric sarebbe comunque in grado di assicurare che nessun nodo superi la rispettiva capacità, a meno che il cluster non sia complessivamente troppo pieno. Le capacità costituiscono il meccanismo usato da Service Fabric per verificare la quantità di risorse disponibili per un nodo, sottraendo da questo valore l'utilizzo da parte di diversi servizi, come illustrato più avanti, per calcolare la quantità di risorse residue. Sia la capacità che l'utilizzo a livello di servizio sono espressi in termini di metriche.

Durante la fase di esecuzione, Resource Manager verifica la quantità di risorse presente in ogni nodo, definita dalla rispettiva capacità, e la quantità rimanente, sottraendo l'eventuale utilizzo dichiarato da ogni servizio. Queste informazioni consentono a Resource Manager di Service Fabric di individuare la posizione ottimale per l'inserimento o lo spostamento di repliche, in modo che i nodi non superino le rispettive capacità.

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "Memory";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 1024;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,1024,1024”)
```

![Nodi e capacità del cluster][Image7]

Questi valori sono disponibili nel manifesto del cluster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="Memory" Value="10"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

È anche possibile che il carico di un servizio cambi in modo dinamico. In questo caso è possibile che la posizione in cui si trova una replica o un'istanza non risulti più valida perché l'utilizzo combinato di tutte le repliche e le istanze del nodo supera la capacità del nodo. Lo scenario che consente di cambiare dinamicamente il carico verrà illustrato più avanti, ma per quanto riguarda la capacità, viene gestita nello stesso modo. Le funzionalità di gestione delle risorse di Service Fabric vengono attivate automaticamente e riportano il nodo sotto la soglia massima di capacità, spostando una o più repliche o istanze del nodo in nodo diversi. Durante questa operazione, Resource Manager prova a ridurre al minimo il costo di tutti gli spostamenti. Il concetto di costo verrà illustrato più avanti.

## Capacità del cluster
Se si vuole impedire che complessivamente il cluster sia troppo pieno, con il carico dinamico non sono effettivamente disponibili molte alternative. Poiché i servizi possono presentare picchi di carico indipendentemente dalle azioni effettuate da Resource Manager, è possibile che il cluster che attualmente non include molte risorse risulti insufficiente in seguito. Sono però disponibili alcuni controlli specifici che consentono di evitare errori di base. È prima di tutto possibile impedire la creazione di nuovi carichi di lavoro che riempirebbero il cluster.

Si supponga di creare un semplice servizio senza stato con alcuni carichi associati. La creazione di report sul carico predefinito e dinamico verrà illustrata in seguito. Per questo servizio specifico, si supponga che vengano ritenute rilevanti alcune risorse, ad esempio DiskSpace, e che per impostazione predefinita vengano utilizzate 5 unità di DiskSpace per ogni istanza del servizio. Si vogliono creare 3 istanze del servizio. L'installazione è riuscita. È quindi necessario che 15 unità di DiskSpace siano presenti nel cluster per consentire semplicemente di creare queste istanze del servizio. Service Fabric calcola continuamente la capacità complessiva e l'utilizzo di ogni metrica, quindi è possibile prendere facilmente una decisione e rifiutare la chiamata di creazione del servizio se lo spazio è insufficiente.

Si noti che, poiché il requisito prevede solo la disponibilità di 15 unità, è possibile che lo spazio venga allocato in molti modi diversi. È ad esempio possibile che in 15 nodi diversi rimanga solo un'unità di capacità o che rimangano tre unità di capacità su 5 nodi diversi. Se la capacità è insufficiente su tre nodi diversi, Service Fabric riorganizzerà i servizi già presenti nel cluster per liberare spazio nei tre nodi necessari. Questa riorganizzazione è quasi sempre possibile, a meno che il cluster nel complesso non sia quasi interamente pieno.

## Capacità in buffering
Per semplificare la gestione della capacità complessiva del cluster, è stato anche aggiungo il concetto di buffer parzialmente riservato per la capacità specificata in ogni nodo. Questa opzione è facoltativa, ma consente agli utenti di riservare una parte della capacità complessiva del nodo, in modo che venga usata solo per il posizionamento dei servizi durante gli aggiornamenti e gli errori, ovvero nei casi in cui la capacità del cluster risulta ridotta per altri motivi. Attualmente il buffer viene specificato a livello globale per ogni metrica per tutti i nodi tramite ClusterManifest. Il valore scelto per la capacità riservata sarà una funzione delle risorse dei servizi che presentano più vincoli, oltre al numero di domini di errore e di aggiornamento disponibili nel cluster. In genere, un numero elevato di domini di errore e di aggiornamento consente di scegliere un numero ridotto per la capacità in buffer, perché ci si aspetta che una quantità minore del cluster risulti non disponibile durante aggiornamenti ed errori. Si noti che specificare la percentuale del buffer risulta utile solo se è stata specificata anche la capacità del nodo per una metrica.

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```
Le chiamate di creazione che hanno come risultato nuovi servizi hanno esito negativo quando il cluster esaurisce la capacità in buffer, in modo da assicurare che il cluster conservi un overhead di riserva sufficiente affinché gli aggiornamenti e gli errori non abbiano come risultato il superamento effettivo della capacità dei nodi. Resource Manager espone molte di queste informazioni tramite PowerShell e le API di query, consentendo all'utente di visualizzare le impostazioni sulla capacità in buffer, sulla capacità totale e sull'utilizzo attuale per ogni metrica specifica. Ecco un esempio dell'output:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2015 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2015 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## Passaggi successivi
- Per informazioni sull'architettura e sul flusso di informazioni in Cluster Resource Manager, vedere [questo articolo ](service-fabric-cluster-resource-manager-architecture.md)
- Definire la metrica di deframmentazione rappresenta un modo per consolidare il carico sui nodi anziché distribuirlo. Per informazioni su come configurare la deframmentazione, vedere [questo articolo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Partire dall'inizio e vedere l'[introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

<!---HONumber=AcomDC_0525_2016-->