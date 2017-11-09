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
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 06d65878d84fb845cf0c4c333a1e2d12b0aaec2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="balancing-your-service-fabric-cluster"></a>Bilanciamento del carico nel cluster di Service Fabric
Cluster Resource Manager di Service Fabric supporta le modifiche al carico dinamico, reagisce all'aggiunta o alla rimozione di nodi o servizi. Corregge anche automaticamente le violazioni dei vincoli ed esegue in modo proattivo il ribilanciamento del cluster. Ma con quale frequenza vengono eseguite queste azioni, e che cosa le attiva?

Sono disponibili tre diverse categorie di lavoro eseguite da Cluster. Resource Manager. Sono:

1. Posizionamento: questa fase riguarda l'inserimento di repliche con stato o istanze senza stato mancanti. Il posizionamento include sia i nuovi servizi sia la gestione di repliche con stato o istanze senza stato non riuscite. In questa fase viene gestita l'eliminazione di istanze e repliche.
2. Verifiche dei vincoli: in questa fase vengono controllate e corrette le violazioni dei vincoli (regole) di posizionamento all'interno del sistema. Le regole servono, ad esempio, per controllare che i nodi non superino la capacità o che i vincoli di posizionamento di un servizio vengano rispettati.
3. Bilanciamento del carico: in questa fase viene verificata la necessità di applicare il ribilanciamento sulla base del livello di bilanciamento configurato per le diverse metriche. In tal caso, viene eseguito il tentativo di trovare una disposizione più bilanciata nel cluster.

## <a name="configuring-cluster-resource-manager-timers"></a>Configurazione dei timer di Cluster Resource Manager
Il primo set di controlli sul bilanciamento del carico sono un set di timer. Questi timer controllano la frequenza con cui Cluster Resource Manager esamina il cluster e intraprende le azioni correttive.

Ognuno dei tipi diversi di correzioni che Cluster Resource Manager può apportare è controllato da un timer diverso che ne determina la frequenza. Quando viene attivato ogni timer, l'attività viene pianificata. Per impostazione predefinita, Resource Manager:

* Analizza lo stato e applica gli aggiornamenti, ad esempio la registrazione di un nodo inattivo, ogni decimo di secondo.
* imposta il flag di controllo di selezione 
* imposta il flag di controllo del vincolo ogni secondo
* Imposta il flag di bilanciamento ogni cinque secondi.

Di seguito sono riportati alcuni esempi di configurazione che controllano i timer:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

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
          "name": "MinConstraintCheckInterval",
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

Oggi Cluster Resource Manager esegue solo una di queste azioni alla volta, in sequenza. Questo è il motivo per cui si fa riferimento a questi timer come "intervalli minimi" e le azioni che vengono eseguite quando il timer viene spente come "flag di impostazione". Ad esempio, Cluster Resource Manager si occupa delle richieste di creazione di servizi in sospeso prima di bilanciare il carico del cluster. Come si nota dagli intervalli di tempo predefiniti specificati, Cluster Resource Manager analizza di frequente tutte le attività. In genere, ciò significa che il set di modifiche apportate durante ogni passaggio è piccolo. Le modifiche piccole e frequenti consentono a Cluster Resource Manager di essere sensibile quando ciò si verifica nel cluster. I timer predefiniti eseguono una sorta di divisione in batch, dato che molti degli eventi dello stesso tipo tendono a verificarsi simultaneamente. 

Ad esempio, quando i nodi presentano errori possono procedere con interi domini di errore alla volta. Tutti questi errori vengono acquisiti durante il successivo aggiornamento dopo il *PLBRefreshGap*. Le correzioni vengono determinate durante il posizionamento seguente del controllo del vincolo, e bilanciamento del carico viene eseguito. Per impostazione predefinita, Cluster Resource Manager non analizza ore di modifiche nel cluster e non tenta di risolvere tutte le modifiche in una sola volta. Questo potrebbe generare burst di varianza.

Cluster Resource Manager necessita di informazioni aggiuntive per determinare se il cluster è sbilanciato. Per gestire questi aspetti sono disponibili due controlli di configurazione: le *soglie di bilanciamento* e le *soglie di attività*.

