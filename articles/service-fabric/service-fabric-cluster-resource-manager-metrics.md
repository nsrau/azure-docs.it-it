---
title: Gestione delle metriche con Cluster Resource Manager di Azure Service Fabric | Microsoft Docs
description: Informazioni su come configurare e usare le metriche in Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche
Le metriche sono il termine generico usato in Service Fabric per le risorse rilevanti per i servizi e sono fornite dai nodi nel cluster. In genere, una metrica è un qualsiasi elemento che si vuole controllare per gestire le prestazioni dei servizi.

Alcuni esempi di metriche sono Memory, Disk e CPU. In questo caso si tratta di metriche fisiche, ovvero risorse che corrispondono a risorse fisiche sul nodo e che devono essere gestite. Le metriche possono essere, e spesso sono, anche metriche logiche, ad esempio "MyWorkQueueDepth", ovvero possono essere definite dall'applicazione e corrispondenti a un livello di utilizzo della risorsa. In questo caso, tuttavia, l'applicazione non è effettivamente consapevole della metrica o non sa come misurarla. La maggior parte delle metriche usate sono metriche logiche. I motivi sono molti, ma il più comune è che oggi molti dei clienti scrivono i servizi in codice gestito e dall'interno di un'istanza di servizio senza stato o un oggetto di replica di servizio con stato è effettivamente molto difficile misurare e segnalare l'utilizzo delle risorse fisiche effettive. La complessità della generazione di report sulle metriche è un altro motivo per il quale sono disponibili alcune metriche predefinite.

## Metriche predefinite
Si supponga di volere semplicemente iniziare e di non sapere quali risorse verranno utilizzate o addirittura quali risorse risulteranno rilevanti. Si procede quindi all'implementazione e alla creazione dei servizi senza specificare metriche. Questo approccio non presenta problemi. Le metriche verranno selezionate automaticamente. Le metriche predefinite usate attualmente se l'utente non specifica alcuna metrica sono PrimaryCount, ReplicaCount e Count, quest'ultima piuttosto vaga. La tabella seguente descrive il carico associato per ogni metrica a ogni oggetto di servizio:

| Metrica | Carico di istanza senza stato | Carico secondario con stato | Carico primario con stato |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Numero |1 |1 |1 |

Le metriche predefinite consentono di ottenere una distribuzione abbastanza valida del lavoro per i carichi di lavoro di base. Nell'esempio seguente vengono creati un servizio con stato con tre partizioni e un set di repliche di destinazione con dimensioni pari a tre, oltre a un singolo servizio senza stato con tre istanze.

![Layout dei cluster con metriche predefinite][Image1]

Come si può notare nell'esempio:

* Le repliche primarie per il servizio con stato non vengono impilate in un singolo nodo.
* Le repliche per la stessa partizione non si trovano sullo stesso nodo.
* Il numero totale di elementi primari e secondari è distribuito in modo ottimale nel cluster.
* Il numero totale di oggetti servizio, con e senza stato, è allocato in modo uniforme in ogni nodo.

Si tratta di una situazione abbastanza valida.

È particolarmente efficace fino a quando non si prende in considerazione l'effettiva probabilità che lo schema di partizionamento scelto produca un utilizzo perfettamente bilanciato da parte di tutte le partizioni nel tempo. Qual è poi la probabilità che il carico per un determinato servizio risulti costante nel tempo o invariato? Le probabilità di un'equivalenza di tutte le repliche sono effettivamente piuttosto basse per qualunque carico di lavoro significativo, quindi è opportuno prendere in considerazione metriche personalizzate se si vuole ottenere il massimo dal cluster.

È realisticamente possibile procedere all'esecuzione con le sole metriche predefinite, ma questo approccio comporta in genere un uso del cluster inferiore al desiderato, perché la creazione di report non è adattiva e presume che tutto sia equivalente. Nei casi peggiori, è possibile che si ottengano nodi sovrapianificati, che determinano problemi di prestazioni. Le metriche personalizzate e i report sui carichi dinamici consentono di ottenere risultati migliori, come si vedrà in seguito.

