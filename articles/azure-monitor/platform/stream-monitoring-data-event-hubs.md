---
title: Trasmettere i dati di monitoraggio di Azure a Hub eventi
description: Informazioni su come trasmettere tutti i dati di monitoraggio di Azure a un hub eventi per inserire i dati in uno strumento di analisi o nelle informazioni di sicurezza e gestione degli eventi di partner.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 424dc1611622a1dfc37419fd443d860698020524
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468234"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno

Monitoraggio di Azure offre un'unica pipeline per l'accesso a tutti i dati di monitoraggio dall'ambiente Azure, consentendo di configurare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner per il consumo di questi dati. Questo articolo illustra come configurare diversi livelli di dati dell'ambiente Azure da inviare a un unico spazio dei nomi di Hub eventi o a un unico hub eventi in cui questi dati possono essere raccolti da uno strumento esterno.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

## <a name="what-data-can-i-send-into-an-event-hub"></a>Quali dati si possono inviare a un hub eventi?

All'interno dell'ambiente Azure esistono vari livelli di dati di monitoraggio a cui è possibile accedere in modi leggermente diversi a seconda del livello. In genere, i livelli possono essere descritti come segue:

- **Dati di monitoraggio dell'applicazione:** dati relativi alle prestazioni e alle funzionalità del codice scritto in esecuzione in Azure. Esempi di dati di monitoraggio dell'applicazione sono le tracce delle prestazioni, i registri applicazioni e i dati di telemetria utente. I dati di monitoraggio dell'applicazione vengono in genere raccolti in uno dei modi seguenti:
  - Tramite la strumentazione del codice con un SDK, ad esempio [Application Insights SDK](../../azure-monitor/app/app-insights-overview.md).
  - Tramite l'esecuzione di un agente di monitoraggio che rimane in ascolto di nuovi registri applicazioni nel computer che esegue l'applicazione, ad esempio l'[agente Diagnostica di Azure per Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) o l'[agente Diagnostica di Azure per Linux](../../virtual-machines/extensions/diagnostics-linux.md).
- **Dati di monitoraggio del sistema operativo guest:** dati relativi al sistema operativo in cui viene eseguita l'applicazione. Esempi di dati di monitoraggio del sistema operativo guest sono syslog di Linux o gli eventi di sistema di Windows. Per raccogliere questo tipo di dati, è necessario installare un agente, ad esempio l'[agente Diagnostica di Azure per Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) o l'[agente Diagnostica di Azure per Linux](../../virtual-machines/extensions/diagnostics-linux.md).
- **Dati di monitoraggio delle risorse di Azure:** dati relativi al funzionamento di una risorsa di Azure. Per alcuni tipi di risorsa di Azure, ad esempio le macchine virtuali, sono presenti un sistema operativo guest e una o più applicazioni da monitorare all'interno di questo servizio di Azure. Per altre risorse di Azure, ad esempio i gruppi di sicurezza di rete, i dati di monitoraggio delle risorse costituiscono il livello superiore di dati disponibili (in queste risorse, infatti, non vengono eseguiti sistemi operativi guest o applicazioni). Questi dati possono essere raccolti tramite [le impostazioni di diagnostica delle risorse](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
- **Dati di monitoraggio della sottoscrizione di Azure:** dati relativi al funzionamento e alla gestione di una sottoscrizione di Azure e dati relativi all'integrità e al funzionamento di Azure stesso. Il [log attività](./../../azure-monitor/platform/activity-logs-overview.md) contiene la maggior parte dei dati di monitoraggio della sottoscrizione, ad esempio eventi imprevisti relativi all'integrità del servizio e controlli di Azure Resource Manager. È possibile raccogliere questi dati tramite un profilo di log.
- **Dati di monitoraggio del tenant di Azure:** i dati relativi al funzionamento dei servizi di Azure a livello di tenant, ad esempio Azure Active Directory. I controlli di Azure Active Directory e gli accessi sono esempi di dati di monitoraggio del tenant. Questi dati possono essere raccolti tramite le impostazioni di diagnostica del tenant.

I dati di qualsiasi livello possono essere inviati a un hub eventi in cui è possibile effettuarne il pull in uno strumento di partner. Le sezioni seguenti spiegano come configurare i dati di ogni livello per poterli trasmettere a un hub eventi. I passaggi descritti presuppongono che siano già presenti asset da monitorare nel livello considerato.

## <a name="set-up-an-event-hubs-namespace"></a>Configurare uno spazio dei nomi di Hub eventi

