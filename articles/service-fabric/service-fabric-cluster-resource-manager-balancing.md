---
title: Bilanciare il carico del cluster di Azure Service Fabric | Documentazione Microsoft
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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: ee77a01b2bb17ab70099e891e00255d1cec676f6


---
# <a name="balancing-your-service-fabric-cluster"></a>Bilanciamento del carico nel cluster di Service Fabric
Cluster Resource Manager di Service Fabric supporta le modifiche al carico dinamico, reagisce all'aggiunta o alla rimozione di nodi o servizi, corregge le violazioni di vincolo e ribilancia il cluster. Ma qual è la frequenza di queste operazioni e come si attiva?

Il primo set di controlli sul bilanciamento del carico sono un set di timer. Questi timer controllano la frequenza con cui Cluster Resource Manager esamina lo stato del cluster alla ricerca di problemi da risolvere. Esistono tre diverse categorie di lavoro, ognuna con un timer corrispondente. Sono:

1. Posizionamento: questa fase riguarda l'inserimento di repliche con stato o istanze senza stato mancanti. Questo riguarda sia i nuovi servizi sia la gestione di repliche con stato o istanze senza stato non riuscite. In questa fase viene gestita l'eliminazione di istanze e repliche.
2. Verifiche dei vincoli: in questa fase vengono controllate e corrette le violazioni dei vincoli (regole) di posizionamento all'interno del sistema. Le regole servono, ad esempio, per controllare che i nodi non superino la capacità o che i vincoli di posizionamento di un servizio vengano rispettati.
3. Bilanciamento del carico: in questa fase viene verificata la necessità di applicare il ribilanciamento attivo sulla base del livello di bilanciamento configurato per le diverse metriche. In tal caso, viene eseguito il tentativo di trovare una disposizione più bilanciata nel cluster.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configurazione delle fasi e dei timer di Cluster Resource Manager
Ognuno dei tipi diversi di correzioni che Cluster Resource Manager può apportare è controllato da un timer diverso che ne determina la frequenza. Quando viene attivato ogni timer, l'attività viene pianificata. Per impostazione predefinita, Resource Manager:

* Analizza lo stato e applica gli aggiornamenti, ad esempio la registrazione di un nodo inattivo, ogni decimo di secondo.
* Imposta i flag di posizionamento e controllo dei vincoli ogni secondo.
* Imposta il flag di bilanciamento ogni cinque secondi.

Tali impostazioni si rispecchiano nelle informazioni di configurazione seguenti:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Tramite ClusterConfig.json per le distribuzioni autonome o Template.json per cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Oggi Cluster Resource Manager esegue solo una di queste azioni alla volta, in sequenza. Per questo motivo i timer sono detti "intervalli minimi". Ad esempio, Cluster Resource Manager si occupa delle richieste di creazione di servizi in sospeso prima di bilanciare il carico del cluster. Come si nota dagli intervalli di tempo predefiniti specificati, Cluster Resource Manager analizza e controlla di frequente tutte le attività, quindi il set di modifiche alla fine di ogni passaggio è in genere ridotto. Le modifiche piccole e frequenti rendono Cluster Resource Manager sensibile a ciò che si verifica nel cluster. I timer predefiniti eseguono una sorta di divisione in batch, dato che molti degli eventi dello stesso tipo tendono a verificarsi simultaneamente. Per impostazione predefinita, Cluster Resource Manager non analizza ore di modifiche nel cluster e non tenta di risolvere tutte le modifiche in una sola volta. Questo potrebbe generare burst di varianza.

Cluster Resource Manager necessita di informazioni aggiuntive per determinare se il cluster è sbilanciato. Per gestire questi aspetti sono disponibili due controlli di configurazione: le *soglie di bilanciamento* e le *soglie di attività*.

