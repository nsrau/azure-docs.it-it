---
title: Monitoraggio a livello piattaforma di Azure Service Fabric | Microsoft Docs
description: Informazioni sugli eventi e i log a livello piattaforma usati per il monitoraggio e la diagnosi dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: c5857515ae8357b003f0999c4b11bd666c32bbf9
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="platform-level-event-and-log-generation"></a>Generazione di eventi e log a livello piattaforma

## <a name="monitoring-the-cluster"></a>Monitoraggio del cluster

Il monitoraggio a livello piattaforma è importante per determinare se l'hardware e il cluster abbiano o meno il comportamento previsto. Service Fabric può mantenere in esecuzione le applicazioni durante un errore hardware, ma è comunque necessario diagnosticare se l'errore si sia verificato in un'applicazione o nell'infrastruttura sottostante. È consigliabile monitorare i cluster anche per pianificare in modo più efficiente la capacità e supportare le decisioni in merito all'aggiunta o alla rimozione dell'hardware.

Service Fabric offre cinque diversi canali di registrazione predefiniti che generano gli eventi descritti di seguito.

* Canale operativo: operazioni generali eseguite da Service Fabric e dal cluster, inclusi gli eventi per l'attivazione di un nodo, la distribuzione di una nuova applicazione, il ripristino dello stato precedente a un aggiornamento di Service Fabric e così via
* Canale di informazioni operativo: report di integrità e decisioni di bilanciamento del carico
* Canale per la messaggistica e i dati: registri ed eventi critici generati nella messaggistica, attualmente solo il ReverseProxy, e il percorso dei dati, modelli di servizi affidabili
* Canale di informazioni per la messaggistica e i dati: canale dettagliato che contiene tutti i registri non critici di dati e messaggistica nel cluster. Questo canale ha un volume molto elevato di eventi   
* [Eventi di Reliable Services](service-fabric-reliable-services-diagnostics.md): eventi specifici del modello di programmazione
* [Eventi di Reliable Actors](service-fabric-reliable-actors-diagnostics.md): eventi e contatori delle prestazioni specifici del modello di programmazione
* Log di supporto: log di sistema generati da Service Fabric solo per l'uso da parte di Microsoft per fornire supporto

Questi diversi canali coprono la maggior parte della registrazione consigliabile a livello piattaforma. Per migliorare la registrazione a livello piattaforma, valutare la possibilità di comprendere meglio il modello di integrità e aggiungere report sull'integrità personalizzati e **contatori delle prestazioni** personalizzati per ottenere una conoscenza in tempo reale dell'impatto dei servizi e delle applicazioni sul cluster.

### <a name="azure-service-fabric-health-and-load-reporting"></a>Creazione di report su integrità e carico di Azure Service Fabric

