---
title: Panoramica dell'estensione Diagnostica di AzureAzure Diagnostics extension overview
description: Usare Diagnostica di Azure per debug, valutazione delle prestazioni, monitoraggio e analisi del traffico dei servizi cloud, delle macchine virtuali e di Service Fabric.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672379"
---
# <a name="azure-diagnostics-extension-overview"></a>Panoramica dell'estensione Diagnostica di AzureAzure Diagnostics extension overview
L'estensione Diagnostica di Azure è un [agente in Monitoraggio di Azure](agents-overview.md) che raccoglie i dati di monitoraggio dal sistema operativo guest delle risorse di calcolo di Azure, incluse le macchine virtuali. Questo articolo offre una panoramica dell'estensione Diagnostica di Azure, che include funzionalità specifiche supportate e opzioni per l'installazione e la configurazione. 

> [!NOTE]
> L'estensione Diagnostica di Azure è uno degli agenti disponibili per raccogliere dati di monitoraggio dal sistema operativo guest delle risorse di calcolo. Vedere [Panoramica degli agenti di Monitoraggio di Azure](agents-overview.md) per una descrizione dei diversi agenti e indicazioni sulla selezione degli agenti appropriati per i requisiti.

## <a name="comparison-to-log-analytics-agent"></a>Confronto con l'agente di Log Analytics
L'agente Log Analytics in Monitoraggio di Azure può essere usato anche per raccogliere dati di monitoraggio dal sistema operativo guest delle macchine virtuali. È possibile scegliere di utilizzare uno o entrambi a seconda delle proprie esigenze. Per un confronto dettagliato degli agenti di Monitoraggio di Azure, vedere [Panoramica degli agenti di Monitoraggio](agents-overview.md) di Azure.See Overview of the Azure Monitor agents for a detailed comparison of the Azure Monitor agents. 

Le principali differenze da considerare sono:

- L'estensione Diagnostica di Azure può essere usata solo con le macchine virtuali di Azure.Azure Diagnostics Extension can be used only with Azure virtual machines. L'agente Log Analytics può essere usato con macchine virtuali in Azure, altri cloud e in locale.
- L'estensione Diagnostica di Azure invia i dati ad Archiviazione di Azure, [metriche di Monitoraggio di Azure](data-platform-metrics.md) (solo Windows) e Hub eventi. L'agente di Log Analytics raccoglie dati ai log di Monitoraggio di [Azure.](data-platform-logs.md)
- L'agente Log Analytics è necessario per [le soluzioni](../monitor-reference.md#insights-and-core-solutions), [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md)e altri servizi, ad esempio Centro sicurezza di [Azure.](/azure/security-center/)

## <a name="costs"></a>Costi
L'estensione di diagnostica di Azure non ha alcun costo, ma è possibile che siano addebitati costi per i dati ingeriti. Controllare [i prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) per la destinazione in cui si raccolgono i dati.

## <a name="data-collected"></a>Dati raccolti
Le tabelle seguenti elencano i dati che possono essere raccolti dall'estensione di diagnostica di Windows e Linux.The following tables list the data that can be collected by the Windows and Linux diagnostics extension.

### <a name="windows-diagnostics-extension-wad"></a>Estensione diagnostica Windows (WAD)

