---
title: Gestire il carico dei microservizi di Azure con le metriche | Documentazione Microsoft
description: Informazioni su come configurare e usare le metriche in Service Fabric per gestire il consumo delle risorse dei servizi.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c0e5418574920495bf277f4127b97ff5b07b56ef


---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche
Le metriche sono il termine generico usato in Service Fabric per le risorse rilevanti per i servizi e sono fornite dai nodi nel cluster. In generale, una metrica è un elemento che si vuole controllare per gestire le prestazioni dei servizi.

Sono metriche, ad esempio, l'utilizzo della CPU, della memoria e dei dischi. In questo caso si tratta di metriche fisiche, ovvero risorse che corrispondono a risorse fisiche sul nodo e che devono essere gestite. Le metriche possono anche essere logiche e in genere sono di questo tipo. Sono metriche logiche, ad esempio, "MyWorkQueueDepth", "MessagesToProcess" e "TotalRecords". Le metriche logiche sono definite dall'applicazione e corrispondono al consumo di qualche risorsa fisica, che l'applicazione tuttavia non è realmente in grado di misurare. È una situazione comune. La maggior parte delle metriche usate sono metriche logiche. Ciò è in genere dovuto al fatto che molti servizi sono attualmente scritti in codice gestito. Di conseguenza, eseguire la misurazione e la creazione di report del consumo di risorse fisiche di un singolo oggetto di servizio da un processo host può essere difficile. La complessità della misurazione e della creazione di report delle metriche è anche il motivo per cui vengono fornite alcune metriche predefinite.

## <a name="default-metrics"></a>Metriche predefinite
Si supponga di non sapere inizialmente quali risorse verranno utilizzate o addirittura quali risulteranno importanti. Si procede quindi all'implementazione e alla creazione dei servizi senza specificare metriche. Questo approccio non presenta problemi. Cluster Resource Manager di Service Fabric seleziona automaticamente alcune semplici metriche. Le metriche predefinite attualmente usate se l'utente non ne specifica alcuna sono PrimaryCount, ReplicaCount e Count. La tabella seguente illustra il carico associato per ogni metrica a ogni oggetto di servizio:

| Metrica | Carico di istanza senza stato | Carico secondario con stato | Carico primario con stato |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Numero |1 |1 |1 |

Le metriche predefinite consentono di ottenere una distribuzione soddisfacente del lavoro per carichi di lavoro di base. L'esempio seguente illustra che cosa accade quando si creano due servizi. Il primo è un servizio con stato con tre partizioni e dimensioni del set di repliche di destinazione pari a tre. Il secondo è un servizio senza stato con una partizione e numero di istanze pari a tre:

<center>
![Layout dei cluster con metriche predefinite][Image1]
</center>

Il risultato è il seguente:

* Le repliche primarie per il servizio con stato non vengono impilate in un singolo nodo.
* Le repliche per la stessa partizione non si trovano sullo stesso nodo.
* Il numero totale di elementi primari e secondari è distribuito nel cluster.
* Il numero totale di oggetti di servizio è allocato in modo uniforme in ogni nodo.

Ottimo.

Questo approccio è efficace fino a quando non si eseguono numeri elevati di carichi di lavoro reali e non si prendono in considerazione le effettive probabilità che lo schema di partizionamento selezionato produca un utilizzo perfettamente uniforme in tutte le partizioni e che il carico per un determinato servizio sia costante nel tempo o resti invariato.

Sarebbe realisticamente possibile procedere all'esecuzione con le sole metriche predefinite, ma questo approccio comporta in genere un utilizzo del cluster inferiore a quello desiderato. Ciò è dovuto al fatto che la creazione di report delle metriche predefinite non è adattiva e presuppone che tutti gli elementi siano equivalenti. Nel peggiore dei casi, l'uso delle sole metriche predefinite può determinare anche la sovrapianificazione dei nodi e di conseguenza problemi di prestazioni. Le metriche personalizzate e i report sui carichi dinamici consentono di ottenere risultati migliori, come si vedrà in seguito. Per qualsiasi carico di lavoro significativo, le probabilità che tutti i servizi siano per sempre equivalenti sono basse. Se si è interessati a sfruttare al meglio il cluster ed evitare problemi di prestazioni, è opportuno prendere in considerazione metriche personalizzate.

