---
title: Trasmettere i dati di monitoraggio di Azure all'hub eventi
description: Informazioni su come trasmettere i dati di monitoraggio di Azure a un hub eventi per ottenere i dati in uno strumento SIEM o Analytics del partner.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 254cbc995da9380f108970fb981c000fca7dc63f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925819"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Trasmettere i dati di monitoraggio di Azure a un hub eventi
Monitoraggio di Azure offre una soluzione completa per il monitoraggio completo dello stack per applicazioni e servizi in Azure, in altri cloud e in locale. Oltre a usare monitoraggio di Azure per analizzare i dati e sfruttarli per diversi scenari di monitoraggio, potrebbe essere necessario inviarli ad altri strumenti di monitoraggio nell'ambiente in uso. Il metodo più efficace per trasmettere i dati di monitoraggio agli strumenti esterni nella maggior parte dei casi consiste nell'usare [Hub eventi di Azure](/azure/event-hubs/). Questo articolo fornisce una breve descrizione di come è possibile trasmettere i dati di monitoraggio da origini diverse a un hub eventi e collegamenti a istruzioni dettagliate.


## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Prima di configurare il flusso per qualsiasi origine dati, è necessario [creare uno spazio dei nomi di hub eventi e un hub eventi](../../event-hubs/event-hubs-create.md). Lo spazio dei nomi e l'hub eventi sono la destinazione di tutti i dati di monitoraggio. Uno spazio dei nomi di Hub eventi è un raggruppamento logico di hub eventi che condividono gli stessi criteri di accesso, così come un account di archiviazione contiene al suo interno singoli BLOB. Tenere presente i dettagli seguenti sullo spazio dei nomi di hub eventi e sugli hub eventi usati per lo streaming dei dati di monitoraggio:

* Il numero di unità elaborate consente di aumentare la scala della velocità effettiva per gli hub eventi. In genere è necessaria una sola unità di velocità effettiva. Se è necessario aumentare le prestazioni man mano che aumenta l'utilizzo del log, è possibile aumentare manualmente il numero di unità di velocità effettiva per lo spazio dei nomi o abilitare l'inflazione automatica.
* Il numero di partizioni consente di parallelizzare il consumo tra molti consumer. Una singola partizione può supportare fino a 20MBps o circa 20.000 messaggi al secondo. A seconda dello strumento che utilizza i dati, può essere supportato o meno il consumo di dati di più partizioni. Se non si è certi del numero di partizioni da impostare, è ragionevole avviare quattro partizioni.
* È possibile impostare la conservazione dei messaggi nell'hub eventi per almeno 7 giorni. Se lo strumento utilizzato diventa inattivo per più di un giorno, in questo modo si garantisce che lo strumento possa riprendersi dal punto in cui è stato interrotto per gli eventi fino a 7 giorni fa.
* Usare il gruppo di consumer predefinito per l'hub eventi. Non è necessario creare altri gruppi di consumer o usare un gruppo di consumer distinto, a meno che non siano previsti due diversi strumenti che utilizzano gli stessi dati dello stesso hub eventi.
* Per il log attività di Azure, si sceglie uno spazio dei nomi di hub eventi e monitoraggio di Azure crea un hub eventi all'interno di tale spazio dei nomi denominato _Insights-logs-Operational-logs_. Per altri tipi di log, è possibile scegliere un hub eventi esistente o fare in modo che monitoraggio di Azure crei un hub eventi per ogni categoria di log.
* È in genere necessario aprire la porta in uscita 5671 e 5672 nel computer o VNET l'utilizzo dei dati dall'hub eventi.

## <a name="monitoring-data-available"></a>Dati di monitoraggio disponibili
Le [origini dei dati di monitoraggio per monitoraggio di Azure](data-sources.md) descrivono i diversi livelli di dati per le applicazioni Azure e i tipi di dati di monitoraggio disponibili per ognuno di essi. La tabella seguente elenca ognuno di questi livelli e una descrizione del modo in cui i dati possono essere trasmessi a un hub eventi. Per ulteriori dettagli, attenersi ai collegamenti disponibili.