## Metriche personalizzate
Come accennato in precedenza è possibile usare metriche fisiche e logiche e definire metriche personalizzate. L'installazione è riuscita. In che modo? È piuttosto semplice. È sufficiente configurare la metrica e il carico iniziale predefinito durante la creazione del servizio. Qualsiasi set di metriche e di valori predefiniti che rappresenti l'utilizzo previsto del servizio può essere configurato in base a singole istanze del servizio durante la creazione del servizio.

Si noti che quando si inizia a definire le metriche personalizzate è necessario aggiungere esplicitamente le metriche predefinite, se si vuole usarle per il bilanciamento del servizio. È infatti necessario comprendere la relazione tra le metriche predefinite e le metriche personalizzate. È ad esempio possibile che si attribuisca maggiore importanza all'utilizzo della memoria o alla profondità della coda di lavoro rispetto alla distribuzione primaria.

Si supponga che si voglia configurare un servizio che fornisce report su una metrica denominata "Memory", in aggiunta alle metriche predefinite. Nel caso della memoria, si supponga di avere eseguito alcune misurazioni di base e che sia stato rilevato che normalmente per una replica primaria del servizio sono necessari 20 MB di memoria, mentre per le repliche secondarie dello stesso servizio saranno necessari 5 MB. Si è consapevoli del fatto che la memoria è la metrica più importante in termini di gestione delle prestazioni del servizio specifico, ma si vuole comunque che le repliche primarie siano bilanciate, in modo che la perdita di nodi o domini di errore non renda inattivo un numero troppo elevato di repliche primarie. Le impostazioni predefinite risultano appropriate per tutti gli altri aspetti.

È necessario eseguire queste operazioni:

Codice:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription memoryMetric = new StatefulServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
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

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Promemoria: se si vogliono usare solo le metriche predefinite, non è necessario modificare in alcun modo la raccolta di metriche né eseguire operazioni speciali durante la creazione del servizio.

È stato illustrato come definire le metriche personalizzate, quindi è ora possibile esaminare le diverse proprietà possibili per le metriche. Verrà esaminato in modo dettagliato il significato delle proprietà. Una metrica può attualmente avere quattro proprietà diverse:

* Nome della metrica: corrisponde al nome della metrica. Si tratta di un identificatore univoco per la metrica nel cluster dal punto di vista di Resource Manager.
* Default Load: il carico predefinito è rappresentato in modo diverso a seconda che il servizio sia con o senza stato.
  * Per i servizi senza stato ogni metrica dispone semplicemente di una singola proprietà denominata Default Load
  * Per i servizi con stato si definisce
    * PrimaryDefaultLoad: carico predefinito che il servizio eserciterà per la metrica come replica primaria
    * SecondaryDefaultLoad: carico predefinito che il servizio eserciterà per la metrica come replica secondaria
* Peso: indica la rilevanza della metrica rispetto alle altre metriche configurate per il servizio.

## chiudi
Il carico è un concetto generale che indica la quantità di una metrica specifica utilizzata da un'istanza del servizio o da una replica in un nodo specifico.

## Carico predefinito
Il carico predefinito corrisponde al carico che Cluster Resource Manager deve presupporre venga usato da ogni istanza o replica del servizio, fino a quando non riceve eventuali aggiornamenti dalle effettive istanze o dalle repliche del servizio. Per i servizi più semplici, si tratta di una definizione statica che non viene mai aggiornata dinamicamente e che verrà quindi usata per la durata del servizio. Questo approccio è ottimale per la pianificazione della capacità semplice, perché corrisponde alle operazioni consuete, ovvero dedicare determinate risorse a determinati carichi di lavoro. Il vantaggio è però costituito dal fatto che almeno ora si adotta un approccio basato su microservizi, in cui le risorse non sono effettivamente assegnate in modo statico a carichi di lavoro specifici e gli utenti non vengono coinvolti nel processo decisionale.

