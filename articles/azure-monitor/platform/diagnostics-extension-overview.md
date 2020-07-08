---
title: Panoramica dell’estensione Diagnostica di Azure
description: Usare Diagnostica di Azure per debug, valutazione delle prestazioni, monitoraggio e analisi del traffico dei servizi cloud, delle macchine virtuali e di Service Fabric.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 5dcdfba6e8dd00c8ba09e5e98293a30d19e51c99
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83635963"
---
# <a name="azure-diagnostics-extension-overview"></a>Panoramica dell’estensione Diagnostica di Azure
L’estensione Diagnostica di Azure è un [agente Monitoraggio di Azure](agents-overview.md) che raccoglie i dati di monitoraggio dal sistema operativo guest delle risorse di elaborazione di Azure, comprese le macchine virtuali. Questo articolo offre una panoramica dell'estensione Diagnostica di Azure, comprese le funzionalità specifiche supportate e le opzioni di installazione e configurazione. 

> [!NOTE]
> L’estensione Diagnostica di Azure è uno degli agenti disponibili per la raccolta dei dati di monitoraggio dal sistema operativo guest delle risorse di elaborazione. Vedere [Panoramica degli agenti di Monitoraggio di Azure](agents-overview.md) per una descrizione dei diversi agenti e indicazioni sulla selezione degli agenti appropriati a seconda delle esigenze.

## <a name="primary-scenarios"></a>Scenari principali
Gli scenari principali in cui opera l'estensione di diagnostica sono:

- Raccolta delle metriche guest in Metriche di Monitoraggio di Azure.
- Invio delle metriche e dei log guest ad Archiviazione di Azure per l'archiviazione.
- Invio delle metriche e dei log guest agli hub eventi di Azure per l'invio all'esterno di Azure.


## <a name="comparison-to-log-analytics-agent"></a>Confronto con l’agente di Log Analytics
Anche l’agente di Log Analytics di Monitoraggio di Azure può essere usato per raccogliere i dati di monitoraggio dal sistema operativo guest delle macchine virtuali. È possibile scegliere di usare uno dei due agenti oppure entrambi, a seconda delle esigenze. Vedere [Panoramica degli agenti di Monitoraggio di Azure](agents-overview.md) per un confronto dettagliato degli agenti di Monitoraggio di Azure. 

Le differenze principali da considerare sono le seguenti:

- L'estensione Diagnostica di Azure può essere usata solo con le macchine virtuali di Azure. L'agente di Log Analytics può essere usato con le macchine virtuali di Azure, in altri cloud e in locale.
- L'estensione Diagnostica di Azure invia i dati ad Archiviazione di Azure, alle [metriche di Monitoraggio di Azure](data-platform-metrics.md) (solo Windows) e a Hub eventi. L'agente di Log Analytics raccoglie i dati da inviare ai [log di Monitoraggio di Azure](data-platform-logs.md).
- L'agente di Log Analytics è necessario per [soluzioni](../monitor-reference.md#insights-and-core-solutions), [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) e altri servizi, ad esempio [Centro sicurezza di Azure](/azure/security-center/).

## <a name="costs"></a>Costi
Non sono previsti costi per l'estensione Log Analytics, ma è possibile che vengano addebitati costi per i dati inseriti. Controllare i [prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) in base alla destinazione in cui si intende raccogliere i dati.

## <a name="data-collected"></a>Dati raccolti
Le tabelle seguenti elencano i dati che possono essere raccolti dall'estensione di diagnostica Windows e Linux.

### <a name="windows-diagnostics-extension-wad"></a>Estensione di diagnostica Windows (WAD)