## <a name="balancing-thresholds"></a>Soglie di bilanciamento del carico
Una soglia di bilanciamento del carico è il controllo principale per attivare il ribilanciamento. La soglia di bilanciamento del carico per una metrica è espressa con un _rapporto_. Se il volume di carico sul nodo più carico diviso per il volume di carico sul nodo meno carico supera il *BalancingThreshold*della metrica, il cluster viene considerato sbilanciato. In tal caso, al successivo controllo di Cluster Resource Manager viene attivato il bilanciamento del carico. Il timer *MinLoadBalancingInterval* definisce la frequenza dei controlli eseguiti da Cluster Resource Manager se è necessario il ribilanciamento. Controllare non comporta che venga eseguita un'operazione. 

Le soglie di bilanciamento del carico sono definite sulla base delle singole metriche nell'ambito della definizione del cluster. Per altre informazioni sulle metriche, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

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

<center>
![Esempio di soglia di bilanciamento][Image1]
</center>

In questo esempio ogni servizio usa una unità di una metrica. Nell'esempio in alto il carico massimo su un nodo è cinque e il carico minimo è due. Si supponga che la soglia di bilanciamento del carico per questa metrica sia tre. Dato che il rapporto nel cluster è 5/2 = 2,5 e che tale cifra è inferiore al valore tre della soglia di bilanciamento, il cluster è bilanciato. In tal caso, al successivo controllo di Cluster Resource Manager non viene attivato alcun bilanciamento del carico.

Nell'esempio in basso il carico massimo su un nodo è 10, mentre il carico minimo è due. Il rapporto è quindi pari a cinque. Cinque è superiore alla soglia di bilanciamento del carico designata di tre per tale metrica. Di conseguenza, all'attivazione successiva del timer di bilanciamento del carico verrà pianificato un ribilanciamento. In una situazione come questa, parte del carico viene in genere distribuito nel nodo 3. Poiché Cluster Resource Manager di Service Fabric non usa un approccio greedy, parte del carico potrebbe essere distribuita anche nel nodo 2. 