## <a name="balancing-thresholds"></a>Soglie di bilanciamento del carico
Una soglia di bilanciamento del carico è il controllo principale per attivare il ribilanciamento proattivo. Il timer MinLoadBalancingInterval indica unicamente la frequenza dei controlli eseguiti da Cluster Resource Manager e non implica necessariamente un'operazione. La soglia di bilanciamento del carico definisce quanto deve essere sbilanciato il cluster in base a una metrica specifica per fare in modo che Cluster Resource Manager lo consideri sbilanciato e attivi il bilanciamento del carico.

Le soglie di bilanciamento del carico sono definite sulla base delle singole metriche nell'ambito della definizione del cluster. Per altre informazioni sulle metriche, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Tramite ClusterConfig.json per le distribuzioni autonome o Template.json per cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

La soglia di bilanciamento del carico per una metrica è espressa con un rapporto. Se il volume di carico sul nodo più carico diviso per il volume di carico sul nodo meno carico supera questo numero, il cluster viene considerato sbilanciato. In tal caso, al successivo controllo di Cluster Resource Manager viene attivato il bilanciamento del carico.

<center>
![Esempio di soglia di bilanciamento][Image1]
</center>

In questo esempio ogni servizio usa una unità di una metrica. Nell'esempio in alto il carico massimo su un nodo è cinque e il carico minimo è due. Si supponga che la soglia di bilanciamento del carico per questa metrica sia tre. Dato che il rapporto nel cluster è 5/2 = 2,5 e che tale cifra è inferiore al valore tre della soglia di bilanciamento, il cluster è bilanciato. In tal caso, al successivo controllo di Cluster Resource Manager non viene attivato alcun bilanciamento del carico.

Nell'esempio in basso il carico massimo su un nodo è dieci, mentre il carico minimo è due. Il rapporto è quindi pari a cinque. Cinque è superiore alla soglia di bilanciamento del carico designata di tre per tale metrica. Di conseguenza, all'attivazione successiva del timer di bilanciamento del carico verrà pianificato un ribilanciamento. In una situazione come questa, parte del carico verrà quasi certamente distribuito nel nodo&3;. Dato che Cluster Resource Manager di Service Fabric non usa un approccio greedy, parte del carico potrebbe essere distribuita anche nel nodo&2;. Questo comporta una riduzione delle differenze totali tra i nodi, che è uno degli obiettivi di Cluster Resource Manager.