Service Fabric offre un modello di integrità specifico, descritto in dettaglio in questi articoli:
- [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)
- [Creare report e verificare l'integrità dei servizi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Aggiungere report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)
- [Come visualizzare i report sull'integrità di Service Fabric](service-fabric-view-entities-aggregated-health.md)

Il monitoraggio dell'integrità è fondamentale per vari aspetti dell'uso di un servizio. Il monitoraggio dell'integrità è particolarmente importante quando Service Fabric esegue l'aggiornamento di un'applicazione denominata. Dopo l'aggiornamento di ogni dominio di aggiornamento del servizio, quando il dominio è disponibile per i clienti, è necessario che superi controlli di integrità prima che la distribuzione passi al dominio di aggiornamento successivo. Se non è possibile ottenere uno stato di integrità ottimale, viene eseguito il rollback della distribuzione, in modo che l'applicazione raggiunga uno stato positivo noto. Anche se è possibile che alcuni clienti subiscano interruzioni prima del rollback del servizio, la maggior parte dei clienti non riscontra problemi. La risoluzione è inoltre relativamente veloce, senza dovere attendere l'intervento di un tecnico. Con un numero maggiore di controlli di integrità inclusi nel codice, il servizio sarà più resistente alle problematiche di distribuzione.

Un altro aspetto dell'integrità del servizio è dato dalla creazione di report sulle metriche del servizio. Le metriche sono importanti in Service Fabric perché vengono usate per bilanciare l'uso delle risorse. Possono anche essere usate come un indicatore dell'integrità del sistema. È ad esempio possibile che sia presente un'applicazione con molti servizi e che ogni istanza segnali una metrica relativa alle richieste al secondo. Se uno dei servizi usa più risorse rispetto a un altro, Service Fabric sposta le istanze del servizio nel cluster, provando a mantenere un uso bilanciato delle risorse stesse. Per una spiegazione più dettagliata del funzionamento dell'utilizzo delle risorse, vedere [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md).

Le metriche consentono anche di ottenere informazioni approfondite sulle prestazioni del servizio. Nel corso del tempo, è possibile usare le metriche per assicurarsi che il servizio stia operando entro i parametri previsti. Se, ad esempio, le tendenze indicano che alle 9:00 del lunedì mattina la media delle richieste al secondo è pari a 1.000, è possibile configurare un report di integrità che genera un avviso se le richieste al secondo sono inferiori a 500 o superiori a 1.500. È possibile che non si verifichino problemi, ma è comunque consigliabile controllare per assicurare la migliore esperienza possibile per i clienti. Il servizio può definire una serie di metriche di cui eseguire il report per l'integrità. Questo comunque non influirà sul bilanciamento delle risorse del cluster. Per ottenere questo risultato, impostare il peso delle metriche su zero. È consigliabile avviare tutte le metriche con peso zero e non aumentare il peso fino a quando non si è sicuri di aver compreso l'impatto della ponderazione sul bilanciamento delle risorse nel cluster.

> [!TIP]
> Non usare un numero eccessivo di metriche ponderate. Potrebbe essere difficile comprendere i motivi dello spostamento delle istanze del servizio per il bilanciamento del carico. Anche un numero ridotto di metriche può essere molto utile.

Tutte le informazioni che possono indicare l'integrità e le prestazioni dell'applicazione possono essere usate per report relativi alle metriche e all'integrità. Un contatore delle prestazioni della CPU può indicare l'uso di un nodo ma non consente di capire se un servizio specifico sia integro, poiché su quel nodo potrebbero essere in esecuzione più servizi. Le metriche relative alle richieste al secondo, agli elementi elaborati e alla latenza delle richieste, tuttavia, possono indicare l'integrità di un servizio specifico.

Per segnalare l'integrità, usare codice analogo al seguente:

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Per segnalare una metrica, usare codice analogo al seguente:

  ```csharp
    this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

### <a name="service-fabric-support-logs"></a>Log di supporto di Service Fabric

Se è necessario contattare il supporto Microsoft per assistenza con il cluster di Azure Service Fabric, saranno quasi sempre richiesti i log di supporto. Se il cluster è ospitato in Azure, questi log vengono automaticamente configurati e raccolti in fase di creazione di un cluster. I log vengono archiviati in un account di archiviazione dedicato nel gruppo di risorse del cluster. L'account di archiviazione non ha un nome predefinito, ma nell'account vengono visualizzati contenitori BLOB e tabelle con nomi che iniziano con *fabric*. Per informazioni sulla configurazione di raccolte di log per un cluster autonomo, vedere [Creare un cluster autonomo di Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) e [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md). Per istanze autonome di Service Fabric, i log devono essere inviati a una condivisione file locale. È **obbligatorio** disporre di questi log per ottenere assistenza. I log devono essere usati solo dal team di assistenza clienti Microsoft.

## <a name="enabling-diagnostics-for-a-cluster"></a>Abilitazione della diagnostica per un cluster

Per sfruttare questi log, è consigliabile che durante la creazione del cluster sia abilitata la diagnostica. Attivando la diagnostica, durante la distribuzione del cluster Diagnostica di Microsoft Azure può riconoscere il canale operativo e quelli di Reliable Services e Reliable Actors e archiviare i dati come illustrato con maggiore dettaglio [Aggregare eventi con Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md).

Come illustrato sopra, c'è anche un campo facoltativo per aggiungere una chiave di strumentazione di Application Insights (AI). Se si sceglie di usare AI per l'analisi degli eventi (per altre informazioni, vedere [Analisi di eventi con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)), includere la chiave di strumentazione (GUID) della risorsa di AppInsights in questo campo.


Se si distribuiranno contenitori nel cluster, abilitare l'acquisizione delle statistiche Docker in Diagnostica di Microsoft Azure aggiungendo quanto segue a "WadCfg > DiagnosticMonitorConfiguration":

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},

```

## <a name="measuring-performance"></a>Misurazione delle prestazioni

La misurazione delle prestazioni del cluster consentirà di comprenderne la capacità di gestire il carico e prendere decisioni in merito al ridimensionamento del cluster. Vedere altre informazioni sul ridimensionamento di un cluster [in Azure](service-fabric-cluster-scale-up-down.md) o [in locale](service-fabric-cluster-windows-server-add-remove-nodes.md). I dati sulle prestazioni sono utili anche rispetto alle azioni eseguite dall'utente oppure dalle applicazioni e dai servizi, per la futura analisi dei log. 

Per un elenco dei contatori delle prestazioni da raccogliere quando si usa Service Fabric, vedere l'articolo relativo ai [contatori delle prestazioni in Service Fabric](service-fabric-diagnostics-event-generation-perf.md).

Di seguito sono illustrati due modi comuni disponibili per configurare la raccolta dei dati sulle prestazioni del cluster.

* Uso di un agente: è il modo preferito per raccogliere i dati sulle prestazioni da un computer, perché gli agenti includono in genere un elenco di possibili metriche delle prestazioni disponibili per la raccolta e scegliere o modificare le metriche da raccogliere è un processo relativamente semplice. Per altre informazioni sull'agente OMS, un agente di monitoraggio di questo tipo che può acquisire i dati sulle prestazioni delle VM del cluster e dei contenitori distribuiti, vedere gli articoli su [come configurare OMS per Service Fabric](service-fabric-diagnostics-event-analysis-oms.md) e sulla [configurazione dell'agente Windows di OMS](../log-analytics/log-analytics-windows-agents.md).

* Configurazione della diagnostica per la scrittura dei contatori delle prestazioni in una tabella: per i cluster in Azure, è necessario modificare la configurazione di Diagnostica di Azure in modo da acquisire i contatori delle prestazioni appropriati per le VM del cluster e, se verranno distribuiti contenitori, abilitare l'acquisizione delle statistiche Docker. Per configurare la raccolta dei contatori delle prestazioni, leggere l'articolo relativo alla configurazione dei [contatori delle prestazioni in Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) per Service Fabric.

## <a name="next-steps"></a>Passaggi successivi

Per poter essere inviati a qualsiasi piattaforma di analisi, i log e gli eventi devono prima essere aggregati. Per comprendere meglio alcune delle opzioni consigliate, vedere gli articoli relativi a [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md).
