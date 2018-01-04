---
title: Flusso di dati per gli hub di eventi di monitoraggio di Azure | Documenti Microsoft
description: Informazioni su come eseguire il flusso di tutti i dati di monitoraggio Azure a un hub di eventi per ottenere i dati in un partner SIEM o lo strumento analitica.
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
ms.date: 12/20/2017
ms.author: johnkem
ms.openlocfilehash: 59f0cba66a5d8d2a528700861efff86967c1c748
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Azure flusso dati a un hub di eventi per l'utilizzo di monitoraggio da uno strumento esterno

Monitoraggio Azure fornisce una singola pipeline per ottenere l'accesso a tutti i dati di monitoraggio dall'ambiente Azure, che consente di impostare facilmente partner SIEM e strumenti per utilizzare i dati di monitoraggio. Questo articolo viene illustrato come impostare diversi livelli di dati dall'ambiente di Azure da inviare a un singolo hub eventi dello spazio dei nomi o un evento hub, in cui possono essere raccolti da uno strumento esterno.

## <a name="what-data-can-i-send-into-an-event-hub"></a>I dati che è possibile inviare a un hub eventi 

All'interno dell'ambiente Azure, esistono diverse 'livelli' dei dati di monitoraggio e il metodo di accesso ai dati da ogni livello varia leggermente. In genere, questi livelli possono essere descritta come:

- **Dati di monitoraggio delle applicazioni:** dati sulle prestazioni e funzionalità del codice scritto personalmente e sono in esecuzione in Azure. Le tracce sulle prestazioni, registri applicazioni e dati di telemetria utente sono esempi di dati di monitoraggio delle applicazioni. Dati di monitoraggio delle applicazioni in genere vengono raccolti in uno dei modi seguenti:
  - Tramite la strumentazione del codice con un SDK, ad esempio il [Application Insights SDK](../application-insights/app-insights-overview.md).
  - Tramite l'esecuzione di un agente di monitoraggio è in ascolto per Registra nuova applicazione nel computer che esegue l'applicazione, come il [agente diagnostica Windows Azure](./azure-diagnostics.md) o [agente Linux di diagnostica Azure](../virtual-machines/linux/diagnostic-extension.md).
