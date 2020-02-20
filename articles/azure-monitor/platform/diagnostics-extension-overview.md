---
title: Panoramica sull'estensione Diagnostica di Azure
description: Usare Diagnostica di Azure per debug, valutazione delle prestazioni, monitoraggio e analisi del traffico dei servizi cloud, delle macchine virtuali e di Service Fabric.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: d9db4b4c8e6d82f29d227b9f8afe528e000c651e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467998"
---
# <a name="azure-diagnostics-extension-overview"></a>Panoramica sull'estensione Diagnostica di Azure
Diagnostica di Azure estensione è un [agente di monitoraggio di Azure](agents-overview.md) che raccoglie i dati di monitoraggio dal sistema operativo guest delle risorse di calcolo di Azure, incluse le macchine virtuali. Questo articolo fornisce una panoramica dell'estensione Diagnostica di Azure, incluse funzionalità specifiche che supporta e opzioni per l'installazione e la configurazione. 

> [!NOTE]
> Diagnostica di Azure estensione è uno degli agenti disponibili per raccogliere i dati di monitoraggio dal sistema operativo guest delle risorse di calcolo. Vedere [Panoramica degli agenti di monitoraggio di Azure](agents-overview.md) per una descrizione dei diversi agenti e informazioni aggiuntive sulla selezione degli agenti appropriati per le proprie esigenze.

## <a name="comparison-to-log-analytics-agent"></a>Confronto con Log Analytics Agent
L'agente di Log Analytics in monitoraggio di Azure può essere usato anche per raccogliere i dati di monitoraggio dal sistema operativo guest delle macchine virtuali. È possibile scegliere di usare uno dei due o entrambi, a seconda dei requisiti. Per un confronto dettagliato degli agenti di monitoraggio di Azure, vedere [Panoramica degli agenti di monitoraggio di Azure](agents-overview.md) . 

Le differenze principali da considerare sono:

- Diagnostica di Azure estensione può essere usata solo con le macchine virtuali di Azure. L'agente di Log Analytics può essere usato con macchine virtuali in Azure, in altri cloud e in locale.
- Diagnostica di Azure estensione invia dati ad archiviazione di Azure, [metriche di monitoraggio di Azure](data-platform-metrics.md) (solo Windows) e hub eventi. L'agente di Log Analytics raccoglie i dati nei [log di monitoraggio di Azure](data-platform-logs.md).
- L'agente di Log Analytics è necessario per le [soluzioni](../monitor-reference.md#insights-and-core-solutions), [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md)e altri servizi, ad esempio il [Centro sicurezza di Azure](/azure/security-center/).

## <a name="costs"></a>Costi
Non è previsto alcun costo per l'estensione diagnostica di Azure, ma è possibile che vengano addebitati i costi per i dati inseriti. Controllare i [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) per la destinazione in cui si stanno raccogliendo i dati.

## <a name="data-collected"></a>Dati raccolti
Le tabelle seguenti elencano i dati che possono essere raccolti dall'estensione diagnostica Windows e Linux.

### <a name="windows-diagnostics-extension-wad"></a>Estensione di diagnostica Windows (WAD)

