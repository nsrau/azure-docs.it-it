---
title: Monitoraggio a livello piattaforma di Azure Service Fabric | Microsoft Docs
description: Informazioni sugli eventi e i log a livello piattaforma usati per il monitoraggio e la diagnosi dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 90ec06b01b11b5cbe119f41483eaf794af4e991b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243092"
---
# <a name="monitoring-the-cluster"></a>Monitoraggio del cluster

Il monitoraggio a livello di cluster è importante per determinare se l'hardware e il cluster presentino o meno il comportamento previsto. Service Fabric può mantenere in esecuzione le applicazioni durante un errore hardware, ma è comunque necessario diagnosticare se l'errore si sia verificato in un'applicazione o nell'infrastruttura sottostante. È consigliabile monitorare i cluster anche per pianificare in modo più efficiente la capacità e supportare le decisioni in merito all'aggiunta o alla rimozione dell'hardware.

Service Fabric espone diversi eventi di piattaforma strutturati, come [eventi di Service Fabric](service-fabric-diagnostics-events.md), tramite EventStore e diversi canali di log immediatamente disponibili. 

In Windows, gli eventi di Service Fabric sono disponibili da un unico provider ETW con un set di `logLevelKeywordFilters` pertinenti che consente di scegliere tra canali operativo e dati e messaggistica. È così che gli eventi di Service Fabric in uscita vengono separati per essere filtrati in base alle esigenze.

* **Operativo** Operazioni di alto livello eseguite da Service Fabric e dal cluster, ad esempio gli eventi per l'attivazione di un nodo, la distribuzione di una nuova applicazione, il ripristino dello stato precedente a un aggiornamento e così via. L'elenco completo degli eventi è disponibile [qui](service-fabric-diagnostics-event-generation-operational.md).  

* **Canale OPERATIVO - in dettaglio**  
Report sull'integrità e decisioni di bilanciamento del carico.

È possibile accedere al canale operativo in vari modi, ad esempio i registri eventi ETW/Windows ed [EventStore](service-fabric-diagnostics-eventstore.md), disponibile in Windows nella versione 6.2 e successive per i cluster di Windows. EventStore consente l'accesso agli eventi del cluster per singola entità (tra cui cluster, nodi, applicazioni, servizi, partizioni, repliche e contenitori) e li espone tramite API REST e la libreria client di Service Fabric. Usare EventStore per monitorare i cluster di sviluppo/test e per ottenere un riconoscimento temporizzato dello stato dei cluster di produzione.

* **Canale dati e messaggistica**  
Eventi e registri critici generati nella messaggistica (attualmente solo ReverseProxy) e nel percorso dati (modelli di servizi affidabili).

* **Canale data e messaggistica - in dettaglio**  
Canale dettagliato che contiene tutti i registri non critici di dati e messaggistica nel cluster. Questo canale presenta un volume di eventi molto elevato.

Oltre a questi, sono forniti due canali EventSource strutturati, nonché log che vengono raccolti a scopo di supporto.