Si consente ai servizi con stato di specificare il carico predefinito per le repliche primarie e secondarie. Realisticamente, per molti servizi questi valori sono diversi a causa dei diversi carichi di lavoro eseguiti dalle repliche primarie e dalle repliche secondarie. Poiché le repliche primarie gestiscono in genere operazioni di lettura e scrittura, oltre alla maggior parte delle operazioni di calcolo, il carico predefinito per una replica primaria è maggiore rispetto a quello delle repliche secondarie.

Si supponga di avere eseguito il servizio per un certo periodo di tempo e che si sia notato che alcune istanze o repliche del servizio usano molte più risorse rispetto alle altre o che il rispettivo utilizzo varia nel tempo, ad esempio perché sono associate a un cliente specifico o corrispondono semplicemente a carichi di lavoro che presentano variazioni nel corso della giornata, come traffico di messaggistica, telefonate o transazioni azionarie. A ogni modo, si nota che non è disponibile alcun "numero singolo" utilizzabile per il carico che non presenti differenze significative rispetto a quanto previsto per almeno una parte del tempo. Si nota anche che le differenze rispetto alle stime iniziali provocano in Cluster Resource Manager un'allocazione eccessiva o insufficiente delle risorse al servizio e che quindi alcuni nodi sono sovra o sottoutilizzati.

Cosa fare? È ad esempio possibile fare in modo che il servizio segnali in tempo reale il carico.

## Carico dinamico
I report relativi al carico dinamico consentono alle repliche o alle istanze di modificare la rispettiva allocazione o il rispettivo uso segnalato delle metriche nel cluster nel corso del proprio ciclo di vita. Una replica o un'istanza del servizio che risultava inattiva e non eseguiva alcuna operazione segnalerà in genere un utilizzo ridotto di una determinata metrica, mentre le repliche o le istanze occupate segnaleranno un uso superiore. Questo livello generale di varianza nel cluster consente di riorganizzare in tempo reale le repliche e le istanze del servizio nel cluster, in modo da assicurare che ottengano le risorse necessarie, affinché i servizi occupati possano recuperare risorse da altre repliche o istanze attualmente inattive o meno occupate. La segnalazione in tempo reale del carico può essere eseguita mediante il metodo ReportLoad, in ServicePartition, disponibile come proprietà nella classe StatefulService o StatelessService di base tramite il modello di programmazione Reliable Services. Il codice nel servizio avrà un aspetto analogo al seguente:

Codice:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("metric1", 42) });
```

Le repliche o le istanze del servizio possono segnalare il carico solo per le metriche disponibili per l'uso in base alla rispettiva configurazione. L'elenco di metriche viene definito in fase di creazione di ogni servizio e può essere aggiornato in un secondo momento. Se una replica o un'istanza del servizio prova a segnalare il carico per una metrica non ancora disponibile per l'uso in base alla rispettiva configurazione, Service Fabric registra il report ma lo ignora. Non verrà quindi usato durante il calcolo o la creazione di report sullo stato del cluster. Questo approccio consente maggiori sperimentazioni. Il codice può misurare e segnalare tutti gli aspetti configurati e l'operatore può configurare, modificare e aggiornare le regole di bilanciamento delle risorse per il servizio in tempo reale, senza dover modificare il codice. È ad esempio possibile disabilitare una metrica con un report anomalo, riconfigurare i pesi in base al comportamento oppure abilitare una nuova metrica solo dopo che il codice è già stato distribuito e convalidato tramite altri meccanismi.

## Combinazione di valori di carico predefiniti e report sul carico dinamico
Specificare un carico predefinito per un servizio che crea report in modo dinamico per il carico offre molti vantaggi. In questo caso il carico predefinito viene usato come una stima, fino a quando i report effettivi non risultano disponibili dalla replica o dall'istanza del servizio. Ciò è ideale perché permette a Cluster Resource Manager di usare la stima del carico predefinito per il corretto posizionamento delle repliche o delle istanze dei servizi fin dall'inizio. In mancanza di informazioni sul carico predefinito, il posizionamento dei servizi è effettivamente casuale in fase di creazione e, in caso di successiva modifica dei carichi, Cluster Resource Manager dovrà quasi certamente eseguire degli spostamenti.

Partendo dall'esempio precedente, è possibile verificare le conseguenze dell'aggiunta di carico personalizzato e quindi dell'aggiornamento dinamico di un servizio dopo la creazione. In questo esempio verrà usata la metrica "Memory" e si presuppone di avere creato inizialmente il servizio con stato con il comando seguente:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Questa sintassi è stata illustrata in precedenza (MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad), ma più avanti verrà esaminato in modo dettagliato il significato del valore specifico per Weight.

Un layout di cluster può avere un aspetto analogo al seguente:

![Cluster bilanciato con le metriche predefinite e personalizzate][Image2]

Occorre notare alcuni aspetti:

* Poiché le repliche o le istanze usano il carico predefinito del servizio fino a quando non viene segnalato il rispettivo carico, si sa che le repliche all'interno della partizione 1 del servizio con stato non hanno segnalato alcun carico specifico.
* Le repliche secondarie in una partizione possono avere un carico specifico.
* Le metriche sono complessivamente buone, con una differenza tra il carico massimo e minimo su un nodo, per la metrica più rilevante Memory, pari solamente a un fattore di 1,75. Il nodo con il carico maggiore per la memoria è N3, quello con il carico minore è N2 e 28/16 = 1,75. La configurazione è abbastanza bilanciata.

I concetti seguenti devono essere ancora spiegati:

* In che modo è stato stabilito se un rapporto di 1,75 è ragionevole? Come si capisce se questa configurazione è ottimale o se occorre modificarla?
* Quando viene applicato il bilanciamento?
* Cosa significa che il peso di Memory è "High"?

## Pesi metrici
I pesi metrici consentono a due servizi diversi di segnalare le stesse metriche ma di visualizzare in modo diverso l'importanza del bilanciamento della metrica specifica. Ad esempio, si prendano in considerazione un motore analitico in memoria e un database persistente. È probabile che entrambi siano interessati alla metrica "Memory", ma probabilmente il servizio in memoria non sarà interessato alla metrica "Disk": è possibile che ne usi una parte, ma non si tratta di una metrica essenziale per le prestazioni del servizio e probabilmente non vengono creati report correlati. La possibilità di tenere traccia delle stesse metriche in diversi servizi offre molti vantaggi, perché consente a Cluster Resource Manager di rilevare l'utilizzo effettivo nel cluster, di assicurare che non venga superata la capacità dei nodi e così via.

I pesi delle metriche consentono inoltre a Cluster Resource Manager di prendere decisioni in merito a come bilanciare il cluster quando non è disponibile una risposta perfetta, ovvero in molti casi. Le metriche possono avere quattro livelli di peso diversi: Zero, Low, Medium e High. Una metrica con peso pari a Zero non ha alcun impatto sulle considerazioni relative al bilanciamento o meno degli elementi, ma il rispettivo carico ha comunque un impatto su aspetti quali la misurazione della capacità.

L'impatto effettivo di diversi pesi per le metriche nel cluster consiste nel fatto che porta a disposizioni diverse dei servizi, perché Cluster Resource Manager è stato informato, in modo aggregato, che alcune metriche sono più importanti di altre. Essendone quindi a conoscenza, in presenza di un conflitto tra metriche di pesi diversi, Cluster Resource Manager può scegliere soluzioni che bilancino al meglio le metriche con livello di peso High.

Ecco un semplice esempio di alcuni report sul carico e del modo in cui diversi pesi per le metriche possono comportare diverse allocazioni nel cluster. In questo esempio si può notare che la modifica del peso relativo delle metriche fa in modo che Resource Manager preferisca alcune soluzioni, creando diverse disposizioni dei servizi.

![Esempio di peso delle metriche e del rispettivo impatto sulle soluzioni di bilanciamento][Image3]

In questo esempio sono presenti quattro servizi diversi, ognuno dei quali segnala valori diversi per due metriche diverse, A e B. In un caso definito da tutti i servizi, MetricA è la più importante (Weight = High) e MetricB è relativamente poco importante (Weight = Low). Cluster Resource Manager posiziona i servizi in modo che il bilanciamento di MetricA sia migliore (deviazione standard minore) rispetto a MetricB. Nel secondo caso vengono invertiti i pesi delle metriche e si può notare che Cluster Resource Manager probabilmente scambierà i servizi A e B, in modo da ottenere un'allocazione in cui il bilanciamento di MetricB è migliore rispetto a MetricA.

### Pesi metrici globali
Se ServiceA definisce MetricA come metrica più importante e ServiceB non è interessato a questa metrica, occorre calcolare il peso reale effettivamente usato.

Sono in effetti disponibili due pesi di cui tenere traccia per ogni metrica, ovvero il peso del servizio stesso definito e il peso medio globale in tutti i servizi interessati alla metrica. Entrambi i pesi vengono usati per il calcolo dei punteggio delle soluzioni generate, perché è importante assicurarsi che un servizio sia bilanciato rispetto alle relative priorità e che anche il cluster sia allocato complessivamente in modo corretto.

Se non si presta attenzione al bilanciamento globale e locale, è possibile che si creino soluzioni bilanciate a livello globale, ma che hanno come risultato un bilanciamento e un'allocazione di risorse non appropriati per i singoli servizi. Nell'esempio seguente vengono esaminate le metriche predefinite configurate per un servizio con stato, ovvero PrimaryCount, ReplicaCount e Count, e vengono illustrate le conseguenze di un'attenzione esclusiva al bilanciamento globale:

![Impatto di una soluzione solo globale][Image4]

Nel primo esempio, in cui è stato esaminato solo il bilanciamento globale, il cluster nel suo complesso è effettivamente bilanciato: tutti i nodi hanno lo stesso numero di repliche primarie e di repliche totali. Se tuttavia si esamina l'impatto effettivo di questa allocazione, si notano alcuni problemi. La perdita di un nodo influisce in modo sproporzionato su un carico di lavoro specifico, perché rende inattive tutte le repliche primarie. Si prenda ad esempio in considerazione la perdita del primo nodo. Se questa situazione si verificasse, le tre repliche primarie per le tre diverse partizioni del servizio Circle andrebbero perse contemporaneamente. Le partizioni degli altri due servizi (Triangle e Hexagon), invece, perderebbero una replica e ciò non provocherebbe alcun problema per il servizio, ad eccezione della necessità di ripristinare la replica inattiva.

Nel secondo esempio le repliche sono state distribuite in base al bilanciamento globale e per i singoli servizi. Quando si calcola il punteggio della soluzione, la maggior parte del peso viene assegnata alla soluzione globale, ma una parte configurabile viene assegnata in modo da assicurare che anche i servizi siano bilanciati internamente per quanto possibile. Se quindi si perdesse il primo nodo, si noterebbe che la perdita delle repliche primarie e secondarie viene distribuita in tutte le partizioni di tutti i servizi e che l'impatto per ogni elemento è uguale.

Prendendo in considerazione i pesi delle metriche, il bilanciamento globale viene calcolato in base alla media dei pesi metrici configurati per ogni servizio. Un servizio viene bilanciato rispetto ai pesi definiti specifici per le metriche.

## Passaggi successivi
* Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili per Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
* Definire la metrica di deframmentazione rappresenta un modo per consolidare il carico sui nodi anziché distribuirlo. Per informazioni su come configurare la deframmentazione, vedere [questo articolo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
* Partire dall'inizio e vedere l'[introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Il costo dello spostamento è un modo per segnalare a Cluster Resource Manager che alcuni servizi sono più costosi da spostare rispetto ad altri. Per altre informazioni sui costi di spostamento, vedere [questo articolo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0824_2016-->