| Origine dati | Descrizione |
| --- | --- |
| Log eventi di Windows   | Eventi del registro eventi di Windows. |
| Contatori delle prestazioni | Valori numerici che misurano le prestazioni di diversi aspetti del sistema operativo e dei carichi di lavoro. |
| Log di IIS             | Informazioni sull'utilizzo per i siti Web IIS in esecuzione nel sistema operativo guest. |
| Log applicazioni     | Messaggi di traccia scritti dall'applicazione. |
| Log EventSource .NET |Eventi di scrittura di codice con la classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) di .NET |
| [Log ETW basati su manifesto](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Event Tracing for Windows eventi generati da un processo. |
| Dump di arresto anomalo (log)   | Informazioni sullo stato del processo in caso di arresto anomalo di un'applicazione. |
| Log basati su file    | Log creati dall'applicazione o dal servizio. |
| Log di diagnostica dell'agente | Informazioni su Diagnostica di Azure stesso. |


### <a name="linux-diagnostics-extension-lad"></a>Estensione di diagnostica Linux (LAD)

| Origine dati | Descrizione |
| --- | --- |
| Syslog | Eventi inviati al sistema di registrazione eventi di Linux.   |
| Contatori delle prestazioni  | Valori numerici che misurano le prestazioni di diversi aspetti del sistema operativo e dei carichi di lavoro. |
| File di log | Voci inviate a un log basato su file.  |

## <a name="data-destinations"></a>Destinazioni dei dati
L'estensione diagnostica di Azure per Windows e Linux raccoglie sempre i dati in un account di archiviazione di Azure. Vedere [installare e configurare l'estensione diagnostica di Microsoft Azure (WAD)](diagnostics-extension-windows-install.md) e [usare l'estensione di diagnostica Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md) per un elenco di tabelle e BLOB specifici in cui vengono raccolti questi dati.

Configurare uno o più *sink di dati* per l'invio di dati ad altre destinazioni aggiuntive. Le sezioni seguenti elencano i sink disponibili per l'estensione diagnostica Windows e Linux.

### <a name="windows-diagnostics-extension-wad"></a>Estensione di diagnostica Windows (WAD)

| Destination | Descrizione |
|:---|:---|
| Metriche di monitoraggio di Azure | Raccogliere i dati sulle prestazioni nelle metriche di monitoraggio di Azure. Vedere [inviare metriche del sistema operativo guest al database delle metriche di monitoraggio di Azure](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Hub eventi | Usare hub eventi di Azure per inviare dati all'esterno di Azure. Vedere [trasmettere dati diagnostica di Azure a hub eventi](diagnostics-extension-stream-event-hubs.md) |
| BLOB del servizio di archiviazione di Azure | Scrivere i dati nei BLOB in archiviazione di Azure, oltre alle tabelle. |
| Application Insights | Raccogliere i dati dalle applicazioni in esecuzione nella macchina virtuale per Application Insights l'integrazione con altre applicazioni di monitoraggio. Vedere [inviare dati di diagnostica a Application Insights](diagnostics-extension-to-application-insights.md). |

È anche possibile raccogliere dati WAD dalla risorsa di archiviazione in un'area di lavoro Log Analytics per analizzarli con i log di monitoraggio di Azure anche se l'agente Log Analytics viene in genere usato per questa funzionalità. Può inviare dati direttamente a un'area di lavoro di Log Analytics e supportare soluzioni e informazioni dettagliate che forniscono funzionalità aggiuntive.  Vedere [raccogliere i log di diagnostica di Azure da archiviazione di Azure](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Estensione di diagnostica Linux (LAD)
LAD scrive i dati nelle tabelle in archiviazione di Azure. Supporta i sink nella tabella seguente.

| Destination | Descrizione |
|:---|:---|
| Hub eventi | Usare hub eventi di Azure per inviare dati all'esterno di Azure. |
| BLOB del servizio di archiviazione di Azure | Scrivere i dati nei BLOB in archiviazione di Azure, oltre alle tabelle. |
| Metriche di monitoraggio di Azure | Installare l'agente Telegraf oltre a LAD. Vedere [raccogliere metriche personalizzate per una VM Linux con l'agente Telegraf InfluxData](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Installazione e configurazione
L'estensione di diagnostica viene implementata come [estensione della macchina virtuale](/virtual-machines/extensions/overview) in Azure, pertanto supporta le stesse opzioni di installazione usando modelli di gestione risorse, PowerShell e l'interfaccia della riga di comando. Per informazioni generali sull'installazione e la gestione delle estensioni delle macchine virtuali, vedere [estensioni e funzionalità delle macchine virtuali per Windows](/virtual-machines/extensions/features-windows) e le [estensioni e le funzionalità delle macchine virtuali per Linux](/virtual-machines/extensions/features-linux) .

È anche possibile installare e configurare l'estensione di diagnostica Windows e Linux nel portale di Azure in **impostazioni di diagnostica** nella sezione **monitoraggio** del menu della macchina virtuale.

Per informazioni dettagliate sull'installazione e la configurazione dell'estensione di diagnostica per Windows e Linux, vedere gli articoli seguenti.

- [Installare e configurare l'estensione diagnostica di Microsoft Azure (WAD)](diagnostics-extension-windows-install.md)
- [Usare l'estensione di diagnostica Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Altra documentazione

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Ruoli Web e di lavoro del servizio cloud di Azure (versione classica)
- [Introduzione al monitoraggio del servizio cloud](../../cloud-services/cloud-services-how-to-monitor.md)
- [Abilitazione di Diagnostica di Azure nei servizi cloud di Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights per i servizi cloud di Azure](../app/cloudservices.md)<br>[Tracciare il flusso di un'applicazione di Servizi cloud con Diagnostica di Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Passaggi successivi


* Informazioni su come [usare i contatori delle prestazioni in Diagnostica di Azure](../../cloud-services/diagnostics-performance-counters.md).
* In caso di problemi nell'avvio della diagnostica o nell'individuazione dei dati nelle tabelle di archiviazione di Azure, vedere [Risoluzione dei problemi di Diagnostica di Azure](diagnostics-extension-troubleshooting.md)