Prima di iniziare, è necessario [creare uno spazio dei nomi di Hub eventi e un hub eventi](../../event-hubs/event-hubs-create.md). Lo spazio dei nomi e l'hub eventi sono la destinazione di tutti i dati di monitoraggio. Uno spazio dei nomi di Hub eventi è un raggruppamento logico di hub eventi che condividono gli stessi criteri di accesso, così come un account di archiviazione contiene al suo interno singoli BLOB. Tenere presenti alcuni dettagli relativi allo spazio dei nomi di Hub eventi e agli hub eventi creati:
* È consigliabile usare uno spazio dei nomi di Hub eventi Standard.
* In genere, è necessaria una sola unità elaborata. Per aumentare le prestazioni di pari passo con un maggiore utilizzo dei log, è sempre possibile aumentare manualmente il numero di unità elaborate per lo spazio dei nomi in un secondo momento oppure abilitare la funzionalità Aumento automatico.
* Il numero di unità elaborate consente di aumentare la scala della velocità effettiva per gli hub eventi. Il numero di partizioni consente di parallelizzare il consumo tra molti consumer. Una singola partizione può eseguire fino a 20 Mbps o circa 20.000 messaggi al secondo. A seconda dello strumento che utilizza i dati, può essere supportato o meno il consumo di dati di più partizioni. Se si hanno dubbi sul numero di partizioni da impostare, è consigliabile iniziare con quattro partizioni.
* È consigliabile impostare il periodo di memorizzazione dei messaggi nell'hub eventi su 7 giorni. Se lo strumento che utilizza i dati diventa inattivo per più di un giorno, questa impostazione assicura che tale strumento riprenderà l'esecuzione dal punto in cui è stata interrotta (per gli eventi degli ultimi 7 giorni).
* È consigliabile usare il gruppo di consumer predefinito per l'hub eventi. Non è necessario creare altri gruppi di consumer o usare un gruppo di consumer distinto, a meno che non siano previsti due diversi strumenti che utilizzano gli stessi dati dello stesso hub eventi.
* Per il log attività di Azure si sceglie uno spazio dei nomi di Hub eventi e Monitoraggio di Azure crea un hub eventi all'interno di questo spazio dei nomi, denominato "insights-log-operationallogs". Per altri tipi di log è possibile scegliere un hub eventi esistente (che consente di riutilizzare lo stesso hub eventi insights-log-operationallogs) oppure lasciare che Monitoraggio di Azure crei un hub eventi per ogni categoria di log.
* In genere, le porte 5671 e 5672 devono essere aperte sulla macchina che utilizza i dati dell'hub eventi.

Vedere anche le [domande frequenti su Hub eventi di Azure](../../event-hubs/event-hubs-faq.md).

## <a name="azure-tenant-monitoring-data"></a>Dati di monitoraggio del tenant di Azure

I dati di monitoraggio del tenant di Azure sono attualmente disponibili solo per Azure Active Directory. È possibile usare i dati dei [report di Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md), che contengono la cronologia degli accessi e l'audit trail delle modifiche apportate all'interno di un particolare tenant.

### <a name="azure-active-directory-data"></a>Dati di Azure Active Directory

Per inviare dati dal log di Azure Active Directory in uno spazio dei nomi di Hub eventi, è necessario configurare un'impostazione di diagnostica del tenant nel tenant di AAD. [Seguire questa guida](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) per configurare un'impostazione di diagnostica del tenant.

## <a name="azure-subscription-monitoring-data"></a>Dati di monitoraggio della sottoscrizione di Azure

I dati di monitoraggio della sottoscrizione di Azure sono disponibili nel [log attività di Azure](./../../azure-monitor/platform/activity-logs-overview.md). Questo contiene le operazioni di creazione, aggiornamento ed eliminazione di Gestione risorse, le modifiche apportate all'[integrità dei servizi di Azure](../../service-health/service-health-overview.md) che possono influire sulle risorse disponibili nella sottoscrizione, le transizioni dello stato di [integrità delle risorse](../../service-health/resource-health-overview.md) e vari altri tipi di eventi a livello di sottoscrizione. [Questo articolo descrive in dettaglio tutte le categorie di eventi indicati nel log attività di Azure](./../../azure-monitor/platform/activity-log-schema.md).

### <a name="activity-log-data"></a>Dati del log attività

Per inviare dati dal log attività di Azure in uno spazio dei nomi di Hub eventi, si configura un profilo di log per la sottoscrizione. [Seguire queste istruzioni](./activity-logs-stream-event-hubs.md) per configurare un profilo di log per la sottoscrizione. Eseguire queste operazioni una sola volta per ogni sottoscrizione da monitorare.

> [!TIP]
> Attualmente un profilo di log consente di selezionare solo uno spazio dei nomi di Hub eventi in cui è stato creato un hub eventi con il nome insights-operational-logs. In un profilo di log non è ancora possibile specificare un nome per l'hub eventi.

## <a name="azure-resource-metrics-and-diagnostics-logs"></a>Log di diagnostica e metriche delle risorse di Azure

Le risorse di Azure generano due tipi di dati di monitoraggio:
1. [Log di diagnostica delle risorse](./../../azure-monitor/platform/diagnostic-logs-overview.md)
2. [Metriche](../../azure-monitor/platform/data-collection.md)

Entrambi i tipi di dati vengono inviati a un hub eventi tramite un'impostazione di diagnostica delle risorse. [Seguire queste istruzioni](./../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) per configurare un'impostazione di diagnostica per una determinata risorsa. Configurare un'impostazione di diagnostica per ogni risorsa di cui si vogliono raccogliere i log.