## <a name="custom-metrics"></a>Metriche personalizzate
Le metriche sono configurate in base alle singole istanze del servizio durante la creazione del servizio.

Qualsiasi metrica è descritta da alcune proprietà: nome, carico predefinito e peso.

* Nome della metrica: il nome della metrica, che è un identificatore univoco della metrica nel cluster dal punto di vista di Resource Manager.
* Default Load: il carico predefinito è rappresentato in modo diverso a seconda che il servizio sia con o senza stato.
  * Per i servizi senza stato, ogni metrica ha un'unica proprietà denominata DefaultLoad
  * Per i servizi con stato si definiscono le proprietà seguenti.
    * PrimaryDefaultLoad: quantità predefinita della metrica utilizzata dal servizio quando è primario
    * SecondaryDefaultLoad: quantità predefinita della metrica utilizzata dal servizio quando è secondario
* Peso: il peso della metrica ne definisce l'importanza rispetto alle altre metriche per il servizio.

Se si definiscono metriche personalizzate e si vogliono usare anche le metriche predefinite, è necessario riaggiungerle esplicitamente, per stabilire chiaramente la relazione tra le metriche predefinite e quelle personalizzate. È ad esempio possibile che si attribuisca al consumo di memoria o alla profondità della coda di lavoro molta più importanza rispetto alla distribuzione primaria.

### <a name="defining-metrics-for-your-service---an-example"></a>Definizione delle metriche per il servizio: esempio
Si supponga di voler configurare un servizio che fornisce report su una metrica denominata "MemoryInMb", usando anche le metriche predefinite. Si supponga anche di aver eseguito alcune misurazioni rilevando che normalmente per una replica primaria del servizio sono necessarie 20 unità di "MemoryInMb", mentre per quelle secondarie ne sono necessarie 5. Si è consapevoli del fatto che la memoria è la metrica più importante in termini di gestione delle prestazioni del servizio specifico, ma si vuole comunque che le repliche primarie siano bilanciate. Il bilanciamento delle repliche primarie è consigliabile per evitare che la perdita di nodi o domini di errore influisca sulla maggior parte delle repliche primarie. A parte questi perfezionamenti, si vogliono usare le metriche predefinite.

Di seguito è riportato il codice da scrivere per creare un servizio con tale configurazione delle metriche:

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
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Promemoria: se si vogliono usare solo le metriche predefinite, non è necessario modificare in alcun modo la raccolta di metriche né eseguire operazioni speciali durante la creazione del servizio.

Dopo l'introduzione e questo esempio, verranno esaminate in modo più dettagliato le singole impostazioni e verrà illustrato il comportamento ottenuto.

## <a name="load"></a>chiudi
La definizione di metriche consiste essenzialmente nella rappresentazione di un carico. Il "carico" è la quantità di una determinata metrica che viene utilizzata da una replica o un'istanza del servizio in un nodo specifico. Il carico previsto può essere configurato quando si crea un servizio, essere aggiornato dopo la creazione e/o essere segnalato per singolo oggetto di servizio.

## <a name="default-load"></a>Carico predefinito
Il carico predefinito è la quantità di carico utilizzata per impostazione predefinita da ogni oggetto (istanza o replica) del servizio specifico. Per i servizi più semplici, il carico predefinito è una definizione statica che non viene mai aggiornata e viene usata per la durata del servizio. Il carico predefinito è efficace per scenari di pianificazione della capacità semplici, in cui determinate quantità di risorse sono dedicate a diversi carichi di lavoro.