| origine dati | Descrizione |
| --- | --- |
| Log eventi di Windows   | Eventi dal registro eventi di Windows. |
| Contatori delle prestazioni | Valori numerici che misurano le prestazioni di diversi aspetti del sistema operativo e dei carichi di lavoro. |
| Log IIS             | Informazioni sull'utilizzo per i siti Web IIS in esecuzione nel sistema operativo guest. |
| Log applicazioni     | Tenere traccia dei messaggi scritti dall'applicazione. |
| Log EventSource .NET |Eventi di scrittura di codice con la classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) di .NET |
| [Log ETW basati su manifesto](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Traccia eventi per gli eventi di Windows generati da qualsiasi processo. |
| Dump di arresto anomalo (log)   | Informazioni sullo stato del processo in caso di arresto anomalo di un'applicazione. |
| Registri basati su file    | Log creati dall'applicazione o dal servizio. |
| Registri diagnostici dell'agente | Informazioni sulla diagnostica di Azure stessa. |


### <a name="linux-diagnostics-extension-lad"></a>Estensione diagnostica Linux (LAD)

| origine dati | Descrizione |
| --- | --- |
| syslog | Eventi inviati al sistema di registrazione degli eventi Linux.   |
| Contatori delle prestazioni  | Valori numerici che misurano le prestazioni di diversi aspetti del sistema operativo e dei carichi di lavoro. |
| File di log | Voci inviate a un registro basato su file.  |

## <a name="data-destinations"></a>Destinazioni dei dati
L'estensione Diagnostica di Azure per Windows e Linux raccoglie sempre i dati in un account di archiviazione di Azure.The Azure Diagnostic extension for both Windows and Linux always collect data into an Azure Storage account. Vedere [Installare e configurare l'estensione](diagnostics-extension-windows-install.md) di diagnostica di Windows Azure (WAD) e [Usare l'estensione di diagnostica Linux per monitorare metriche e log](../../virtual-machines/extensions/diagnostics-linux.md) per un elenco di tabelle e BLOB specifici in cui vengono raccolti questi dati.

Configurare uno o più *sink di dati* per l'invio di dati ad altre destinazioni aggiuntive. Le sezioni seguenti elencano i sink disponibili per l'estensione di diagnostica Windows e Linux.The following sections list the sinks available for the Windows and Linux diagnostics extension.

### <a name="windows-diagnostics-extension-wad"></a>Estensione diagnostica Windows (WAD)

| Destination | Descrizione |
|:---|:---|
| Metriche di Monitoraggio di AzureAzure Monitor Metrics | Raccogliere i dati sulle prestazioni nelle metriche di Monitoraggio di Azure.Collect performance data to Azure Monitor Metrics. Vedere [Inviare metriche del sistema operativo guest al database delle metriche](collect-custom-metrics-guestos-resource-manager-vm.md)di Monitoraggio di Azure.  |
| Hub eventi | Usare Hub eventi di Azure per inviare dati all'esterno di Azure.Use Azure Event Hubs to send data outside of Azure. Vedere Streaming dei dati di [Diagnostica di Azure in Hub eventi](diagnostics-extension-stream-event-hubs.md) |
| BLOB del servizio di archiviazione di Azure | Scrivere nei dati nei BLOB in Archiviazione di Azure oltre alle tabelle. |
| Application Insights | Raccogliere dati dalle applicazioni in esecuzione nella macchina virtuale in Application Insights per l'integrazione con altri monitoraggio delle applicazioni. Vedere [Inviare dati di diagnostica ad Application Insights](diagnostics-extension-to-application-insights.md). |

È anche possibile raccogliere dati WAD dall'archiviazione in un'area di lavoro di Log Analytics per analizzarli con i log di Monitoraggio di Azure anche se l'agente di Log Analytics viene in genere usato per questa funzionalità. Può inviare dati direttamente a un'area di lavoro di Log Analytics e supporta soluzioni e informazioni dettagliate che forniscono funzionalità aggiuntive.  Vedere Raccogliere log di diagnostica di [Azure da Archiviazione di Azure.See Collect Azure diagnostic logs from Azure Storage.](diagnostics-extension-logs.md) 


### <a name="linux-diagnostics-extension-lad"></a>Estensione diagnostica Linux (LAD)
LAD writes data to tables in Azure Storage. Supporta i sink nella tabella seguente.

| Destination | Descrizione |
|:---|:---|
| Hub eventi | Usare Hub eventi di Azure per inviare dati all'esterno di Azure.Use Azure Event Hubs to send data outside of Azure. |
| BLOB del servizio di archiviazione di Azure | Scrivere nei dati nei BLOB in Archiviazione di Azure oltre alle tabelle. |
| Metriche di Monitoraggio di AzureAzure Monitor Metrics | Installare l'agente Telegraf oltre a LAD. Vedere [Raccogliere metriche personalizzate per una macchina virtuale Linux con l'agente InfluxData Telegraf.](collect-custom-metrics-linux-telegraf.md)


## <a name="installation-and-configuration"></a>Installazione e configurazione
L'estensione Diagnostica viene implementata come estensione di macchina virtuale in Azure, pertanto supporta le stesse opzioni di installazione usando i modelli di Resource Manager, PowerShell e [l'interfaccia](../../virtual-machines/extensions/overview.md) della riga di comando. Per informazioni generali sull'installazione e la gestione delle estensioni delle macchine virtuali, [vedere Estensioni e funzionalità](../../virtual-machines/extensions/features-windows.md) delle macchine virtuali per Windows e le estensioni e funzionalità delle macchine [virtuali.](../../virtual-machines/extensions/features-linux.md)

È anche possibile installare e configurare l'estensione di diagnostica Di Windows e Linux nel portale di Azure in Impostazioni di **diagnostica** nella sezione **Monitoraggio** del menu della macchina virtuale.

Vedere gli articoli seguenti per informazioni dettagliate sull'installazione e la configurazione dell'estensione di diagnostica per Windows e Linux.See the following articles for details on installing and configuring the diagnostics extension for Windows and Linux.

- [Installare e configurare l'estensione di diagnostica di Windows Azure (WAD)Install and configure Windows Azure diagnostics extension (WAD)](diagnostics-extension-windows-install.md)
- [Usare l'estensione Diagnostica per Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Altra documentazione

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Ruoli Web e di lavoro del servizio cloud di AzureAzure Cloud Service (classic) Web and Worker Roles
- [Presentazione del monitoraggio del servizio cloud](../../cloud-services/cloud-services-how-to-monitor.md)
- [Abilitazione di Diagnostica di Azure in servizi cloud di Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights per i servizi cloud di AzureApplication Insights for Azure cloud services](../app/cloudservices.md)<br>[Tracciare il flusso di un'applicazione di Servizi cloud con Diagnostica di Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorare e diagnosticare i servizi in una configurazione di sviluppo del computer localeMonitor and diagnose services in a local machine development setup](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Passaggi successivi


* Informazioni su come [usare i contatori delle prestazioni in Diagnostica di Azure](../../cloud-services/diagnostics-performance-counters.md).
* In caso di problemi nell'avvio della diagnostica o nell'individuazione dei dati nelle tabelle di archiviazione di Azure, vedere [Risoluzione dei problemi di Diagnostica di Azure](diagnostics-extension-troubleshooting.md)

