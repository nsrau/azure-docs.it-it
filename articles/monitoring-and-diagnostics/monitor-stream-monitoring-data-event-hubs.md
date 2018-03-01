---
title: Trasmettere i dati di monitoraggio di Azure a Hub eventi | Microsoft Docs
description: Informazioni su come trasmettere tutti i dati di monitoraggio di Azure a un hub eventi per inserire i dati in uno strumento di analisi o nelle informazioni di sicurezza e gestione degli eventi di partner.
author: johnkemnetz
manager: robb
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/13/2018
ms.author: johnkem
ms.openlocfilehash: d449be98cd59756e2bafc584e0501b8c83c594eb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno

Monitoraggio di Azure offre un'unica pipeline per l'accesso a tutti i dati di monitoraggio dall'ambiente Azure, consentendo di configurare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner per il consumo di questi dati. Questo articolo illustra come configurare diversi livelli di dati dell'ambiente Azure da inviare a un unico spazio dei nomi di Hub eventi o a un unico hub eventi in cui questi dati possono essere raccolti da uno strumento esterno.

## <a name="what-data-can-i-send-into-an-event-hub"></a>Quali dati si possono inviare a un hub eventi? 

All'interno dell'ambiente Azure esistono vari livelli di dati di monitoraggio a cui è possibile accedere in modi leggermente diversi a seconda del livello. In genere, i livelli possono essere descritti come segue:

- **Dati di monitoraggio dell'applicazione**: dati relativi alle prestazioni e alle funzionalità del codice scritto in esecuzione in Azure. Esempi di dati di monitoraggio dell'applicazione sono le tracce delle prestazioni, i registri applicazioni e i dati di telemetria utente. I dati di monitoraggio dell'applicazione vengono in genere raccolti in uno dei modi seguenti:
  - Tramite la strumentazione del codice con un SDK, ad esempio [Application Insights SDK](../application-insights/app-insights-overview.md).
  - Tramite l'esecuzione di un agente di monitoraggio che rimane in ascolto di nuovi registri applicazioni nel computer che esegue l'applicazione, ad esempio l'[agente Diagnostica di Azure per Windows](./azure-diagnostics.md) o l'[agente Diagnostica di Azure per Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Dati di monitoraggio del sistema operativo guest**: dati relativi al sistema operativo in cui viene eseguita l'applicazione. Esempi di dati di monitoraggio del sistema operativo guest sono syslog di Linux o gli eventi di sistema di Windows. Per raccogliere questo tipo di dati, è necessario installare un agente, ad esempio l'[agente Diagnostica di Azure per Windows](./azure-diagnostics.md) o l'[agente Diagnostica di Azure per Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Dati di monitoraggio delle risorse di Azure**: dati relativi al funzionamento di una risorsa di Azure. Per alcuni tipi di risorsa di Azure, ad esempio le macchine virtuali, sono presenti un sistema operativo guest e una o più applicazioni da monitorare all'interno di questo servizio di Azure. Per altre risorse di Azure, ad esempio i gruppi di sicurezza di rete, i dati di monitoraggio delle risorse costituiscono il livello superiore di dati disponibili (in queste risorse, infatti, non vengono eseguiti sistemi operativi guest o applicazioni). Questi dati possono essere raccolti tramite [le impostazioni di diagnostica delle risorse](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Dati di monitoraggio della piattaforma Azure**: dati relativi al funzionamento e alla gestione di un tenant o una sottoscrizione di Azure e dati relativi all'integrità e al funzionamento di Azure stesso. Il [log attività](./monitoring-overview-activity-logs.md), che include i dati di integrità del servizio, e i controlli di Active Directory sono esempi di dati di monitoraggio della piattaforma. Questi dati possono essere raccolti anche tramite le impostazioni di diagnostica.

I dati di qualsiasi livello possono essere inviati a un hub eventi in cui è possibile effettuarne il pull in uno strumento di partner. Le sezioni seguenti spiegano come configurare i dati di ogni livello per poterli trasmettere a un hub eventi. I passaggi descritti presuppongono che siano già presenti asset da monitorare nel livello considerato.

Prima di iniziare, è necessario [creare uno spazio dei nomi di Hub eventi e un hub eventi](../event-hubs/event-hubs-create.md). Lo spazio dei nomi e l'hub eventi sono la destinazione di tutti i dati di monitoraggio.

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Come si configurano i dati di monitoraggio della piattaforma Azure per poterli trasmettere a un hub eventi?

I dati di monitoraggio della piattaforma Azure provengono da due origini principali:
1. Il [log attività di Azure](./monitoring-overview-activity-logs.md), che contiene le operazioni di creazione, aggiornamento ed eliminazione di Resource Manager, le modifiche apportate all'[integrità dei servizi di Azure](../service-health/service-health-overview.md) che possono influire sulle risorse disponibili nella sottoscrizione, le transizioni dello stato di [integrità delle risorse](../service-health/resource-health-overview.md) e vari altri tipi di eventi a livello di sottoscrizione. [Questo articolo descrive in dettaglio tutte le categorie di eventi indicati nel log attività di Azure](./monitoring-activity-log-schema.md).
2. I [report di Azure Active Directory](../active-directory/active-directory-reporting-azure-portal.md), che contengono la cronologia degli accessi e l'audit trail delle modifiche apportate all'interno di un particolare tenant. Non è ancora possibile per trasmettere i dati di Azure Active Directory in un hub eventi.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Trasmettere i dati del log attività di Azure in un hub eventi

Per inviare dati dal log attività di Azure in uno spazio dei nomi di Hub eventi, si configura un profilo di log per la sottoscrizione. [Seguire queste istruzioni](./monitoring-stream-activity-logs-event-hubs.md) per configurare un profilo di log per la sottoscrizione. Eseguire queste operazioni una sola volta per ogni sottoscrizione da monitorare.

> [!TIP]
> Attualmente un profilo di log consente di selezionare solo uno spazio dei nomi di Hub eventi in cui è stato creato un hub eventi con il nome insights-operational-logs. In un profilo di log non è ancora possibile specificare un nome per l'hub eventi.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Come si configurano i dati di monitoraggio delle risorse di Azure per poterli trasmettere a un hub eventi?

Le risorse di Azure generano due tipi di dati di monitoraggio:
1. [Log di diagnostica delle risorse](./monitoring-overview-of-diagnostic-logs.md)
2. [Metriche](monitoring-overview-metrics.md)

Entrambi i tipi di dati vengono inviati a un hub eventi tramite un'impostazione di diagnostica delle risorse. [Seguire queste istruzioni](./monitoring-stream-diagnostic-logs-to-event-hubs.md) per configurare un'impostazione di diagnostica per una determinata risorsa. Configurare un'impostazione di diagnostica per ogni risorsa di cui si vogliono raccogliere i log.

> [!TIP]
> I criteri di Azure consentono di assicurarsi che tutte le risorse di un determinato ambito siano sempre configurate con un'impostazione di diagnostica [usando l'effetto di DeployIfNotExists nella regola dei criteri](../azure-policy/policy-definition.md#policy-rule). DeployIfNotExists è attualmente supportato solo nei criteri predefiniti.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>Come si configurano i dati di monitoraggio del sistema operativo guest per poterli trasmettere a un hub eventi?

È necessario installare un agente per inviare i dati di monitoraggio del sistema operativo guest in un hub eventi. Per Windows o Linux, si specificano in un file di configurazione i dati da inviare all'hub eventi e l'hub eventi a cui i dati devono essere inviati. Il file di configurazione viene quindi passato all'agente in esecuzione nella macchina virtuale.

### <a name="stream-linux-data-to-an-event-hub"></a>Trasmettere dati di Linux a un hub eventi

L'[agente Diagnostica di Azure per Linux](../virtual-machines/linux/diagnostic-extension.md) consente di inviare i dati di monitoraggio da un computer Linux a un hub eventi. A questo scopo, aggiungere l'hub eventi come sink nella sezione JSON impostazioni protette del file di configurazione LAD. [Per altre informazioni sull'aggiunta del sink di hub eventi all'agente Diagnostica di Azure per Linux, vedere questo articolo](../virtual-machines/linux/diagnostic-extension.md#protected-settings).

> [!NOTE]
> Non è possibile configurare la trasmissione dei dati di monitoraggio del sistema operativo guest a un hub eventi nel portale. In alternativa, è necessario modificare manualmente il file di configurazione.

### <a name="stream-windows-data-to-an-event-hub"></a>Trasmettere dati di Windows a un hub eventi

L'[agente Diagnostica di Azure per Windows](./azure-diagnostics.md) consente di inviare i dati di monitoraggio da un computer Windows a un hub eventi. A questo scopo, aggiungere l'hub eventi come sink nella sezione privateConfig del file di configurazione WAD. [Per altre informazioni sull'aggiunta del sink di hub eventi all'agente Diagnostica di Azure per Windows, vedere questo articolo](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> Non è possibile configurare la trasmissione dei dati di monitoraggio del sistema operativo guest a un hub eventi nel portale. In alternativa, è necessario modificare manualmente il file di configurazione.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>Come si configurano i dati di monitoraggio dell'applicazione per poterli trasmettere a un hub eventi?

I dati di monitoraggio dell'applicazione richiedono che il codice sia instrumentato con un SDK. Non esiste quindi una soluzione generica per il routing dei dati di monitoraggio dell'applicazione a un hub eventi in Azure. Tuttavia, [Azure Application Insights](../application-insights/app-insights-overview.md) è un servizio che consente di raccogliere i dati di Azure a livello di applicazione. Se si usa Application Insights, è possibile trasmettere i dati di monitoraggio a un hub eventi eseguendo queste operazioni:

1. [Impostare l'esportazione continua](../application-insights/app-insights-export-telemetry.md) dei dati di Application Insights in un account di archiviazione.

2. Configurare un'app per la logica attivata da un timer che [esegua il pull dei dati dall'archiviazione BLOB](../connectors/connectors-create-api-azureblobstorage.md#use-an-action) e [il push come messaggio nell'hub eventi](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Quali operazioni si possono eseguire con i dati di monitoraggio inviati all'hub eventi?

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner. Per leggere i dati dall'hub di eventi, quasi tutti gli strumenti richiedono la stringa di connessione dell'hub eventi e determinate autorizzazioni di accesso alla sottoscrizione di Azure. Di seguito è riportato un elenco non esaustivo di strumenti con Monitoraggio di Azure integrato:

* **IBM QRadar** - Microsoft Azure DSM e Microsoft Azure Event Hub Protocol sono scaricabili dal [sito Web del supporto IBM](http://www.ibm.com/support). [Altre informazioni sull'integrazione con Azure sono disponibili qui](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** - A seconda delle impostazioni di Splunk, sono possibili due approcci:
    1. Il [componente aggiuntivo Monitoraggio di Azure per Splunk](https://splunkbase.splunk.com/app/3534/) è disponibile in Splunkbase e in un progetto open source. [La documentazione è disponibile qui](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Se non è possibile installare un componente aggiuntivo nell'istanza di Splunk (ad es. se si usa un proxy o Splunk Cloud), è possibile inoltrare questi eventi all'agente di raccolta di eventi Splunk HTTP tramite [questa funzione, che viene attivata da nuovi messaggi nell'hub eventi](https://github.com/sebastus/AzureFunctionForSplunkVS).
* **SumoLogic** - Le istruzioni per configurare SumoLogic per il consumo dei dati di un hub eventi sono [disponibili qui](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub).

## <a name="next-steps"></a>Passaggi successivi
* [Archive the Activity Log to a storage account](monitoring-archive-activity-log.md) (Archiviare il log attività in un account di archiviazione)
* Leggere la [panoramica sul log attività di Azure](monitoring-overview-activity-logs.md)
* [Set up an alert based on an Activity Log event](insights-auditlog-to-webhook-email.md) (Configurare un avviso in base a un evento del log attività)