| origine dati | Descrizione |
| --- | --- |
| Log eventi di Windows   | Eventi dai log eventi di Windows. |
| Contatori delle prestazioni | Valori numerici che misurano le prestazioni di diversi aspetti del sistema operativo e dei carichi di lavoro. |
| Log IIS             | Informazioni sull'utilizzo per siti Web IIS in esecuzione nel sistema operativo guest. |
| Log applicazioni     | Messaggi di traccia scritti dall'applicazione usata. |
| Log EventSource .NET |Eventi di scrittura di codice con la classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) di .NET |
| [Log ETW basati su manifesto](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Traccia di eventi per eventi Windows generati da qualsiasi processo. |
| Dump di arresto anomalo (log)   | Informazioni sullo stato del processo in caso di arresto anomalo di un'applicazione. |
| Log basati su file    | Log creati dall'applicazione o dal servizio. |
| Log di diagnostica dell’agente | Informazioni su Diagnostica di Azure. |


### <a name="linux-diagnostics-extension-lad"></a>Estensione di diagnostica Linux (LAD)

| origine dati | Descrizione |
| --- | --- |
| syslog | Eventi inviati al sistema di registrazione eventi di Linux.   |
| Contatori delle prestazioni  | Valori numerici che misurano le prestazioni di diversi aspetti del sistema operativo e dei carichi di lavoro. |
| File di log | Voci inviate a un log basato su file.  |

## <a name="data-destinations"></a>Destinazioni dei dati
L'estensione Diagnostica di Azure per Windows e Linux raccoglie sempre i dati in un account di Archiviazione di Azure. Per un elenco di tabelle e BLOB specifici in cui i dati vengono raccolti, vedere [Installare e configurare l’estensione Diagnostica di Azure per Windows (WAD)](diagnostics-extension-windows-install.md) e [Usare l’estensione di diagnostica di Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md).

Configurare uno o più *sink di dati* per inviare i dati ad altre destinazioni aggiuntive. Le sezioni seguenti elencano i sink disponibili per l'estensione diagnostica Windows e Linux.

### <a name="windows-diagnostics-extension-wad"></a>Estensione di diagnostica Windows (WAD)

| Destination | Descrizione |
|:---|:---|
| Metriche di Monitoraggio di Azure | Raccogliere i dati sulle prestazioni in Metriche di Monitoraggio di Azure. Vedere [Inviare le metriche del sistema operativo guest al database delle metriche di Monitoraggio di Azure](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Hub eventi | Usare Hub eventi di Azure per inviare dati all'esterno di Azure. Vedere [Inviare i dati di Diagnostica di Azure in streaming a Hub eventi](diagnostics-extension-stream-event-hubs.md) |
| BLOB del servizio di archiviazione di Azure | Scrivere i dati nei BLOB in Archiviazione di Azure oltre che nelle tabelle. |
| Application Insights | Raccogliere i dati dalle applicazioni in esecuzione nella macchina virtuale in Application Insights per integrarli con i dati di monitoraggio di altre applicazioni. Vedere [Inviare i dati di diagnostica ad Application Insights](diagnostics-extension-to-application-insights.md). |

È anche possibile raccogliere dati WAD dalla risorsa di archiviazione in un'area di lavoro Log Analytics per analizzarli con i log di Monitoraggio di Azure, anche se in genere si usa l'agente di Log Analytics per questo compito. Questa soluzione può inviare i dati direttamente a un'area di lavoro Log Analytics e supporta soluzioni e informazioni dettagliate che forniscono funzionalità aggiuntive.  Vedere [Raccogliere i log di diagnostica da Archiviazione di Azure](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Estensione di diagnostica Linux (LAD)
L’estensione LAD scrive i dati in tabelle in Archiviazione di Azure. Supporta i sink nella tabella seguente.

| Destination | Descrizione |
|:---|:---|
| Hub eventi | Usare Hub eventi di Azure per inviare dati all'esterno di Azure. |
| BLOB del servizio di archiviazione di Azure | Scrivere i dati nei BLOB in Archiviazione di Azure oltre che nelle tabelle. |
| Metriche di Monitoraggio di Azure | Installare l'agente Telegraf oltre a LAD. Vedere [Raccogliere metriche personalizzate per una VM Linux con l’agente InfluxData](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Installazione e configurazione
L'estensione Diagnostica viene implementata come [estensione della macchina virtuale](../../virtual-machines/extensions/overview.md) in Azure, pertanto supporta le stesse opzioni di installazione usando modelli di Gestione risorse, PowerShell e l'interfaccia della riga di comando. Per informazioni dettagliate sull'installazione e la gestione delle estensioni di macchina virtuale, vedere [Estensioni e funzionalità della macchina virtuale per Windows](../../virtual-machines/extensions/features-windows.md) e [Estensioni e funzionalità della macchina virtuale per Linux](../../virtual-machines/extensions/features-linux.md).

È anche possibile installare e configurare l'estensione di diagnostica Windows e Linux nel portale di Azure in **Impostazioni di diagnostica** nella sezione **Monitoraggio** del menu della macchina virtuale.

Per informazioni dettagliate sull'installazione e la configurazione dell'estensione di diagnostica per Windows e Linux, vedere gli articoli seguenti.

- [Installare e configurare l'estensione Diagnostica di Azure per Windows (WAD)](diagnostics-extension-windows-install.md)
- [Usare l'estensione Diagnostica per Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Altra documentazione

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Ruoli di lavoro e Web di Servizio cloud di Azure (classico)
- [Presentazione del monitoraggio del servizio cloud](../../cloud-services/cloud-services-how-to-monitor.md)
- [Abilitazione di Diagnostica di Azure in Servizi cloud di Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights per i servizi cloud di Azure](../app/cloudservices.md)<br>[Tracciare il flusso di un'applicazione di Servizi cloud con Diagnostica di Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Passaggi successivi


* Informazioni su come [usare i contatori delle prestazioni in Diagnostica di Azure](../../cloud-services/diagnostics-performance-counters.md).
* In caso di problemi nell'avvio della diagnostica o nell'individuazione dei dati nelle tabelle di archiviazione di Azure, vedere [Risoluzione dei problemi di Diagnostica di Azure](diagnostics-extension-troubleshooting.md)