| Livello | Dati | Metodo |
|:---|:---|:---|
| [Tenant di Azure](data-sources.md#azure-tenant) | Log di controllo Azure Active Directory | Configurare un'impostazione di diagnostica del tenant nel tenant di AAD. Per informazioni dettagliate, vedere [esercitazione: trasmettere i log Azure Active Directory a un hub eventi di Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) . |
| [Sottoscrizione di Azure](data-sources.md#azure-subscription) | Azure Activity Log | Creare un profilo di log per esportare gli eventi del log attività in hub eventi.  Per informazioni dettagliate, vedere [esportare il log attività di Azure nell'archiviazione o hub eventi di Azure](activity-log-export.md) . |
| [Risorse di Azure](data-sources.md#azure-resources) | Metriche della piattaforma<br> Log risorse |Entrambi i tipi di dati vengono inviati a un hub eventi tramite un'impostazione di diagnostica delle risorse. Per informazioni dettagliate, vedere [trasmettere i log delle risorse di Azure a un hub eventi](resource-logs-stream-event-hubs.md) . |
| [Sistema operativo (Guest)](data-sources.md#operating-system-guest) | Macchine virtuali di Azure | Installare l' [estensione diagnostica di Azure](diagnostics-extension-overview.md) in macchine virtuali Windows e Linux in Azure. Per informazioni dettagliate sulle macchine virtuali Windows e l' [uso dell'estensione di diagnostica Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) per i dettagli sulle macchine virtuali Linux, vedere [trasmettere i dati diagnostica di Azure nel percorso critico tramite hub eventi](diagnostics-extension-stream-event-hubs.md) . |
| [Codice dell'applicazione](data-sources.md#application-code) | Application Insights | Application Insights non fornisce un metodo diretto per trasmettere dati a hub eventi. È possibile [configurare l'esportazione continua](../../azure-monitor/app/export-telemetry.md) dei dati Application Insights in un account di archiviazione e quindi usare un'app per la logica per inviare i dati a un hub eventi, come descritto in [streaming manuale con l'app per la logica](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Streaming manuale con l'app per la logica
Per i dati che non è possibile trasmettere direttamente a un hub eventi, è possibile scrivere in archiviazione di Azure e quindi usare un'app per la logica attivata in base al tempo che [estrae i dati dall'archiviazione BLOB](../../connectors/connectors-create-api-azureblobstorage.md#add-action) e [li inserisce come messaggio nell'hub eventi](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Strumenti per i partner con integrazione di monitoraggio di Azure

Il routing dei dati di monitoraggio a un hub eventi con monitoraggio di Azure consente di eseguire facilmente l'integrazione con gli strumenti esterni SIEM e di monitoraggio. Di seguito sono riportati alcuni esempi di strumenti con l'integrazione di monitoraggio di Azure:

| Strumento | Ospitato in Azure | Description |
|:---|:---| :---|
|  IBM QRadar | No | Microsoft Azure DSM e Microsoft Azure Event Hub Protocol sono scaricabili dal [sito Web del supporto IBM](https://www.ibm.com/support). Altre informazioni sull'integrazione con Azure sono disponibili nella pagina relativa alla [configurazione di QRADAR DSM](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | No | [Il componente aggiuntivo monitoraggio di Azure per Splunk](https://splunkbase.splunk.com/app/3534/) è un progetto open source disponibile in splunkbase. La documentazione è disponibile nell' [addon di monitoraggio di Azure per Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Se non è possibile installare un componente aggiuntivo nell'istanza di Splunk, se ad esempio si usa un proxy o in esecuzione in Splunk cloud, è possibile inviare questi eventi all'agente di raccolta eventi HTTP Splunk usando la [funzione di Azure per Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), che viene attivata da nuovi messaggi nell'hub eventi. |
| sumologic | No | Le istruzioni per la configurazione di SumoLogic per l'utilizzo di dati da un hub eventi sono disponibili in [raccogliere i log per l'app Azure audit dall'hub eventi](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | No | Il connettore intelligente Hub eventi di Azure ArcSight è disponibile come parte della [raccolta di Smart Connector ArcSight](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Server Syslog | No | Se si vuole trasmettere i dati di monitoraggio di Azure direttamente a un server syslog, è possibile usare una [soluzione basata su una funzione di Azure](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | No| Le istruzioni per configurare LogRhythm per la raccolta di log da un hub eventi sono disponibili [qui](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | SÌ | Per altre informazioni, vedere [Introduzione al monitoraggio e alla registrazione con LOGZ.io per app Java in esecuzione in Azure](https://docs.microsoft.com/azure/java/java-get-started-with-logzio)


## <a name="next-steps"></a>Fasi successive
* [Archiviare il log attività in un account di archiviazione](../../azure-monitor/platform/archive-activity-log.md)
* [Leggere la panoramica del log attività di Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Configurare un avviso in base a un evento del log attività](../../azure-monitor/platform/alerts-log-webhook.md)