> [!TIP]
> I criteri di Azure consentono di assicurarsi che tutte le risorse di un determinato ambito siano sempre configurate con un'impostazione di diagnostica [usando l'effetto di DeployIfNotExists nella regola dei criteri](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="guest-os-data"></a>Dati del sistema operativo guest

È necessario installare un agente per inviare i dati di monitoraggio del sistema operativo guest in un hub eventi. Per Windows o Linux, si specificano in un file di configurazione i dati da inviare all'hub eventi e l'hub eventi a cui i dati devono essere inviati. Il file di configurazione viene quindi passato all'agente in esecuzione nella macchina virtuale.

### <a name="linux-data"></a>Dati di Linux

L'[agente Diagnostica di Azure per Linux](../../virtual-machines/extensions/diagnostics-linux.md) consente di inviare i dati di monitoraggio da un computer Linux a un hub eventi. A questo scopo, aggiungere l'hub eventi come sink nella sezione JSON impostazioni protette del file di configurazione LAD. [Per altre informazioni sull'aggiunta del sink di hub eventi all'agente Diagnostica di Azure per Linux, vedere questo articolo](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Non è possibile configurare la trasmissione dei dati di monitoraggio del sistema operativo guest a un hub eventi nel portale. In alternativa, è necessario modificare manualmente il file di configurazione.

### <a name="windows-data"></a>Dati di Windows

L'[agente Diagnostica di Azure per Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) consente di inviare i dati di monitoraggio da un computer Windows a un hub eventi. A questo scopo, aggiungere l'hub eventi come sink nella sezione privateConfig del file di configurazione WAD. [Per altre informazioni sull'aggiunta del sink di hub eventi all'agente Diagnostica di Azure per Windows, vedere questo articolo](./../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

> [!NOTE]
> Non è possibile configurare la trasmissione dei dati di monitoraggio del sistema operativo guest a un hub eventi nel portale. In alternativa, è necessario modificare manualmente il file di configurazione.

## <a name="application-monitoring-data"></a>Dati di monitoraggio dell'applicazione

I dati di monitoraggio dell'applicazione richiedono che il codice sia instrumentato con un SDK. Non esiste quindi una soluzione generica per il routing dei dati di monitoraggio dell'applicazione a un hub eventi in Azure. Tuttavia, [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) è un servizio che consente di raccogliere i dati di Azure a livello di applicazione. Se si usa Application Insights, è possibile trasmettere i dati di monitoraggio a un hub eventi eseguendo queste operazioni:

1. [Impostare l'esportazione continua](../../azure-monitor/app/export-telemetry.md) dei dati di Application Insights in un account di archiviazione.

2. Configurare un'app per la logica attivata da un timer che [esegua il pull dei dati dall'archiviazione BLOB](../../connectors/connectors-create-api-azureblobstorage.md#add-action) e [il push come messaggio nell'hub eventi](../../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Quali operazioni si possono eseguire con i dati di monitoraggio inviati all'hub eventi?

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrare facilmente gli strumenti di monitoraggio e le informazioni di sicurezza e gestione degli eventi di partner. Per leggere i dati dall'hub di eventi, quasi tutti gli strumenti richiedono la stringa di connessione dell'hub eventi e determinate autorizzazioni di accesso alla sottoscrizione di Azure. Di seguito è riportato un elenco non esaustivo di strumenti con Monitoraggio di Azure integrato:

* **IBM QRadar** - Microsoft Azure DSM e Microsoft Azure Event Hub Protocol sono scaricabili dal [sito Web del supporto IBM](https://www.ibm.com/support). [Altre informazioni sull'integrazione con Azure sono disponibili qui](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** - A seconda delle impostazioni di Splunk, sono possibili due approcci:
    1. Il [componente aggiuntivo Monitoraggio di Azure per Splunk](https://splunkbase.splunk.com/app/3534/) è disponibile in Splunkbase e in un progetto open source. [La documentazione è disponibile qui](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Se non è possibile installare un componente aggiuntivo nell'istanza di Splunk (ad es. se si usa un proxy o Splunk Cloud), è possibile inoltrare questi eventi all'agente di raccolta di eventi Splunk HTTP tramite [questa funzione, che viene attivata da nuovi messaggi nell'hub eventi](https://github.com/Microsoft/AzureFunctionforSplunkVS).
* **SumoLogic** - Le istruzioni per configurare SumoLogic per il consumo dei dati di un hub eventi sono [disponibili qui](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub).
* **ArcSight**: il connettore intelligente dell'hub eventi di Azure per ArcSight è disponibile nella [raccolta di connettori intelligenti ArcSight qui](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852).
* **Server Syslog** - Se si vuole trasmettere i dati di Monitoraggio di Azure direttamente a un server syslog, è possibile estrarre [questo repository GitHub](https://github.com/miguelangelopereira/azuremonitor2syslog/).

## <a name="next-steps"></a>Passaggi successivi
* [Archive the Activity Log to a storage account](../../azure-monitor/platform/archive-activity-log.md) (Archiviare il log attività in un account di archiviazione)
* Leggere la [panoramica sul log attività di Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Set up an alert based on an Activity Log event](../../azure-monitor/platform/alerts-log-webhook.md) (Configurare un avviso in base a un evento del log attività)