<center>
![Azioni sull'esempio di soglia di bilanciamento][Image2]
</center>

Rimanere sotto la soglia di bilanciamento del carico non è un obiettivo esplicito. Le soglie di bilanciamento del carico sono semplicemente un *trigger* che indica a Cluster Resource Manager di esaminare il cluster per determinare gli eventuali miglioramenti da apportare. In effetti, l'attivazione di una ricerca di bilanciamento del carico non implica necessariamente un'azione. A volte il cluster è sbilanciato ma non è possibile migliorare la situazione.

## <a name="activity-thresholds"></a>soglie di attività
A volte, sebbene i nodi siano relativamente sbilanciati, la quantità *totale* di carico nel cluster è bassa. La mancanza di carico può essere dovuta a un calo temporaneo o al fatto che il cluster è nuovo ed è stato avviato da poco. In entrambi i casi non è consigliabile perdere tempo con il bilanciamento del carico del cluster perché i risultati non sarebbero soddisfacenti. Se il carico del cluster è stato bilanciato, si perderebbero risorse di rete e di calcolo per spostamenti che *non* farebbero alcuna differenza. Per ovviare a questa situazione è disponibile il controllo delle soglie di attività. Le soglie di attività consentono di specificare un limite inferiore assoluto per l'attività. Se nessun nodo supera tale soglia, il bilanciamento del carico non viene attivato neanche se viene raggiunta la soglia di bilanciamento.

Ad esempio, si veda il diagramma seguente. Si supponga anche di mantenere la soglia di bilanciamento a tre per questa metrica, con una soglia di attività di 1536. Nel primo caso il cluster è sbilanciato in base alla soglia di bilanciamento, ma nessun nodo raggiunge la soglia di attività, quindi non viene eseguita alcuna azione. Nell'esempio in basso il nodo&1; supera di gran lunga la soglia di attività. Dato che vengono superate sia la soglia di bilanciamento che la soglia di attività per la metrica, viene pianificato un bilanciamento del carico.

<center>
![Esempio di soglia di attività][Image3]
</center>

Proprio come le soglie di bilanciamento del carico, le soglie di attività sono definite sulla base di singoli metriche tramite la definizione del cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Tramite ClusterConfig.json per le distribuzioni autonome o Template.json per cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Si noti che le soglie di bilanciamento e di attività sono entrambe legate a una metrica specifica. Il bilanciamento del carico viene attivato solo se vengono superate entrambe le soglie di bilanciamento e di attività per la stessa metrica.

## <a name="balancing-services-together"></a>Bilanciamento composto dei servizi
È interessante notare che lo sbilanciamento del cluster non è una decisione che riguarda l'intero cluster. Viene, tuttavia, corretto spostando le singole repliche e le istanze dei servizi. Questo approccio ha senso, giusto? Uno stack di memoria in un nodo può ricevere contributi da più repliche o istanze. Per correggere lo sbilanciamento potrebbe essere necessario spostare una delle repliche con stato o delle istanze senza stato che usano la metrica sbilanciata.

Può tuttavia accadere che venga spostato un servizio che non era sbilanciato. Come è potuto accadere che il servizio sia stato spostato se tutte le metriche del servizio erano perfettamente bilanciate al momento dell'altro squilibrio? Vediamo che cosa potrebbe essere successo.

Si considerino ad esempio quattro servizi: Service1, Service2, Service3 e Service4. Service1 segnala le metriche Metric1 e Metric2, Service2 segnala Metric2 e Mmetric3, Service3 segnala Metric3 e Metric4 e Service4 segnala alcune metriche Metric99. Sicuramente è già possibile intravedere una spiegazione. C'è una catena. Non ci sono quattro servizi indipendenti, ma una serie di servizi correlati tra loro (Service1, Service2 e Service3) e uno autonomo.

<center>
![Bilanciamento composto dei servizi][Image4]
</center>

È quindi possibile che uno squilibrio in Metric1 provochi lo spostamento di repliche o istanze appartenenti a Service3, che non fa riferimento a Metric1. In genere questi movimenti sono limitati, ma possono essere di maggiore portata a seconda dello sbilanciamento di Metric1 e delle modifiche necessarie per correggerlo. È anche possibile affermare con certezza che uno sbilanciamento delle metriche 1, 2 o 3 non può comportare movimenti in Service4. Non sarebbero di alcuna utilità, dato che lo spostamento di repliche o istanze appartenenti a Service4 non influirebbe in alcun modo sul bilanciamento delle metriche 1, 2 o 3.

Cluster Resource Manager trova automaticamente i servizi correlati, perché i servizi potrebbero essere stati aggiunti, rimossi o modificati in termini di configurazione delle metriche. Ad esempio, tra due esecuzioni di bilanciamento del carico, Service2 potrebbe essere stato riconfigurato per rimuovere Metric2. Questa modifica interrompe la catena tra Service1 e Service2. Ora, invece di due gruppi di servizi correlati ce ne sono tre:

<center>
![Bilanciamento composto dei servizi][Image5]
</center>

## <a name="next-steps"></a>Passaggi successivi
* Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md).
* Il costo dello spostamento è un modo per segnalare a Cluster Resource Manager che alcuni servizi sono più costosi da spostare rispetto ad altri. Per altre informazioni sui costi di spostamento, vedere [questo articolo](service-fabric-cluster-resource-manager-movement-cost.md).
* Cluster Resource Manager dispone di diverse limitazioni da configurare per rallentare la varianza del cluster. Queste limitazioni non sono in genere necessarie, ma sono eventualmente disponibili altre informazioni [qui](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Jan17_HO4-->


