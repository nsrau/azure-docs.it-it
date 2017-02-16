---
title: Bilanciamento del carico del cluster con Cluster Resource Manager di Azure Service Fabric | Microsoft Docs
description: Introduzione al bilanciamento del carico del cluster con Cluster Resource Manager di Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d278e0125d698306366713a6e11957dad2de10c5


---
# <a name="balancing-your-service-fabric-cluster"></a>Bilanciamento del carico nel cluster di Service Fabric
Cluster Resource Manager di Service Fabric consente di segnalare il carico dinamico, di reagire alle modifiche nel cluster, di correggere le violazioni alle limitazioni e di ribilanciare il carico, se necessario. Ma qual è la frequenza di queste operazioni e come si attiva? Sono previsti diversi controlli per questa situazione.

Il primo set di controlli sul bilanciamento del carico sono un set di timer. Questi timer controllano la frequenza con cui Cluster Resource Manager esamina lo stato del cluster alla ricerca di problemi da risolvere. Esistono tre diverse categorie di lavoro, ognuna con un timer corrispondente. Vale a dire:

1. Posizionamento: questa fase riguarda l'inserimento di repliche con stato o istanze senza stato mancanti. Questo interessa sia i nuovi servizi sia la gestione di repliche con stato o istanze senza stato che non sono riuscite correttamente e devono essere ricreate. In questa fase viene gestita anche l'eliminazione di istanze e repliche.
2. Verifiche dei vincoli: in questa fase vengono controllate e corrette le violazioni dei vincoli (regole) di posizionamento all'interno del sistema. Le regole servono, ad esempio, per controllare che i nodi non superino la capacità o che i vincoli di posizionamento del servizio vengano rispettati. Più avanti verranno fornite altre informazioni sui vincoli di posizionamento.
3. Bilanciamento del carico: questa fase verifica se è necessario applicare il ribilanciamento proattivo sulla base del livello di bilanciamento configurato per le diverse metriche e, in tal caso, tenta di trovare una disposizione più bilanciata all'interno del cluster.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configurazione delle fasi e dei timer di Cluster Resource Manager
Ognuno di questi tipi diversi di correzioni che Cluster Resource Manager può apportare è controllato da un timer diverso che determina la frequenza. Poniamo, ad esempio, che si desideri gestire ogni ora solo l'inserimento di nuovi carichi di lavoro del servizio nel cluster (raggruppandoli), ma applicare controlli di bilanciamento standard ogni pochi secondi. È possibile configurare questa operazione. Quando viene attivato ogni timer, l'attività viene pianificata. Per impostazione predefinita, Resource Manager analizza il suo stato e applica gli aggiornamenti (eseguendo il batch di tutte le modifiche apportate dall'ultima scansione, come ad esempio notare che un nodo è inattivo) ogni 1/10 di secondo, imposta i flag di posizionamento e di verifica dei vincoli ogni secondo e imposta il flag di bilanciamento del carico ogni 5 secondi.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Oggi eseguiamo solo una di queste azioni contemporaneamente, in modo sequenziale (per questo motivo ci si riferisce a queste configurazioni come "intervalli minimi"). Ad esempio, prima di procedere al bilanciamento del carico del cluster, avremo già risposto a tutte le richieste in sospeso per creare nuove repliche. Come risulta chiaro dagli intervalli di tempo predefiniti specificati, è possibile analizzare e controllare tutte le operazioni da eseguire molto di frequente, con il risultato che l'insieme di modifiche è generalmente più piccolo. Non vengono infatti analizzate ore di modifiche nel cluster cercando di correggerle tutte contemporaneamente; si prova a gestirle più o meno in tempo reale, optando per la gestione in batch in caso di numeri elevati. È questo aspetto che rende Resource Manager di Service Fabric molto reattivo a quanto avviene nel cluster.

Mentre la maggior parte di queste attività è semplice (in caso di violazioni di vincoli, correggerle, se sono presenti servizi da creare, crearli), Cluster Resource Manager necessita anche di alcune informazioni aggiuntive per determinare se il cluster si è sbilanciato. Per gestire questi aspetti sono disponibili due controlli di configurazione: le *soglie di bilanciamento* e le *soglie di attività*.

## <a name="balancing-thresholds"></a>Soglie di bilanciamento del carico
Una soglia di bilanciamento del carico è il controllo principale per l'attivazione del ribilanciamento proattivo (ricordare che il timer serve solo per la frequenza con cui Cluster Resource Manager deve controllare, questo non significa che si verifichi alcuna operazione). La soglia di bilanciamento del carico definisce quanto deve essere sbilanciato il cluster in base a una metrica specifica per fare in modo che Cluster Resource Manager lo consideri sbilanciato e attivi il bilanciamento del carico.

Le soglie di bilanciamento del carico sono definite sulla base delle singole metriche nell'ambito della definizione del cluster. Per altre informazioni sulle metriche, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

La soglia di bilanciamento del carico per una metrica è espressa con un rapporto. Se il volume di carico sul nodo più carico diviso per il volume di carico sul nodo meno carico supera questo numero, il cluster viene considerato sbilanciato e durante l'esecuzione successiva del nodo di stato Cluster Resource Manager attiverà i controlli (per impostazione predefinita, ogni 5 secondi, come stabilito da MinLoadBalancingInterval, in basso).

![Esempio di soglia di bilanciamento][Image1]

In questo semplice esempio ogni servizio usa un'unità di una metrica. Nell'esempio in alto, il carico massimo su un nodo è 5 e il carico minimo è 2. Supponiamo che la soglia di bilanciamento del carico per questa metrica sia 3. Nell'esempio in alto, il cluster viene quindi considerato bilanciato e il bilanciamento del carico non verrà attivato quando Cluster Resource Manager esegue i controlli, dato che il rapporto nel cluster è 5/2 = 2,5, inferiore alla soglia di bilanciamento di 3.

Nell'esempio in basso, il carico massimo su un nodo è 10, mentre il carico minimo è 2. Il rapporto è quindi pari a 5. In questo modo il cluster supera la soglia di bilanciamento del carico pari a 3 designata per tale metrica. Di conseguenza, l'esecuzione di un ribilanciamento globale sarà pianificata successivamente all'attivazione del timer del bilanciamento. Si noti che il fatto che venga avviata una ricerca di bilanciamento del carico non vuol dire che qualcosa verrà spostata: a volte il cluster è sbilanciato ma la situazione non può essere migliorata; in una situazione di questo tipo (almeno per impostazione predefinita) una parte del carico verrà distribuita quasi certamente sul Node3. Si noti che poiché non viene usato un approccio greedy, una parte del carico potrebbe anche essere distribuito sul Node2, dal momento che questo comporterebbe la riduzione delle differenze complessive tra i nodi, ma è prevedibile che la maggior parte del carico verrà distribuita in Node3.

![Azioni sull'esempio di soglia di bilanciamento][Image2]

Si noti che rimanere sotto la soglia di bilanciamento del carico non è un obiettivo esplicito: le soglie di bilanciamento del carico sono semplicemente un *trigger* che indica a Cluster Resource Manager di esaminare il cluster per determinare quali miglioramenti può apportare, se disponibili.

## <a name="activity-thresholds"></a>soglie di attività
A volte, sebbene i nodi siano relativamente sbilanciati, la quantità *totale* di carico nel cluster è bassa. Ciò può essere legato all'ora del giorno oppure dipendere dal fatto che il cluster è nuovo e non ancora avviato automaticamente. In entrambi i casi, potrebbe essere superfluo dedicare del tempo al bilanciamento del cluster poiché il guadagno sarebbe contenuto visto che si tratterebbe di usare risorse di rete e di calcolo per spostare oggetti da una parte all'altra, senza alcuna differenza. Per evitare ciò, in Resource Manager esiste un altro controllo definito soglia di attività, che consente di specificare un limite inferiore assoluto di attività. Se in nessun nodo è presente almeno questa quantità di attività minima, il bilanciamento del carico non verrà attivato anche se la soglia di bilanciamento viene raggiunta.

Come esempio, si supponga di avere dei rapporti con i seguenti totali di consumo su questi nodi. Supponiamo anche di mantenere la nostra soglia di bilanciamento del carico a 3 per questa metrica, ma aggiungendo una soglia di attività di 1536. Nel primo caso, mentre il cluster è sbilanciato in base alla soglia di bilanciamento, nessun nodo raggiunge la soglia minima di attività, pertanto il cluster non viene toccato. Nell'esempio in basso, Node1 supera la soglia di attività, pertanto, verrà eseguito il bilanciamento, poiché vengono superate sia la soglia di bilanciamento del carico che la soglia di attività per la metrica

![Esempio di soglia di attività][Image3]

Proprio come le soglie di bilanciamento del carico, le soglie di attività sono definite sulla base di singoli metriche tramite la definizione del cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Si noti che le soglie di bilanciamento e attività sono entrambe legate alla metrica: il bilanciamento sarà attivato solo se vengono superate entrambe le soglie di bilanciamento e attività per la stessa metrica. Pertanto, se si supera la soglia di bilanciamento per la memoria e la soglia di attività per la CPU, il bilanciamento non sarà attivato fino a quando le soglie rimanenti (soglia di bilanciamento per la CPU e soglia di attività per la memoria) non saranno superate.

## <a name="balancing-services-together"></a>Bilanciamento composto dei servizi
Un elemento che è interessante notare è che la decisione in merito allo sbilanciamento o meno del cluster riguarda l'intero cluster, ma il modo per correggere la situazione consiste nello spostare singole repliche e istanze in posizioni diverse. Questo approccio ha senso, giusto? Se la memoria in un nodo è satura, la presenza di più repliche o istanze potrebbe contribuire alla situazione e sarebbe pertanto necessario spostare eventuali repliche con stato e istanze senza stato che usano la metrica sbilanciata interessata.

In alcuni casi, tuttavia, accade che un cliente chiami o invii un ticket per lamentarsi che un servizio che non era sbilanciato è stato spostato. Come è potuto accadere che il servizio sia stato spostato se tutte le metriche del servizio erano perfettamente bilanciate al momento dell'altro squilibrio? Vediamo che cosa potrebbe essere successo.

Si considerino ad esempio quattro servizi: Service1, Service2, Service3 e Service4. Service1 segnala le metriche Metric1 e Metric2, Service2 segnala Metric2 e Mmetric3, Service3 segnala Metric3 e Metric4 e Service4 segnala alcune metriche Metric99. Sicuramente è già possibile intravedere una spiegazione. C'è una catena. Dal punto di vista di Cluster Resource Manager non ci sono quattro servizi indipendenti, bensì una serie di servizi correlati tra loro (Service1, Service2 e Service3) e uno autonomo.

![Bilanciamento composto dei servizi][Image4]

Per questo motivo, è possibile che uno squilibrio in Metric1 provochi lo spostamento di repliche o istanze appartenenti a Service3. In genere, questi movimenti sono piuttosto limitati, ma possono essere di maggiore portata a seconda di quanto è sbilanciata Metric1 e di quali modifiche sono state necessarie per correggere l'errore. È anche possibile affermare con certezza che uno squilibrio nelle metriche 1, 2 o 3 non comporterà mai spostamenti in Service4. Non avrebbe alcuna utilità in quanto lo spostamento di repliche o istanze appartenenti a Service4 non ha alcun impatto sul bilanciamento delle metriche 1, 2 o 3.

Cluster Resource Manager determina automaticamente quali servizi sono correlati tra loro, perché i servizi potrebbero essere stati aggiunti, rimossi o modificati in termini di configurazione delle metriche. Ad esempio, tra due esecuzioni di bilanciamento del carico, Metric2 potrebbe essere stata rimossa da Service2. Questa operazione interrompe la catena tra Service1 e Service2. Ora, invece di due gruppi di servizi ce ne sono tre:

![Bilanciamento composto dei servizi][Image5]

## <a name="next-steps"></a>Passaggi successivi
* Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)
* Il costo dello spostamento è un modo per segnalare a Cluster Resource Manager che alcuni servizi sono più costosi da spostare rispetto ad altri. Per altre informazioni sui costi di spostamento, vedere [questo articolo](service-fabric-cluster-resource-manager-movement-cost.md)
* Cluster Resource Manager dispone di diverse limitazioni da configurare per rallentare la varianza del cluster. Queste limitazioni non sono in genere necessarie, ma sono eventualmente disponibili altre informazioni [qui](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Dec16_HO2-->


