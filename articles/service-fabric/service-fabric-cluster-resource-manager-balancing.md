<properties
   pageTitle="Bilanciamento del carico del cluster con Cluster Resource Manager di Azure Service Fabric"
   description="Introduzione al bilanciamento del carico del cluster con Cluster Resource Manager di Service Fabric."
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

# Bilanciare il carico nel cluster di Service Fabric
Cluster Resource Manager di Service Fabric consente di segnalare il carico dinamico, di reagire alle modifiche nel cluster e di generare piani per il bilanciamento. Ma quando esegue queste operazioni? Che cosa determina il ribilanciamento nel cluster se i servizi vengono inseriti in base ai loro valori di carico predefiniti al momento della creazione? Sono previsti diversi controlli per questa situazione.

La prima serie di controlli che governa il bilanciamento del carico è costituita da timer che controllano la frequenza con cui Cluster Resource Manager esamina lo stato del cluster alla ricerca di problemi da risolvere. Questi timer riguardano le diverse fasi del lavoro che vengono sempre eseguite. Si tratta di:

1.	Posizionamento: questa fase riguarda l'inserimento di repliche con stato o istanze senza stato mancanti. Questo interessa sia i nuovi servizi sia la gestione di repliche o istanze che non sono riuscite correttamente e devono essere ricreate. In questa fase viene gestita anche l'eliminazione di istanze e repliche.
2.	Verifiche dei vincoli: in questa fase vengono controllate e corrette le violazioni dei vincoli (regole) di posizionamento all'interno del sistema. Le regole servono, ad esempio, per controllare che i nodi non superino la capacità o che i vincoli di posizionamento del servizio vengano rispettati. Più avanti verranno fornite altre informazioni sui vincoli di posizionamento.
3.	Bilanciamento del carico: questa fase verifica se è necessario applicare il ribilanciamento proattivo sulla base del livello di bilanciamento configurato per le diverse metriche e, in tal caso, tenta di trovare una disposizione più bilanciata all'interno del cluster.

Ogni fase è controllata da un timer diverso che ne regola la frequenza. Poniamo, ad esempio, che si desideri gestire ogni ora solo l'inserimento di nuovi carichi di lavoro del servizio nel cluster (raggruppandoli), ma applicare controlli di bilanciamento standard ogni pochi secondi. I vari timer consentono di farlo. Quando ogni timer si attiva, viene impostato un flag per segnalare che è necessario occuparsi di quella porzione dei compiti di Resource Manager. Il flag viene rilevato nella scansione globale dello stato del computer seguente ed è per questo che queste configurazioni sono definite "intervalli minimi". Per impostazione predefinita, Resource Manager analizza il suo stato e applica gli aggiornamenti ogni 1/10 di secondo, imposta i flag di posizionamento e di verifica dei vincoli ogni secondo e imposta il flag di bilanciamento del carico ogni 5 secondi.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Per questo esempio eseguiremo una sola di queste azioni per volta, in sequenza. Ad esempio, prima di procedere al bilanciamento del carico del cluster, avremo già risposto a tutte le richieste di creare nuove repliche. Come risulta chiaro dagli intervalli di tempo predefiniti specificati, possiamo analizzare e controllare tutto ciò che vogliamo molto di frequente, con il risultato che ogni set di modifiche è generalmente piccolo. Infatti, non analizziamo ore di modifiche nel cluster cercando di correggerle tutte contemporaneamente e possiamo, quindi, gestire efficacemente quello che accade in tempo reale. È questo aspetto che rende Resource Manager di Service Fabric molto reattivo a quanto avviene nel cluster.

Cluster Resource Manager ha sostanzialmente bisogno di sapere quando considerare sbilanciato il cluster e quali repliche spostare per risolvere il problema. Per gestire questi aspetti disponiamo di due controlli di configurazione: le soglie di bilanciamento del carico e le soglie di attività.

## Soglie di bilanciamento del carico
Una soglia di bilanciamento del carico è il controllo principale per attivare il ribilanciamento proattivo. La soglia di bilanciamento del carico definisce quanto deve essere sbilanciato il cluster in base a una metrica specifica per fare in modo che Resource Manager lo consideri sbilanciato e attivi il bilanciamento del carico durante l'esecuzione successiva. Le soglie di bilanciamento del carico sono definite sulla base di metriche attraverso il manifesto del cluster:

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

La soglia di bilanciamento del carico per una metrica è espressa con un rapporto. Se il volume di carico sul nodo più carico diviso per il volume di carico sul nodo meno carico supera questo rapporto, il cluster viene considerato sbilanciato e durante l'esecuzione successiva del nodo di stato Resource Manager attiverà il bilanciamento del carico.

![Esempio di soglia di bilanciamento][Image1]

In questo semplice esempio ogni servizio usa solo un'unità di una metrica. Nell'esempio in alto, il carico massimo su un nodo è 5 e il carico minimo è 2. Supponiamo che la soglia di bilanciamento del carico per questa metrica sia 3. Pertanto, nell'esempio in alto, il cluster viene considerato bilanciato e il bilanciamento del carico non verrà attivato. Nell'esempio in basso, il carico massimo su un nodo è 10 mentre il carico minimo è 2, il che ci porta oltre la soglia di bilanciamento del carico di 3. Di conseguenza, quasi certamente il carico verrà distribuito a Node3 alla prima esecuzione di Resource Manager. Si noti che poiché non stiamo usando un approccio greedy, una parte del carico potrebbe anche finire in Node2, dal momento che questo comporterebbe la riduzione delle differenze complessive tra i nodi.

