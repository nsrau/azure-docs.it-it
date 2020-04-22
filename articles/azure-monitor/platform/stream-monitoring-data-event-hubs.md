---
title: Trasmettere i dati di monitoraggio di Azure a un hub eventi
description: Informazioni su come trasmettere i dati di monitoraggio di Azure a un hub eventi per ottenere i dati in uno strumento SIEM o di analisi del partner.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 32bc90cc069ac82641c3aa7692c900c60db7ba87
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733094"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Trasmettere i dati di monitoraggio di Azure a un hub eventi
Monitoraggio di Azure offre una soluzione completa di monitoraggio dello stack completo per applicazioni e servizi in Azure, in altri cloud e in locale. Oltre a usare Monitoraggio di Azure per analizzare i dati e sfruttarli per scenari di monitoraggio diversi, potrebbe essere necessario inviarli ad altri strumenti di monitoraggio nell'ambiente. Il metodo più efficace per trasmettere i dati di monitoraggio a strumenti esterni nella maggior parte dei casi consiste nell'usare Hub eventi di Azure.The most effective method to stream monitoring data to external tools in most cases is using [Azure Event Hubs](/azure/event-hubs/). Questo articolo fornisce una breve descrizione su come è possibile trasmettere i dati di monitoraggio da origini diverse a un hub eventi e collegamenti a indicazioni dettagliate.


## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Prima di configurare il flusso per qualsiasi origine dati, è necessario creare uno spazio dei nomi e un [hub eventi di Hub eventi.](../../event-hubs/event-hubs-create.md) Lo spazio dei nomi e l'hub eventi sono la destinazione di tutti i dati di monitoraggio. Uno spazio dei nomi di Hub eventi è un raggruppamento logico di hub eventi che condividono gli stessi criteri di accesso, così come un account di archiviazione contiene al suo interno singoli BLOB. Considerare i dettagli seguenti sullo spazio dei nomi degli hub eventi e sugli hub eventi utilizzati per il monitoraggio del flusso dei dati:Consider the following details about the event hubs namespace and event hubs that you use for streaming monitoring data:

* Il numero di unità elaborate consente di aumentare la scala della velocità effettiva per gli hub eventi. In genere è necessaria una sola unità di velocità effettiva. Se è necessario aumentare le dimensioni con l'aumentare dell'utilizzo del log, è possibile aumentare manualmente il numero di unità di velocità effettiva per lo spazio dei nomi o abilitare l'inflazione automatica.
* Il numero di partizioni consente di parallelizzare il consumo tra molti consumer. Una singola partizione può supportare fino a 20 MBps o circa 20.000 messaggi al secondo. A seconda dello strumento che utilizza i dati, può essere supportato o meno il consumo di dati di più partizioni. Quattro partizioni è ragionevole iniziare se non si è sicuri se non si è sicuri circa il numero di partizioni da impostare.
* Impostare la conservazione dei messaggi nell'hub eventi su almeno 7 giorni. Se il vostro strumento di consumo va giù per più di un giorno, questo assicura che lo strumento può riprendere da dove si era interrotto per eventi fino a 7 giorni.
* È consigliabile usare il gruppo di consumer predefinito per l'hub eventi. Non è necessario creare altri gruppi di consumer o usare un gruppo di consumer distinto, a meno che non siano previsti due diversi strumenti che utilizzano gli stessi dati dello stesso hub eventi.
* Per il log attività di Azure, si seleziona uno spazio dei nomi Hub eventi e Monitoraggio di Azure crea un hub eventi all'interno di tale spazio dei nomi denominato _insights-logs-operational-logs_. Per altri tipi di log, è possibile scegliere un hub eventi esistente o fare in modo che Monitoraggio hub di Azure crei un hub eventi per categoria di log.
* Le porte in uscita 5671 e 5672 devono in genere essere aperte nel computer o nella rete Virtuale virtuale utilizzando i dati dell'hub eventi.

## <a name="monitoring-data-available"></a>Dati di monitoraggio disponibili
[Origini dei dati di monitoraggio per Monitoraggio di Azure](data-sources.md) descrive i diversi livelli di dati per le applicazioni di Azure e i tipi di dati di monitoraggio disponibili per ognuno. Nella tabella seguente sono elencati ognuno di questi livelli e una descrizione della modalità di flusso dei dati a un hub eventi. Seguire i collegamenti forniti per ulteriori dettagli.