* [Eventi di Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Eventi specifici del modello di programmazione.

* [Eventi di Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Contatori delle prestazioni ed eventi specifici del modello di programmazione.

* Registri di supporto  
Registri di sistema generati da Service Fabric e destinati esclusivamente a finalità di supporto.

Questi diversi canali coprono la maggior parte della registrazione consigliabile a livello piattaforma. Per migliorare la registrazione a livello piattaforma, valutare la possibilità di comprendere meglio il modello di integrità e aggiungere report sull'integrità personalizzati e **contatori delle prestazioni** personalizzati per ottenere una conoscenza in tempo reale dell'impatto dei servizi e delle applicazioni sul cluster.

Per sfruttare questi log, è consigliabile lasciare abilitata la diagnostica durante la creazione di un cluster. Attivando la diagnostica, durante la distribuzione del cluster Diagnostica di Microsoft Azure può riconoscere il canale operativo e quelli di Reliable Services e Reliable Actors e archiviare i dati come illustrato con maggiore dettaglio [Aggregare eventi con Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Creazione di report su integrità e carico di Azure Service Fabric

Service Fabric offre un modello di integrità specifico, descritto in dettaglio in questi articoli:

- [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)
- [Creare report e verificare l'integrità dei servizi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Aggiungere report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)
- [Come visualizzare i report sull'integrità di Service Fabric](service-fabric-view-entities-aggregated-health.md)

Il monitoraggio dell'integrità è fondamentale per molti aspetti dell'uso di un servizio, soprattutto durante l'aggiornamento di un'applicazione. Dopo l'aggiornamento, ogni dominio di aggiornamento del servizio deve superare i controlli di integrità prima che la distribuzione passi al dominio di aggiornamento successivo. Se non è possibile ottenere uno stato di integrità accettabile, viene eseguito il rollback della distribuzione, in modo che l'applicazione si trovi in uno stato positivo noto. Anche se è possibile che alcuni clienti subiscano interruzioni prima del rollback del servizio, la maggior parte dei clienti non riscontra problemi. La risoluzione, poi, è relativamente veloce, senza che sia necessario attendere l'intervento di un tecnico. Con un numero maggiore di controlli di integrità inclusi nel codice, il servizio sarà più resistente alle problematiche di distribuzione.

Un altro aspetto dell'integrità del servizio è dato dalla creazione di report sulle metriche del servizio. Le metriche sono importanti in Service Fabric perché vengono usate per bilanciare l'uso delle risorse. Possono anche costituire un indicatore dell'integrità del sistema. È ad esempio possibile che sia presente un'applicazione con molti servizi e che ogni istanza segnali una metrica relativa alle richieste al secondo. Se uno dei servizi usa più risorse rispetto a un altro, Service Fabric sposta le istanze del servizio nel cluster, provando a mantenere un uso bilanciato delle risorse stesse. Per una spiegazione più dettagliata del funzionamento dell'utilizzo delle risorse, vedere [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md).

Le metriche consentono anche di ottenere informazioni approfondite sulle prestazioni del servizio. Nel corso del tempo, è possibile usare le metriche per assicurarsi che il servizio stia operando entro i parametri previsti. Se, ad esempio, le tendenze indicano che alle 9:00 del lunedì mattina la media delle richieste al secondo è pari a 1.000, è possibile configurare un report di integrità che genera un avviso se le richieste al secondo sono inferiori a 500 o superiori a 1.500. È possibile che non si verifichino problemi, ma è comunque consigliabile controllare per assicurare la migliore esperienza possibile per i clienti. Il servizio può definire una serie di metriche di cui eseguire il report per l'integrità. Questo comunque non influirà sul bilanciamento delle risorse del cluster. Per ottenere questo risultato, impostare il peso delle metriche su zero. È consigliabile avviare tutte le metriche con peso zero e non aumentare il peso fino a quando non si è sicuri di aver compreso l'impatto della ponderazione sul bilanciamento delle risorse nel cluster.

> [!TIP]
> Non usare un numero eccessivo di metriche ponderate. Potrebbe essere difficile comprendere i motivi dello spostamento delle istanze del servizio per il bilanciamento del carico. Anche un numero ridotto di metriche può essere molto utile.

Tutte le informazioni che possono indicare l'integrità e le prestazioni dell'applicazione possono essere usate per report relativi alle metriche e all'integrità. **Un contatore delle prestazioni della CPU può indicare l'uso di un nodo ma non consente di capire se un servizio specifico sia integro, poiché in quel nodo possono essere in esecuzione più servizi.** Le metriche relative alle richieste al secondo, agli elementi elaborati e alla latenza delle richieste, tuttavia, possono indicare l'integrità di un servizio specifico.

## <a name="service-fabric-support-logs"></a>Log di supporto di Service Fabric

Se è necessario contattare il supporto Microsoft per assistenza con il cluster di Azure Service Fabric, saranno quasi sempre richiesti i log di supporto. Se il cluster è ospitato in Azure, questi log vengono automaticamente configurati e raccolti in fase di creazione di un cluster. I log vengono archiviati in un account di archiviazione dedicato nel gruppo di risorse del cluster. L'account di archiviazione non ha un nome predefinito, ma nell'account vengono visualizzati contenitori BLOB e tabelle con nomi che iniziano con *fabric*. Per informazioni sulla configurazione di raccolte di log per un cluster autonomo, vedere [Creare un cluster autonomo di Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) e [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md). Per istanze autonome di Service Fabric, i log devono essere inviati a una condivisione file locale. È **obbligatorio** disporre di questi log per ottenere assistenza. I log devono essere usati solo dal team di assistenza clienti Microsoft.

## <a name="measuring-performance"></a>Misurazione delle prestazioni

La misurazione delle prestazioni del cluster consentirà di comprenderne la capacità di gestire il carico e prendere decisioni in merito al ridimensionamento del cluster. Vedere altre informazioni sul ridimensionamento di un cluster [in Azure](service-fabric-cluster-scale-up-down.md) o [in locale](service-fabric-cluster-windows-server-add-remove-nodes.md). I dati sulle prestazioni sono utili anche rispetto alle azioni eseguite dall'utente oppure dalle applicazioni e dai servizi, per la futura analisi dei log. 

Per un elenco dei contatori delle prestazioni da raccogliere quando si usa Service Fabric, vedere l'articolo relativo ai [contatori delle prestazioni in Service Fabric](service-fabric-diagnostics-event-generation-perf.md).

Di seguito sono illustrati due modi comuni disponibili per configurare la raccolta dei dati sulle prestazioni del cluster.

* **Uso di un agente**  
Modo preferito per raccogliere dati sulle prestazioni da un computer, perché gli agenti includono in genere un elenco delle possibili metriche delle prestazioni disponibili per la raccolta e scegliere o modificare le metriche da raccogliere è un processo relativamente semplice. La lettura sul monitoraggio di Azure che offre monitoraggio di Azure log in Service Fabric [monitoraggio di Azure log integration](service-fabric-diagnostics-event-analysis-oms.md) e [configurare l'agente di Log Analitica](../log-analytics/log-analytics-windows-agent.md) per altre informazioni sull'agente di Log Analitica, che è una tale agente di monitoraggio che è in grado di prelevare i dati sulle prestazioni per macchine virtuali del cluster e dei contenitori distribuiti.

* **Contatori delle prestazioni per l'archiviazione tabelle di Azure**  
È anche possibile inviare metriche delle prestazioni alla stessa archiviazione tabelle degli eventi. Ciò richiede di modificare la configurazione di Diagnostica di Azure in modo che scelga i contatori delle prestazioni appropriati dalle VM del cluster e di abilitarla per l'acquisizione delle statistiche Docker, se si prevede di distribuire contenitori. Per configurare la raccolta dei contatori delle prestazioni, leggere l'articolo relativo alla configurazione dei [contatori delle prestazioni in Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) per Service Fabric.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su Service Fabric [monitoraggio di Azure log integration](service-fabric-diagnostics-event-analysis-oms.md) per raccogliere dati di diagnostica del cluster e creare query personalizzate e gli avvisi
* Informazioni sull'esperienza di diagnostica incorporata in Service Fabric, [EventStore](service-fabric-diagnostics-eventstore.md)
* Descrizione dettagliata di alcuni [scenari di diagnostica comuni](service-fabric-diagnostics-common-scenarios.md) in Service Fabric