Cluster Resource Manager consente ai servizi con stato di definire un carico predefinito diverso per le repliche primarie e quelle secondarie, mentre per i servizi senza stato può essere specificato un solo valore. Per i servizi con stato, il carico predefinito per le repliche primarie e secondarie è in genere diverso perché le repliche eseguono diverse tipologie di lavoro nel rispettivo ruolo. A differenza delle repliche secondarie, ad esempio, le repliche primarie gestiscono in genere operazioni sia di lettura che di scrittura (oltre alla maggior parte delle operazioni di calcolo). Si prevede quindi che il carico predefinito per una replica primaria sia superiore rispetto a quello delle repliche secondarie, ma i numeri reali dipenderanno dalla misurazioni dell'utente.

## <a name="dynamic-load"></a>Carico dinamico
Si supponga di aver eseguito un servizio per un determinato periodo di tempo e di aver rilevato, con alcune attività di monitoraggio, quanto segue:

1. Alcune partizioni o istanze di un determinato servizio utilizzano più risorse di altre.
2. Il carico di alcuni servizi varia nel tempo.

Fluttuazioni del carico di questo tipo possono avere diverse cause. La partizione o il servizio potrebbe essere associato a un determinato cliente oppure corrispondere a carichi di lavoro che variano nel corso della giornata. Indipendentemente dal motivo, non esiste un singolo numero che possa essere usato come carico predefinito. Qualsiasi valore selezionato come carico predefinito risulterà errato in determinati momenti. Questo costituisce un problema perché carichi predefiniti non corretti causano un'allocazione eccessiva o insufficiente di risorse per il servizio da parte di Cluster Resource Manager. Di conseguenza, i nodi saranno sovrautilizzati o sottoutilizzati nonostante Cluster Resource Manager ritenga che il cluster sia bilanciato. I carichi predefiniti sono comunque validi perché forniscono alcune informazioni, ma nella maggior parte dei casi non rappresentano interamente i carichi di lavoro reali. Per questo motivo, Cluster Resource Manager consente a ogni oggetto di servizio di aggiornare il proprio carico in fase di esecuzione, con la creazione di report di carico dinamici.

I report di carico dinamici consentono alle repliche o alle istanze di modificare l'allocazione o il carico segnalato delle metriche nel corso della propria durata. Una replica o un'istanza del servizio che è inattiva e non esegue alcuna operazione segnalerà in genere un uso ridotto di una determinata metrica, mentre una replica o un'istanza occupata segnalerà un uso superiore.

La creazione di report per replica o istanza consente a Cluster Resource Manager di riorganizzare i singoli oggetti di servizio nel cluster in modo che i servizi ottengano le risorse necessarie. I servizi occupati riescono effettivamente a "recuperare" le risorse da altre repliche o istanze attualmente inattive o meno occupate.

All'interno del servizio Reliable Services, il codice per la creazione di report di carico dinamici si presenterà come riportato di seguito.

Codice:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

Le repliche o le istanze del servizio possono segnalare il carico solo per le metriche disponibili per l'uso in base alla configurazione definita al momento della creazione. L'elenco delle metriche per cui un servizio può creare report corrisponde al set specificato quando il servizio è stato creato. L'elenco delle metriche associate al servizio può anche essere aggiornato in modo dinamico. Se una replica o un'istanza del servizio prova a segnalare il carico per una metrica non ancora disponibile per l'uso in base alla rispettiva configurazione, Service Fabric registra il report ma lo ignora. Se nella stessa chiamata API vengono segnalate altre metriche valide, i report vengono accettati e usati. Questo approccio consente una maggiore sperimentazione. Il codice può eseguire la misurazione e la creazione di report per tutte le metriche configurate e l'operatore può specificare e aggiornare la configurazione delle metriche per il servizio senza dover modificare il codice. L'amministratore o il team operativo può ad esempio disabilitare una metrica con un report anomalo per un determinato servizio, riconfigurare i pesi delle metriche in base al comportamento oppure abilitare una nuova metrica solo dopo che il codice è già stato distribuito e convalidato tramite altri meccanismi.

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Combinazione di valori di carico predefiniti e report sul carico dinamico
Se il carico predefinito non è sufficiente ed è consigliabile creare report di carico dinamici, è possibile usare entrambi? È possibile usarlo. Questa è infatti la configurazione consigliata. Quando è impostato il carico predefinito e vengono utilizzati report di carico dinamici, il carico predefinito funge da stima finché non sono disponibili i report dinamici. Questo approccio è efficace perché offre a Cluster Resource Manager elementi su cui lavorare. Il carico predefinito consente a Cluster Resource Manager di posizionare in modo ottimale gli oggetti di servizio quando vengono creati. Se non vengono specificate informazione sul carico predefinito, i servizi vengono posizionati in modo casuale. Quando successivamente vengono generati i report di carico, è quasi sempre necessario che Cluster Resource Manager sposti i servizi.