![Azioni sull'esempio di soglia di bilanciamento][Image2]

Si noti che rimanere sotto la soglia di bilanciamento del carico non è un obiettivo esplicito, in quanto le soglie di bilanciamento del carico sono solo il trigger. Soglie di attività A volte accade che anche se i nodi sono relativamente sbilanciati, la quantità totale di carico nel cluster sia bassa. Ciò può essere legato all'ora del giorno oppure dipendere dal fatto che il cluster è nuovo e non ancora avviato automaticamente. In entrambi i casi, potrebbe essere superfluo dedicare del tempo al bilanciamento del carico poiché il guadagno sarebbe contenuto visto che si tratterebbe di usare risorse di rete e di calcolo per spostare oggetti da una parte all'altra. In Resource Manager esiste un altro controllo definito soglia di attività, che consente di specificare un limite inferiore assoluto di attività. Se in nessun nodo è presente almeno questa quantità di attività minima, il bilanciamento del carico non verrà attivato anche se la soglia di bilanciamento viene raggiunta. Come esempio, si supponga di avere dei rapporti con i seguenti totali di consumo su questi nodi. Supponiamo inoltre di mantenere la nostra soglia di bilanciamento del carico a 3, ma aggiungendo una soglia di attività di 1536. Nel primo caso, mentre il cluster è sbilanciato in base alla soglia di bilanciamento, nessun nodo raggiunge la soglia minima di attività, pertanto il cluster non viene toccato. Nell'esempio in basso, Node1 è ben oltre la soglia di attività, pertanto, il bilanciamento del carico verrà eseguito.

![Esempio di soglia di attività][Image3]

Proprio come le soglie di bilanciamento del carico, le soglie di attività sono definite sulla base di metriche tramite il file manifesto del cluster:

``` xml
      <Section Name="MetricActivityThresholds">
        <Parameter Name="Memory" Value="1536"/>
      </Section>
```

## Bilanciamento composto dei servizi
Un elemento che è interessante notare è che la decisione in merito allo sbilanciamento o meno del cluster riguarda l'intero cluster, ma il modo per correggere la situazione consiste nello spostare singole repliche e istanze in posizioni diverse. Questo approccio ha senso, giusto? Se la memoria in un nodo è satura, la presenza di più repliche o istanze potrebbe contribuire alla situazione e sarebbe pertanto necessario spostare tutte le repliche e le istanze che usano la metrica sbilanciata interessata.

In alcuni casi, tuttavia, accade che un cliente chiami o invii un ticket per lamentarsi che un servizio che non era sbilanciato è stato spostato. Come è potuto accadere se tutte le metriche del servizio erano perfettamente bilanciate al momento dell'altro squilibrio? Vediamo che cosa potrebbe essere successo.

Si considerino quattro servizi: S1, S2, S3 e S4. S1 segnala i risultati delle metriche M1 e M2, S2 i risultati di M2 e M3, S3 i risultati di M3 e M4 e S4 i risultati della metrica M99. Sicuramente è già possibile intravedere una spiegazione. C'è una catena. Dal punto di vista di Resource Manager non ci sono quattro servizi indipendenti, bensì una serie di servizi correlati tra loro (S1, S2 e S3) e uno autonomo.

![Bilanciamento composto dei servizi][Image4]

Per questo motivo, è possibile che uno squilibrio in Metric1 provochi lo spostamento di repliche o istanze appartenenti a Service3. In genere, questi movimenti sono piuttosto limitati, ma possono essere di maggiore portata a seconda di quanto è sbilanciata Metric1 e di quali modifiche sono state necessarie per correggere l'errore. È anche possibile affermare con certezza che uno squilibrio nelle metriche 1, 2 o 3 non comporterà mai spostamenti in Service4. Non avrebbe alcuna utilità in quanto lo spostamento di repliche o istanze appartenenti a Service4 non ha alcun impatto sul bilanciamento delle metriche 1, 2 o 3.

Resource Manager determina automaticamente a ogni esecuzione quali servizi sono correlati tra loro, perché i servizi potrebbero essere stati aggiunti, rimossi o modificati in termini di configurazione delle metriche. Ad esempio, tra due esecuzioni di bilanciamento del carico, Metric2 potrebbe essere stata rimossa da Service2. Questa operazione interrompe la catena tra Service1 e Service2. Ora, invece di due gruppi di servizi ce ne sono tre:

![Bilanciamento composto dei servizi][Image5]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Informazioni sulle metriche](service-fabric-cluster-resource-manager-metrics.md)
- [Informazioni sulle limitazioni di Resource Manager](service-fabric-cluster-resource-manager-advanced-throttling.md)
- [Informazioni su costo dello spostamento dei servizi](service-fabric-cluster-resource-manager-movement-cost.md)


[Image1]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

<!---HONumber=AcomDC_0309_2016-->