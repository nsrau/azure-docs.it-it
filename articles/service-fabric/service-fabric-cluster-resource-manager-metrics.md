<properties
   pageTitle="Gestione delle metriche con Cluster Resource Manager di Azure Service Fabric"
   description="Informazioni su come configurare e usare le metriche in Service Fabric."
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
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Metrica
Le metriche sono il termine generico usato in Service Fabric per le risorse rilevanti per i servizi. In genere, una metrica è un qualsiasi elemento che si vuole gestire in termini di risorsa, per gestire le prestazioni dei servizi.

In tutti gli esempi precedenti si fa implicitamente riferimento alle metriche. La memoria, il disco, l'utilizzo della CPU sono tutti esempi di metriche. In questo caso si tratta di metriche fisiche, ovvero risorse che corrispondono a risorse fisiche sul nodo e che devono essere gestite. Le metriche possono anche essere metriche logiche, ad esempio "MyWorkQueueDepth", ovvero possono essere definite dall'applicazione e corrispondenti a un livello di utilizzo della risorsa. In questo caso, tuttavia, l'applicazione non è effettivamente consapevole della metrica o non sa come misurarla.

## Metriche predefinite
Si supponga di volere semplicemente iniziare e di non sapere quali risorse verranno utilizzate o addirittura quali risorse risulteranno rilevanti. Si procede quindi all'implementazione e alla creazione dei servizi senza specificare metriche. Questo approccio non presenta problemi. Le metriche verranno selezionate automaticamente. Le metriche predefinite usate attualmente se l'utente non specifica alcuna metrica sono PrimaryCount, ReplicaCount e Count, quest'ultima piuttosto vaga. La tabella seguente illustra il carico verificato per ogni metrica per impostazione predefinita:

| Metrica | Carico di istanza senza stato |	Carico secondario con stato |	Carico primario con stato |
|--------|--------------------------|-------------------------|-----------------------|
| PrimaryCount | 0 |	0 |	1 |
| ReplicaCount | 0 | 1 | 1 |
| Numero |	1 |	1 |	1 |

Le metriche predefinite consentono di ottenere una distribuzione abbastanza valida del lavoro per i carichi di lavoro di base. Nell'esempio seguente vengono creati un servizio con stato con tre partizioni e un set di repliche di destinazione con dimensioni pari a tre, oltre a un singolo servizio senza stato con tre istanze.

![Layout dei cluster con metriche predefinite][Image1]

Come si può notare nell'esempio:
-	Le repliche primarie per il servizio con stato non vengono impilate in un singolo nodo.
-	Le repliche per la stessa partizione non si trovano sullo stesso nodo.
-	Il numero totale di elementi primari e secondari è distribuito in modo ottimale nel cluster.
-	Il numero totale di oggetti servizio, con e senza stato, è allocato in modo uniforme in ogni nodo.

Si tratta di una situazione abbastanza valida.

Se però si esamina attentamente la situazione, ci si domanda quale sia la probabilità che tutti gli elementi primari dei diversi servizi presentino effettivamente lo stesso carico in un momento specifico. Ci si domanda anche quale sia la possibilità che il carico per un determinato servizio risulti costante nel tempo. Questa possibilità è effettivamente abbastanza bassa per qualsiasi carico di lavoro importante.

È realisticamente possibile procedere all'esecuzione con le metriche predefinite o almeno con alcune metriche personalizzate statiche, ma questo approccio comporta in genere un utilizzo del cluster inferiore al desiderato, perché la creazione di report non è adattiva. Nei casi peggiore, è possibile che si ottengano nodi sovrapianificati, che provocano problemi di prestazioni. Le metriche personalizzate e i report sui carichi dinamici consentono di ottenere risultati migliori. Le sezioni seguenti illustrano quindi le metriche personalizzate e i carichi dinamici.

## Metriche personalizzate
Come accennato in precedenza, è possibile usare metriche fisiche e logiche e definire metriche personalizzate. L'esecuzione di queste operazioni è molto semplice. È sufficiente configurare la metrica e il carico iniziale predefinito durante la creazione del servizio. Qualsiasi set di metriche e di valori predefiniti può essere configurato in base a singole istanze del servizio durante la creazione del servizio.

Si noti che quando si inizia a definire le metriche personalizzate è necessario aggiungere esplicitamente le metriche predefinite, se si vuole usarle per il bilanciamento del servizio. È infatti necessario comprendere la relazione tra le metriche predefinite e le metriche personalizzate. È ad esempio possibile che si attribuisca maggiore importanza all'utilizzo della memoria rispetto alla distribuzione primaria o che si vogliano combinare alcune metriche con le metriche predefinite.