Partendo dall'esempio precedente, è possibile verificare le conseguenze dell'aggiunta di alcune metriche personalizzate e dei report di carico dinamici. In questo esempio verrà usata la metrica "Memory" e si presuppone che il servizio con stato sia stato inizialmente creato con il comando riportato di seguito.

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Si ricordi che la sintassi è ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Un layout di cluster può avere un aspetto analogo al seguente:

<center>
![Cluster bilanciato con metriche sia predefinite che personalizzate][Image2]
</center>

Occorre notare alcuni aspetti:

* Dato che le repliche o le istanze usano il carico predefinito del servizio fino a quando non segnalano il proprio, è possibile determinare che le repliche all'interno della partizione 1 del servizio con stato non hanno creato report di carico dinamici.
* Le repliche secondarie in una partizione possono avere un carico specifico.
* Complessivamente, le metriche appaiono bilanciate. Per la memoria, il rapporto tra carico massimo e minimo è 1,75. Il nodo con il carico maggiore è N3, quello con il carico minore è N2 e 28/16 = 1,75.

Restano ancora da spiegare alcuni aspetti:

* In che modo è stato stabilito se un rapporto di 1,75 è ragionevole? Come determina Cluster Resource Manager se la configurazione è ottimale o deve essere modificata?
* Quando viene applicato il bilanciamento?
* Cosa significa che il peso di Memory è "High"?

## <a name="metric-weights"></a>Pesi metrici
La possibilità di tenere traccia delle stesse metriche in diversi servizi è importante, perché consente a Cluster Resource Manager di tenere traccia del consumo nel cluster, bilanciare il consumo tra i nodi e garantire che non ne venga superata la capacità. La stessa metrica, tuttavia, può assumere una diversa importanza per i diversi servizi. In un cluster con molte metriche e numerosi servizi, inoltre, potrebbero non esistere soluzioni perfettamente bilanciate per tutte le metriche. Come devono essere gestite queste situazioni in Cluster Resource Manager?

I pesi delle metriche consentono a Cluster Resource Manager di decidere come bilanciare il cluster quando non esiste una risposta perfetta, nonché di bilanciare in modo diverso servizi specifici. Le metriche possono avere quattro livelli di peso diversi: Zero, Low, Medium e High. Una metrica con peso Zero non ha alcun impatto sulle considerazioni relative al bilanciamento o meno degli elementi, ma il relativo carico ha comunque un impatto su aspetti come la capacità.

L'impatto effettivo di pesi delle metriche diversi nel cluster consiste nel fatto che Cluster Resource Manager genera soluzioni diverse. I pesi indicano a Cluster Resource Manager che determinate metriche sono più importanti di altre. Quando non esiste una soluzione perfetta, Cluster Resource Manager può preferire soluzioni con un migliore bilanciamento delle metriche con peso superiore. Se una metrica non è importante per un servizio, il relativo uso della metrica potrebbe essere sbilanciato in modo da garantire a un altro servizio, per cui è importante, una distribuzione uniforme.

L'esempio seguente illustra alcuni report di carico e come pesi diversi delle metriche possono determinare allocazioni diverse nel cluster. In questo esempio si può notare che la modifica del peso relativo delle metriche fa sì che Cluster Resource Manager preferisca soluzioni diverse e crei disposizioni diverse dei servizi.

<center>
![Esempio di peso delle metriche e del relativo impatto sulle soluzioni di bilanciamento][Image3]
</center>