| Livello | Data | Metodo |
|:---|:---|:---|
| [Tenant di Azure](data-sources.md#azure-tenant) | Log di controllo di Azure Active DirectoryAzure Active Directory audit logs | Configurare un'impostazione di diagnostica tenant nel tenant di AAD. Per informazioni dettagliate, vedere [Esercitazione: Trasmettere i log di Azure Active Directory a un hub eventi di Azure.See Tutorial: Stream Azure Active Directory logs to an Azure event hub for](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) details. |
| [Sottoscrizione di Azure](data-sources.md#azure-subscription) | Azure Activity Log | Creare un profilo di log per esportare gli eventi del log attività negli hub eventi.  Per informazioni dettagliate, vedere Trasmettere in streaming i log della piattaforma Azure agli hub eventi di [Azure.See Stream Azure platform logs to Azure Event Hubs](resource-logs-stream-event-hubs.md) for details. |
| [Risorse di Azure](data-sources.md#azure-resources) | Metriche della piattaforma<br> Log risorse |Entrambi i tipi di dati vengono inviati a un hub eventi tramite un'impostazione di diagnostica delle risorse. Per informazioni dettagliate, vedere Eseguire lo streaming dei log delle risorse di [Azure in un hub eventi.](resource-logs-stream-event-hubs.md) |
| [Sistema operativo (ospite)](data-sources.md#operating-system-guest) | Macchine virtuali di Azure | Installare [l'estensione Diagnostica di Azure](diagnostics-extension-overview.md) nelle macchine virtuali Windows e Linux in Azure.Install the Azure Diagnostics Extension on Windows and Linux virtual machines in Azure. Per [Streaming Azure Diagnostics data in the hot path by using Event Hubs](diagnostics-extension-stream-event-hubs.md) informazioni dettagliate sulle macchine virtuali Windows e [Usare l'estensione di diagnostica Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) per informazioni dettagliate sulle macchine virtuali Linux. |
| [Codice dell'applicazione](data-sources.md#application-code) | Application Insights | Application Insights non fornisce un metodo diretto per trasmettere i dati agli hub eventi. È possibile [configurare l'esportazione continua](../../azure-monitor/app/export-telemetry.md) dei dati di Application Insights in un account di archiviazione e quindi usare un'app per la logica per inviare i dati a un hub eventi come descritto in [Streaming manuale con app per](#manual-streaming-with-logic-app)la logica . |

## <a name="manual-streaming-with-logic-app"></a>Streaming manuale con App per la logica
Per i dati che non è possibile trasmettere direttamente a un hub eventi, è possibile scrivere nell'archiviazione di Azure e quindi usare un'app per la logica con attivazione temporale che [estrae i dati dall'archiviazione BLOB](../../connectors/connectors-create-api-azureblobstorage.md#add-action) e [li invia come messaggio all'hub eventi.](../../connectors/connectors-create-api-azure-event-hubs.md#add-action) 


## <a name="partner-tools-with-azure-monitor-integration"></a>Strumenti partner con l'integrazione di Azure Monitor

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrarsi facilmente con SIEM esterni e strumenti di monitoraggio. Di seguito sono riportati alcuni esempi di strumenti con l'integrazione di Monitoraggio di Azure:Examples of tools with Azure Monitor integration include the following:

| Strumento | Ospitato in Azure | Descrizione |
|:---|:---| :---|
|  IBM QRadar | No | Microsoft Azure DSM e Microsoft Azure Event Hub Protocol sono scaricabili dal [sito Web del supporto IBM](https://www.ibm.com/support). Per altre informazioni sull'integrazione con Azure, vedere [QRadar DSM configuration](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | No | Il componente aggiuntivo Monitor di Azure per Splunk è un progetto open source disponibile in Splunkbase.The [Azure Monitor Add-On for Splunk](https://splunkbase.splunk.com/app/3534/) is an open source project available in Splunkbase. La documentazione è disponibile in [Azure Monitor Addon For Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Se non è possibile installare un componente aggiuntivo nell'istanza di Splunk, ad esempio si usa un proxy o si esegue splunk Cloud, è possibile inoltrare questi eventi all'agente di raccolta eventi HTTP di Splunk usando [la funzione di Azure per Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), che viene attivata dai nuovi messaggi nell'hub eventi. |
| sumologic | No | Le istruzioni per la configurazione di SumoLogic per l'utilizzo dei dati da un hub eventi sono disponibili in [Raccogli log per l'app di controllo di Azure dall'hub eventi](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | No | Lo smart connector ArcSight Azure Event Hub è disponibile come parte della raccolta smart [connector ArcSight.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Server Syslog | No | Se si desidera trasmettere i dati di Monitoraggio di Azure direttamente in un server syslog, è possibile usare una [soluzione basata su](https://github.com/miguelangelopereira/azuremonitor2syslog/)una funzione di Azure.
| LogRhythm | No| Le istruzioni per configurare LogRhythm per raccogliere i log da un hub eventi sono disponibili [qui](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Sì | Per altre informazioni, vedere Introduzione al monitoraggio e alla [registrazione usando Logz.io per le app Java in esecuzione in AzureFor more information, see Getting started with monitoring and logging using Logz.io for Java apps running on Azure](https://docs.microsoft.com/azure/developer/java/fundamentals/java-get-started-with-logzio)


## <a name="next-steps"></a>Passaggi successivi
* [Archiviare il log attività in un account di archiviazioneArchive the Activity log to a storage account](../../azure-monitor/platform/archive-activity-log.md)
* [Leggere la panoramica del log attività di AzureRead the overview of the Azure Activity log](../../azure-monitor/platform/platform-logs-overview.md)
* [Impostare un avviso in base a un evento del log attività](../../azure-monitor/platform/alerts-log-webhook.md)