Si supponga che si voglia configurare un servizio che fornisce report su una metrica denominata "Memory", in aggiunta alle metriche predefinite. Nel caso della memoria, si supponga di avere eseguito alcune misurazioni di base e che sia stato rilevato che normalmente per una replica primaria del servizio sono necessari 20 MB di memoria, mentre per le repliche secondarie dello stesso servizio saranno necessari 5 MB. Si è consapevoli del fatto che la memoria è la metrica più importante in termini di gestione delle prestazioni del servizio specifico, ma si vuole comunque che le repliche primarie siano bilanciate, in modo che la perdita di nodi o domini di errore non renda inattivo un numero troppo elevato di repliche primarie. Le impostazioni predefinite risultano appropriate per tutti gli altri aspetti.

È necessario eseguire queste operazioni:

Codice:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

ServiceLoadMetricDescription primaryCountMetric = new ServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

ServiceLoadMetricDescription replicaCountMetric = new ServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

ServiceLoadMetricDescription totalCountMetric = new ServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Si noti che se si vogliono usare solo le metriche predefinite, non è necessario modificare in alcun modo la raccolta di metriche.

È stato illustrato come definire le metriche personalizzate, quindi è ora possibile esaminare le diverse proprietà possibili per le metriche. Verrà esaminato in modo dettagliato il significato delle proprietà. Una metrica può attualmente avere quattro proprietà diverse:

-	Nome della metrica: corrisponde al nome della metrica. Si tratta di un identificatore univoco per la metrica nel cluster dal punto di vista di Resource Manager.
-	PrimaryDefaultLoad: quantità predefinita di carico che il servizio eserciterà per la metrica come replica primaria.
-	SecondaryDefaultLoad: quantità predefinita di carico che il servizio eserciterà per la metrica come replica secondaria.
-	Peso: indica la rilevanza della metrica rispetto alle altre metriche configurate per il servizio.

## Load
Il carico è un concetto generale che indica la quantità di una metrica specifica utilizzata da un'istanza del servizio o da una replica in un nodo specifico.

### Carico predefinito
Il carico predefinito corrisponde alla quantità di carico che Resource Manager deve presupporre venga utilizzata da ogni istanza del servizio o replica del servizio, fino a quando non riceve eventuali aggiornamenti dalle istanze del servizio o dalle repliche effettive. Per i servizi più semplici, si tratta di una definizione statica che non viene mai aggiornata dinamicamente e che verrà quindi usata per la durata del servizio. Questo approccio è ottimale per la pianificazione della capacità semplice, perché corrisponde alle operazioni consuete, ovvero dedicare determinate risorse a determinati carichi di lavoro. Il vantaggio è però costituito dal fatto che almeno ora si adotta un approccio basato su microservizi, in cui le risorse non sono effettivamente assegnate in modo statico a carichi di lavoro specifici e gli utenti non vengono coinvolti nel processo decisionale.

Si consente ai servizi con stato di specificare il carico predefinito per le repliche primarie e secondarie. Realisticamente, per molti servizi questi valori sono diversi a causa dei diversi carichi di lavoro eseguiti dalle repliche primarie e dalle repliche secondarie. Poiché le repliche primarie gestiscono in genere operazioni di lettura e scrittura, oltre alla maggior parte delle operazioni di calcolo, il carico predefinito per una replica primaria è maggiore rispetto a quello delle repliche secondarie.

Si supponga di avere eseguito il servizio per un certo periodo di tempo e che si sia notato che alcune istanze o repliche del servizio utilizzano molte più risorse rispetto alle altre o che il rispettivo utilizzo varia nel tempo, ad esempio perché sono associate a un cliente specifico o corrispondono semplicemente a carichi di lavoro che presentano variazioni nel corso della giornata, come traffico di messaggistica o transazioni azionarie. Ad ogni modo, si nota che non è disponibile alcun "numero singolo" utilizzabile per il carico che non presenti differenze significative rispetto a quanto previsto. Si nota anche che le differenze rispetto alle stime iniziali provocano in Service Fabric un'allocazione eccessiva o insufficiente delle risorse al servizio e che quindi alcuni nodi sono utilizzati in modo eccessivo o insufficiente. Cosa fare? È ad esempio possibile fare in modo che il servizio segnali in tempo reale il carico.

## Carico dinamico

I report relativi al carico dinamico consentono alle repliche o alle istanze di modificare la rispettiva allocazione o il rispettivo utilizzo segnalato delle metriche nel cluster nel corso del proprio ciclo di vita. Una replica o un'istanza del servizio che risultava inattiva e non eseguiva alcuna operazione segnalerà in genere un utilizzo ridotto delle risorse, mentre le repliche o le istanza occupate segnalano un utilizzo superiore. Questo livello generale di varianza nel cluster consente di riorganizzare in tempo reale le repliche e le istanze del servizio nel cluster, in modo da assicurare che i servizi e le istanze ottengano le risorse necessarie, affinché i servizi occupati possano recuperare risorse da altre repliche o istanze attualmente inattive o meno occupate. La segnalazione in tempo reale del carico può essere eseguita mediante il metodo ReportLoad, disponibile in ServicePartition, disponibile come proprietà nel servizio StatefulService di base. Il codice nel servizio avrà un aspetto analogo al seguente:

Codice:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("Foo", 42) });
```

Le repliche o le istanze del servizio possono segnalare il carico solo per le metriche disponibili per l'uso in base alla rispettiva configurazione. L'elenco di metriche viene definito in fase di creazione di ogni servizio. Se una replica o un'istanza del servizio prova a segnalare il carico per una metrica non ancora disponibile per l'uso in base alla rispettiva configurazione, Service Fabric registra il report ma lo ignora. Non verrà quindi usato durante il calcolo o la creazione di report sullo stato del cluster. Questo approccio consente maggiori sperimentazioni. Il codice può misurare e segnalare tutti gli aspetti configurati e l'operatore può configurare, modificare e aggiornare le regole di bilanciamento delle risorse per il servizio in tempo reale, senza dover modificare il codice. È ad esempio possibile disabilitare una metrica con un report anomalo, riconfigurare i pesi in base al comportamento oppure abilitare una nuova metrica solo dopo che il codice è già stato distribuito e convalidato.

Specificare un carico predefinito per un servizio che crea report in modo dinamico per il carico offre molti vantaggi. In questo caso il carico predefinito viene usato come una stima, fino a quando i report effettivi non risultano disponibili dalla replica o dall'istanza del servizio. Questo approccio è ottimale perché fornisce a Resource Manager alcuni elementi su cui lavorare durante il posizionamento della replica o dell'istanza in fase di creazione. Il carico predefinito viene usato come stima iniziale e ciò consente a Resource Manager di posizionare in modo ottimale fin da subito le istanze o le repliche del servizio. Se non venisse fornita alcuna informazione, il posizionamento sarebbe effettivamente casuale ed è molto probabile che sarebbe necessario spostare elementi immediatamente dopo la ricezione dei report effettivi sul carico.

Partendo dall'esempio precedente, è possibile verificare le conseguenze dell'aggiunta di carico personalizzato e quindi dell'aggiornamento dinamico di un servizio dopo la creazione. In questo esempio verrà usata la metrica "Memory" e si presuppone di avere creato inizialmente il servizio con stato con il comando seguente:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Questa sintassi è stata illustrata in precedenza (MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad), ma più avanti verrà esaminato in modo dettagliato il significato del valore specifico per Weight.

Un layout di cluster può avere un aspetto analogo al seguente:

![Cluster bilanciato con le metriche predefinite e personalizzate][Image2]

Occorre notare alcuni aspetti:
-	Poiché le repliche o le istanze usano il carico predefinito del servizio fino a quando non viene segnalato il rispettivo carico, si sa che le repliche all'interno della partizione 1 del servizio con stato non hanno segnalato alcun carico specifico.
-	Le repliche secondarie in una partizione possono avere un carico specifico.
-	Le metriche sono complessivamente buone, con una differenza tra il carico massimo e minimo su un nodo, per la metrica più rilevante Memory, pari solamente a un fattore di 1,75. Il nodo con il carico maggiore per la memoria è N3, quello con il carico minore è N2 e 28/16 = 1,75. La configurazione è abbastanza bilanciata.

I concetti seguenti devono essere ancora spiegati:

-	In che modo è stato stabilito se un rapporto di 1,75 è ragionevole? Come si capisce se questa configurazione è ottimale o se occorre modificarla?
-	Quando viene applicato il bilanciamento?
-	Cosa significa che il peso di Memory è "High"?

## Pesi metrici
I pesi metrici consentono a due servizi diversi di segnalare le stesse metriche ma di visualizzare in modo diverso l'importanza del bilanciamento della metrica specifica. Ad esempio, si prendano in considerazione un motore analitico in memoria e un database persistente. È probabile che entrambi siano interessati alla metrica "Memory", ma probabilmente il servizio in memoria non sarà interessato alla metrica "Disk": è possibile che ne utilizzi una parte, ma non si tratta di una metrica essenziale per le prestazioni del servizio. La possibilità di tenere traccia delle stesse metriche in diversi servizi offre molti vantaggi, perché consente a Resource Manager di tenere traccia dell'utilizzo effettivo nel cluster, di assicurare che non venga superata la capacità dei nodi e così via.

I pesi delle metriche consentono a Resource Manager di prendere decisioni concrete in merito a come bilanciare il cluster quando non è disponibile una risposta perfetta, ovvero in molti casi. Le metriche possono avere quattro livelli di peso diversi: Zero, Low, Medium e High. Una metrica con peso pari a Zero non ha alcun impatto sulle considerazioni relative al bilanciamento o meno degli elementi, ma il rispettivo carico ha comunque un impatto su aspetti quali la misurazione della capacità.

L'impatto effettivo di diversi pesi per le metriche nel cluster consiste nel fatto che porta a disposizioni materialmente diverse dei servizi, perché Resource Manager è stato informato, in modo aggregato, che alcune metriche sono più importanti di altre e che quindi devono essere preferite a livello di bilanciamento in caso di conflitto con altre metriche di priorità inferiore.

Ecco un semplice esempio di alcuni report sul carico e del modo in cui diversi pesi per le metriche possono comportare diverse allocazioni nel cluster. In questo esempio si può notare che la modifica del peso relativo delle metriche fa in modo che Resource Manager preferisca alcune soluzioni, creando diverse disposizioni dei servizi.

![Esempio di peso delle metriche e del rispettivo impatto sulle soluzioni di bilanciamento][Image3]

In questo esempio sono presenti quattro servizi diversi, ognuno dei quali segnala valori diversi per due metriche diverse, A e B. In un caso definito da tutti i servizi, MetricA è la più importante (Weight = High) e MetricB è relativamente poco importante (Weight = Low). Resource Manager di Service Fabric posiziona effettivamente i servizi in modo che il bilanciamento di MetricA sia migliore (deviazione minore) rispetto a MetricB. Nel secondo caso vengono invertiti i pesi delle metriche e si può notare che Resource Manager probabilmente scambierà i servizi A e B, in modo da ottenere un'allocazione in cui il bilanciamento di MetricB è migliore rispetto a MetricA.

### Pesi metrici globali
Se ServiceA definisce MetricA come metrica più importante e ServiceB non è interessato a questa metrica, occorre calcolare il peso effettivo che viene effettivamente usato da Resource Manager.

Sono in effetti disponibili due pesi di cui tenere traccia per ogni metrica, ovvero il peso del servizio stesso definito e il peso medio globale in tutti i servizi interessati alla metrica. Entrambi i pesi vengono usati per il calcolo dei punteggio delle soluzioni generate, perché è importante assicurarsi che un servizio sia bilanciato rispetto alle relative priorità e che anche il cluster sia allocato complessivamente in modo corretto.

Se non si presta attenzione al bilanciamento globale e locale, è possibile che si creino soluzioni bilanciate a livello globale, ma che hanno come risultato un bilanciamento e un'allocazione di risorse non appropriati per i singoli servizi. Nell'esempio seguente verranno esaminate le metriche predefinite configurate per un servizio con stato, ovvero PrimaryCount, ReplicaCount e TotalCount, e vengono illustrate le conseguenze di un'attenzione esclusiva al bilanciamento globale:

![Impatto di una soluzione solo globale][Image4]

Nel primo esempio, in cui è stato esaminato solo il bilanciamento globale, il cluster nel suo complesso è effettivamente bilanciato: tutti i nodi hanno lo stesso numero di repliche primarie e lo stesso numero totale di repliche. Tutti gli elementi sono bilanciati. Se tuttavia si esamina l'impatto effettivo di questa allocazione, si notano alcuni problemi. La perdita di un nodo influisce in modo sproporzionato su un carico di lavoro specifico, perché rende inattive tutte le repliche primarie. Si prenda ad esempio in considerazione la perdita del primo nodo. Se questa situazione si verificasse, le tre repliche primarie per le tre diverse partizioni del servizio Circle andrebbero perse contemporaneamente. Le partizioni degli altri due servizi (Triangle e Hexagon), invece, perderebbero una replica e ciò non provocherebbe alcun problema per il servizio, ad eccezione della necessità di ripristinare la replica inattiva.

Nel secondo esempio le repliche sono state distribuite in base al bilanciamento globale e per i singoli servizi. Quando si calcola il punteggio della soluzione, la maggior parte del peso viene assegnata alla soluzione globale, ma una parte configurabile viene assegnata in modo da assicurare che anche i servizi siano bilanciati internamente per quanto possibile. Se quindi si perdesse il primo nodo, si noterebbe che la perdita delle repliche primarie e secondarie viene distribuita in tutte le partizioni di tutti i servizi e che l'impatto per ogni elemento è uguale.

Prendendo in considerazione i pesi delle metriche, il bilanciamento globale viene calcolato in base alla media dei pesi delle metriche. Un servizio viene bilanciato rispetto ai pesi definiti specifici per le metriche.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
- [Informazioni sulle metriche di deframmentazione](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- [Informazioni sul bilanciamento del carico nel cluster di Cluster Resource Manager](service-fabric-cluster-resource-manager-balancing.md)
- [Introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- [Informazioni su costo dello spostamento dei servizi](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0309_2016-->