- **Monitoraggio dei dati del sistema operativo guest:** dati relativi al sistema operativo in cui è in esecuzione l'applicazione. Esempi di dati di monitoraggio del sistema operativo guest sono syslog di Linux o eventi di sistema di Windows. Per raccogliere questo tipo di dati, è necessario installare un agente, ad esempio il [agente diagnostica Windows Azure](./azure-diagnostics.md) o [agente Linux di diagnostica Azure](../virtual-machines/linux/diagnostic-extension.md).
- **Dati di monitoraggio risorse di Azure:** dati sull'operazione di una risorsa di Azure. Per alcuni tipi di risorse di Azure, ad esempio macchine virtuali, è disponibile un sistema operativo guest e applicazioni da monitorare all'interno di tale servizio di Azure. Per altre risorse di Azure, ad esempio gruppi di sicurezza di rete, la risorsa dati di monitoraggio è il livello più elevato dei dati disponibili (poiché non esiste alcun sistema operativo guest o l'applicazione in esecuzione in tali risorse). Questi dati possono essere raccolti tramite [le impostazioni di diagnostica di risorse](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Piattaforma Azure, i dati di monitoraggio:** dati sull'operazione e la gestione di una sottoscrizione di Azure o tenant, nonché i dati sull'integrità e il funzionamento di Azure stesso. Il [log attività](./monitoring-overview-activity-logs.md), inclusi i dati di integrità del servizio e Active Directory controlli sono riportati alcuni esempi della piattaforma dati di monitoraggio. Questi dati possono essere raccolti utilizzando anche le impostazioni di diagnostica.

Dati da qualsiasi livello possono essere inviati a un hub di eventi, in cui è possibile effettuare il pull in uno strumento di partner. Nelle sezioni successive viene descritto come configurare i dati da ogni livello da trasmettere a un hub eventi. I passaggi presuppongono che si dispone di risorse a tale livello da monitorare.

Prima di iniziare, è necessario [creazione di un hub di spazio dei nomi e l'evento di hub eventi](../event-hubs/event-hubs-create.md). Questo hub di eventi e di spazio dei nomi è la destinazione per tutti i dati di monitoraggio.

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Come impostare la piattaforma Azure il monitoraggio dei dati da trasmettere a un hub eventi?

Piattaforma Azure, il monitoraggio dei dati provenienza da due origini principali:
1. Il [log attività Azure](./monitoring-overview-activity-logs.md), che contiene la creazione, aggiornamento e le operazioni di eliminazione da Gestione risorse, le modifiche in [dell'integrità del servizio Azure](../service-health/service-health-overview.md) che potrebbe influire sulle risorse nella sottoscrizione, il [integrità delle risorse](../service-health/resource-health-overview.md) transizioni di stato e molti altri tipi di eventi a livello di sottoscrizione. [In questo articolo illustra in dettaglio tutte le categorie di eventi che vengono visualizzati nel log attività Azure](./monitoring-activity-log-schema.md).
2. [Azure Active Directory reporting](../active-directory/active-directory-reporting-azure-portal.md), che contiene la cronologia di accesso attività e itinerario di controllo delle modifiche apportate all'interno di un particolare tenant. Non è ancora possibile per trasmettere i dati di Azure Active Directory in un hub eventi.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Dati di log da parte del flusso attività di Azure in un hub eventi

Per inviare dati dal log delle attività di Azure in uno spazio dei nomi dell'hub eventi, si configura un profilo di Log per la sottoscrizione. [Seguire questa Guida](./monitoring-stream-activity-logs-event-hubs.md) per configurare un profilo di Log per la sottoscrizione. Eseguire questa operazione una volta per ogni sottoscrizione che si desidera monitorare.

> [!TIP]
> Un profilo di Log attualmente solo consente di selezionare uno spazio dei nomi, gli hub di eventi in cui viene creato un hub eventi con i nome 'insights--registri.' Non è ancora possibile specificare il proprio nome hub di eventi in un profilo di Log.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Come impostare dati di monitoraggio risorse di Azure da trasmettere a un hub eventi?

Risorse di Azure creare due tipi di dati di monitoraggio:
1. [Log di diagnostica di risorse](./monitoring-overview-of-diagnostic-logs.md)
2. [Metriche](monitoring-overview-metrics.md)

Entrambi i tipi di dati vengono inviati a un hub di eventi utilizzando un'impostazione di diagnostica di risorse. [Seguire questa Guida](./monitoring-stream-diagnostic-logs-to-event-hubs.md) per configurare un'impostazione di diagnostica di risorse su una determinata risorsa. Configurare un'impostazione di diagnostica di risorse in ogni risorsa da cui si desidera raccogliere i log.

> [!TIP]
> È possibile utilizzare criteri di Azure per verificare che tutte le risorse in un ambito sono sempre configurate con un'impostazione di diagnostica [utilizzando l'effetto DeployIfNotExists nella regola dei criteri](../azure-policy/policy-definition.md#policy-rule). Oggi DeployIfNotExists è supportata solo su criteri predefiniti.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>Come impostare dati di monitoraggio del sistema operativo guest da trasmettere a un hub eventi?

È necessario installare un agente per l'invio di dati di monitoraggio del sistema operativo guest in un hub eventi. Per Windows o Linux, specificare i dati desiderati da inviare per l'hub eventi, nonché l'hub di eventi a cui i dati devono essere inviati in un file di configurazione e passare il file di configurazione per l'agente in esecuzione nella macchina virtuale.

### <a name="stream-linux-data-to-an-event-hub"></a>Flusso di dati di Linux a un hub eventi

Il [agente diagnostica Azure Linux](../virtual-machines/linux/diagnostic-extension.md) può essere utilizzato per inviare i dati da un computer Linux a un hub eventi di monitoraggio. Questo scopo, aggiungere l'hub di eventi come un sink del LAD impostazioni di file protetti di configurazione JSON. [Vedere questo articolo per ulteriori informazioni sull'aggiunta di sink di hub di eventi per l'agente diagnostica Azure Linux](../virtual-machines/linux/diagnostic-extension.md#protected-settings).

> [!NOTE]
> Non è possibile impostare il flusso di dati di monitoraggio del sistema operativo guest a un hub di eventi nel portale. In alternativa, è necessario modificare manualmente il file di configurazione.

### <a name="stream-windows-data-to-an-event-hub"></a>Flusso di dati di Windows a un hub eventi

Il [agente di Windows Azure diagnostica](./azure-diagnostics.md) può essere utilizzato per inviare i dati da un computer Windows a un hub eventi di monitoraggio. Questo scopo, aggiungere l'hub di eventi come sink nella sezione del file di configurazione di WAD privateConfig. [Vedere questo articolo per ulteriori informazioni sull'aggiunta di sink di hub di eventi per l'agente di diagnostica di Microsoft Azure](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> Non è possibile impostare il flusso di dati di monitoraggio del sistema operativo guest a un hub di eventi nel portale. In alternativa, è necessario modificare manualmente il file di configurazione.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>Impostazione dati di monitoraggio delle applicazioni da trasmettere all'hub eventi

Dati di monitoraggio applicazione richiede che il codice è instrumentato con un SDK, pertanto non è una soluzione generica per l'applicazione di routing a un hub di eventi in Azure i dati di monitoraggio. Tuttavia, [Azure Application Insights](../application-insights/app-insights-overview.md) un servizio che può essere utilizzato per raccogliere dati di Azure a livello di applicazione. Se si usa Application Insights, è possibile trasmettere i dati di monitoraggio a un hub eventi effettuando le seguenti operazioni:

1. [Impostare l'esportazione continua](../application-insights/app-insights-export-telemetry.md) dei dati di Application Insights per un account di archiviazione.

2. Configurare un'App di logica di attivazione del timer che [estrae i dati dall'archiviazione blob](../connectors/connectors-create-api-azureblobstorage.md#use-an-action) e [inserisce un messaggio all'hub eventi](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Che cosa può fare con i dati inviati per l'hub di eventi di monitoraggio

Routing dei dati di monitoraggio a un hub eventi con Monitor di Azure consente di integrare facilmente con partner SIEM e strumenti di monitoraggio. La maggior parte degli strumenti richiedono la stringa di connessione hub eventi e determinate autorizzazioni per la sottoscrizione di Azure per leggere i dati dall'hub di eventi. Di seguito è riportato un elenco non esaustivo degli strumenti con l'integrazione di monitoraggio di Azure:

* **IBM QRadar** -QRadar il connettore per gli hub eventi è attualmente in versione beta. Non appena sarà disponibile per l'installazione manualmente da IBM correggere centrale o automaticamente mediante il processo di aggiornamento automatico del QRadar.
* **Splunk** - [il componente aggiuntivo di monitoraggio di Azure per Splunk](https://splunkbase.splunk.com/app/3534/) è disponibile in Splunkbase e un progetto open source. [La documentazione è qui](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
* **SumoLogic** -istruzioni per la configurazione di SumoLogic utilizzare i dati da un hub eventi sono [disponibili in questo punto](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>Fasi successive
* [Archive the Activity Log to a storage account](monitoring-archive-activity-log.md) (Archiviare il log attività in un account di archiviazione)
* Leggere la [panoramica sul log attività di Azure](monitoring-overview-activity-logs.md)
* [Set up an alert based on an Activity Log event](insights-auditlog-to-webhook-email.md) (Configurare un avviso in base a un evento del log attività)