<center>
![Azioni sull'esempio di soglia di bilanciamento][Image2]
</center>

> [!NOTE]
> Il "Bilanciamento" gestisce due diverse strategie per gestire il carico nel cluster. La strategia predefinita usata da Cluster Resource Manager consiste nel distribuire il carico tra i nodi del cluster. L'altra strategia è la [deframmentazione](service-fabric-cluster-resource-manager-defragmentation-metrics.md). La deframmentazione viene eseguita durante l'esecuzione dello stesso bilanciamento. Le strategie di bilanciamento e deframmentazione possono essere usate per metriche diverse all'interno dello stesso cluster. Un servizio può disporre di metriche di bilanciamento e di deframmentazione. Per la metrica di deframmentazione, il rapporto dei carichi nel cluster attiva in questo caso il ribilanciamento quando è _al di sotto_ della soglia di bilanciamento. 
>

Rimanere sotto la soglia di bilanciamento del carico non è un obiettivo esplicito. Le soglie di bilanciamento sono semplicemente un *trigger*. Quando viene eseguito il bilanciamento, Cluster Resource Manager determina quali miglioramenti può apportare, se ve ne sono. Il fatto che venga avviata una ricerca di bilanciamento non significa che vengano spostati degli elementi. A volte il cluster non è bilanciato ma è troppo limitato per essere corretto. In alternativa, i miglioramenti richiedono dei movimenti che sono troppo [costosi](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>soglie di attività
A volte, sebbene i nodi siano relativamente sbilanciati, la quantità *totale* di carico nel cluster è bassa. La mancanza di carico può essere dovuta a un calo temporaneo o al fatto che il cluster è nuovo ed è stato avviato da poco. In entrambi i casi non è consigliabile perdere tempo con il bilanciamento del carico del cluster perché i risultati non sarebbero soddisfacenti. Se il carico del cluster è stato bilanciato, si perderebbero risorse di rete e di calcolo per spostamenti che *non* farebbero grandi differenze. Per evitare movimenti non necessari, è disponibile un altro controllo noto come Soglie di attività. Le soglie di attività consentono di specificare un limite inferiore assoluto per l'attività. Se nessun nodo supera tale soglia, il bilanciamento del carico non viene attivato neanche se viene raggiunta la soglia di bilanciamento.

Si supponga che abbiamo mantenuto la soglia di bilanciamento su tre per questa metrica. Si supponga anche che sia disponibile una Soglia di attività di 1536. Nel primo caso il cluster è sbilanciato in base alla soglia di bilanciamento, ma nessun nodo raggiunge la soglia di attività, quindi non viene eseguita alcuna azione. Nell'esempio in basso il nodo 1 supera la Soglia di attività. Dato che vengono superate sia la soglia di bilanciamento che la soglia di attività per la metrica, viene pianificato un bilanciamento del carico. Ad esempio, si veda il diagramma seguente: 

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

> [!NOTE]
> Se omessa, la soglia di bilanciamento del carico per una metrica è 1, mentre la soglia di attività è 0. Ciò significa che Cluster Resource Manager tenterà di mantenere tale metrica perfettamente bilanciata per un determinato carico. Se si usano metriche personalizzate, è consigliabile definire le soglie di bilanciamento del carico e delle attività in modo esplicito per le metriche. 
>

## <a name="balancing-services-together"></a>Bilanciamento composto dei servizi
Che il cluster sia bilanciato o no è una decisione a livello di cluster. Viene, tuttavia, corretto spostando le singole repliche e le istanze dei servizi. Questo approccio ha senso, giusto? Uno stack di memoria in un nodo può ricevere contributi da più repliche o istanze. Per correggere lo sbilanciamento potrebbe essere necessario spostare una delle repliche con stato o delle istanze senza stato che usano la metrica sbilanciata.

In alcuni casi però viene spostato un servizio che non era sbilanciato (ricordare la discussione precedente relativa ai pesi locali e globali). Per quale motivo si dovrebbe spostare un servizio quando tutte le metriche di quel servizio sono state bilanciate? Di seguito viene illustrato un esempio:

- Si supponga di avere quattro servizi: Service1, Service2, Service3 e Service4. 
- Service1 riporta le metriche Metric1 e Metric2. 
- Service2 riporta le metriche Metric2 e Metric3. 
- Service3 riporta le metriche Metric3 e Metric4.
- Service4 riporta la metrica Metric99. 

Sicuramente è già possibile intravedere una spiegazione: la catena. Non ci sono quattro servizi indipendenti, ma tre servizi correlati tra loro e uno autonomo.

<center>
![Bilanciamento composto dei servizi][Image4]
</center>

A causa di questa catena, è possibile che uno squilibrio nelle metriche 1-4 provochi lo spostamento di repliche o istanze appartenenti ai servizi 1-3. Sappiamo anche che uno sbilanciamento delle metriche 1, 2 o 3 non può comportare movimenti in Service4. Non sarebbero di alcuna utilità, dato che lo spostamento di repliche o istanze appartenenti a Service4 non influirebbe in alcun modo sul bilanciamento delle metriche 1-3.

Cluster Resource Manager determina automaticamente i servizi correlati. Aggiungere, rimuovere o modificare le metriche per i servizi può influire sulle relative relazioni. Ad esempio, tra due esecuzioni di bilanciamento del carico, Service2 potrebbe essere stato aggiornato per rimuovere Metric2. Questa operazione interrompe la catena tra Service1 e Service2. Ora, invece di due gruppi di servizi correlati ce ne sono tre:

<center>
![Bilanciamento composto dei servizi][Image5]
</center>

## <a name="next-steps"></a>Passaggi successivi
* Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)
* Il costo dello spostamento è un modo per segnalare a Cluster Resource Manager che alcuni servizi sono più costosi da spostare rispetto ad altri. Per altre informazioni sui costi di spostamento, vedere [questo articolo](service-fabric-cluster-resource-manager-movement-cost.md).
* Cluster Resource Manager dispone di diverse limitazioni da configurare per rallentare la varianza del cluster. Queste limitazioni non sono in genere necessarie, ma sono eventualmente disponibili altre informazioni [qui](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