In questo esempio sono presenti quattro diversi servizi, ognuno dei quali segnala valori diversi per due diverse metriche, A e B. In un caso, MetricA è definita da tutti i servizi come la più importante (Weight = High), mentre MetricB è definita come non importante (Weight = Low). In questo caso, Cluster Resource Manager posiziona i servizi in modo da ottenere per MetricA un bilanciamento migliore (ovvero una deviazione standard minore) rispetto a MetricB. Nel secondo caso, i pesi delle metriche sono invertiti. Di conseguenza, Cluster Resource Manager probabilmente scambierà i servizi A e B in modo da ottenere un'allocazione in cui il bilanciamento di MetricB è migliore rispetto a MetricA.

### <a name="global-metric-weights"></a>Pesi metrici globali
Se ServiceA definisce MetricA come metrica più importante e ServiceB non è interessato a questa metrica, occorre calcolare il peso reale effettivamente usato.

Per ogni metrica esistono di fatto più pesi di cui tenere traccia. Il primo set è costituito dai pesi definiti da ogni servizio per la metrica. L'altro peso è un peso globale, corrispondente alla media di tutti i servizi che segnalano la metrica. Cluster Resource Manager usa entrambi questi pesi per calcolare i punteggi delle soluzioni. È infatti importante che un servizio venga bilanciato in base alle specifiche priorità, ma anche che l'allocazione complessiva del cluster sia corretta.

Se Cluster Resource Manager non tiene conto del bilanciamento globale e locale, si potrebbero creare soluzioni bilanciate a livello globale, ma che determinano allocazioni inefficienti delle risorse per i singoli servizi. L'esempio seguente illustra le metriche predefinite con cui è configurato un servizio con stato e che cosa accade se viene considerato solo il bilanciamento globale:

<center>
![Impatto di una soluzione solo globale][Image4]
</center>

Nell'esempio in alto in cui è stato preso in considerazione solo il bilanciamento globale, il cluster nel suo complesso è effettivamente bilanciato. Tutti i nodi hanno lo stesso numero di repliche primarie e di repliche totali. Se tuttavia si esamina l'impatto effettivo di questa allocazione, si notano alcuni problemi. La perdita di un nodo influisce in modo sproporzionato su un carico di lavoro specifico, perché rende inattive tutte le repliche primarie. In caso di errore nel primo nodo, ad esempio, tutte e tre le repliche primarie per le tre diverse partizioni del servizio rappresentato con il cerchio andrebbero perse. Le partizioni degli altri due servizi (Triangle e Hexagon), invece, perderebbero una replica e ciò non provocherebbe alcun problema per il servizio, ad eccezione della necessità di ripristinare la replica inattiva.

Nell'esempio in basso, Cluster Resource Manager ha distribuito le repliche in base al bilanciamento globale e per i singoli servizi. Quando calcola il punteggio della soluzione, assegna la maggior parte del peso alla soluzione globale e una parte (configurabile) ai singoli servizi. Il bilanciamento globale viene calcolato in base alla media dei pesi delle metriche configurati per ogni servizio. Ogni servizio viene bilanciato in base ai pesi delle metriche specificamente definiti. In questo modo, i servizi sono bilanciati internamente per quanto possibile in base alle specifiche esigenze. In caso di errore nel primo nodo, quindi, la perdita delle repliche primarie e secondarie viene distribuita in tutte le partizioni di tutti i servizi. L'impatto su ogni elemento è uguale.

## <a name="next-steps"></a>Passaggi successivi
* Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, vedere le [informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md) nell'argomento relativo alle altre configurazioni disponibili per Cluster Resource Manager
* Definire la metrica di deframmentazione rappresenta un modo per consolidare il carico sui nodi anziché distribuirlo. Per informazioni su come configurare la deframmentazione, vedere [questo articolo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
* Partire dall'inizio e vedere l' [introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Il costo dello spostamento è un modo per segnalare a Cluster Resource Manager che alcuni servizi sono più costosi da spostare rispetto ad altri. Per altre informazioni sui costi di spostamento, vedere [questo articolo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png



<!--HONumber=Jan17_HO4-->


