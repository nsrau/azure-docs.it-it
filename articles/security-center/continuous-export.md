---
title: Esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure in SIEM Documenti Microsoft
description: Questo articolo spiega come impostare l'esportazione continua di avvisi di sicurezza e consigli per i SIEM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158971"
---
# <a name="export-security-alerts-and-recommendations"></a>Esportare avvisi e raccomandazioni di sicurezza

Il Centro sicurezza di Azure genera avvisi e suggerimenti dettagliati per la sicurezza. È possibile visualizzarli nel portale o tramite strumenti a livello di codice. Potrebbe anche essere necessario esportare queste informazioni o inviarle ad altri strumenti di monitoraggio nell'ambiente. 

In questo articolo viene descritto il set di strumenti che consentono di esportare avvisi e suggerimenti manualmente o in modo continuo e continuato.

Utilizzando questi strumenti è possibile:

* Esportazione continua nelle aree di lavoro di Log Analytics
* Esportazione continua in Hub eventi di Azure (per integrazioni con SIEM di terze parti)Continuously export to Azure Event Hubs (for integrations with third-party SIEMs)
* Esporta in CSV (una volta)


## <a name="setting-up-a-continuous-export"></a>Impostazione di un'esportazione continua

I passaggi seguenti sono necessari se si sta configurando un'esportazione continua nell'area di lavoro di Log Analytics o negli hub eventi di Azure.The steps below are necessary whether you're setting up a continuous export to Log Analytics workspace or Azure Event Hubs.

1. Dalla barra laterale del Centro sicurezza, seleziona **Impostazioni & dei prezzi**.

1. Selezionare la sottoscrizione specifica per la quale si desidera configurare l'esportazione dei dati.
    
1. Dalla barra laterale della pagina delle impostazioni per la sottoscrizione, selezionare **Esportazione continua**.

    [Opzioni di esportazione nel Centro sicurezza di AzureExport options in Azure Security Center ![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Qui potete vedere le opzioni di esportazione. C'è una scheda per ogni destinazione di esportazione disponibile. 

1. Seleziona il tipo di dati che desideri esportare e scegli tra i filtri per ogni tipo (ad esempio, esporta solo avvisi con gravità elevata).

1. Dall'area "Esporta destinazione", scegli dove vuoi salvare i dati. I dati possono essere salvati in una destinazione in una sottoscrizione diversa, ad esempio in un'istanza dell'hub eventi centrale o in un'area di lavoro centrale di Log Analytics.Data can be saved in a target on a different subscription (for example on a Central Event Hub instance or a central Log Analytics workspace).

1. Fare clic su **Salva**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Configurazione dell'integrazione SIEM tramite Hub eventi di AzureConfiguring SIEM integration via Azure Event Hubs

Hub eventi di Azure è un'ottima soluzione per l'utilizzo a livello di codice di dati di streaming. Per gli avvisi e i suggerimenti del Centro sicurezza di Azure, è il modo preferito per l'integrazione con un SIEM di terze parti.

> [!NOTE]
> Il metodo più efficace per trasmettere i dati di monitoraggio a strumenti esterni nella maggior parte dei casi è l'uso di hub eventi di Azure.The most effective method to stream monitoring data to external tools in most cases is using Azure Event Hubs. [Questo articolo](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fornisce una breve descrizione su come è possibile trasmettere i dati di monitoraggio da origini diverse a un hub eventi e collegamenti a indicazioni dettagliate.

> [!NOTE]
> Se in precedenza sono stati esportati gli avvisi del Centro sicurezza in un SIEM usando il log attività di Azure, la procedura seguente sostituisce tale metodologia.

Per visualizzare gli schemi di evento dei tipi di dati esportati, visitare gli schemi eventi [dell'Hub eventi](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Per l'integrazione con un SIEM 

Dopo aver configurato l'esportazione continua dei dati del Centro sicurezza scelti negli hub eventi di Azure, è possibile configurare il connettore appropriato per il SIEM:

* **Azure Sentinel:** usare il [connettore dati](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) degli avvisi del Centro sicurezza di Azure nativo offerto.
* **Splunk** - Usare il [componente aggiuntivo Monitoraggio di Azure per Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - Utilizzare [un'origine di registro configurata manualmente](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – Utilizzare [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Inoltre, se si desidera spostare automaticamente i dati esportati continuamente dall'hub eventi configurato ad Azure Data Explorer, usare le istruzioni in Inserimento dati [dall'Hub eventi in Azure Data Explorer.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Esportazione continua in un'area di lavoro di Log AnalyticsContinuous export to a Log Analytics workspace

Se si vuole analizzare i dati del Centro sicurezza di Azure in un'area di lavoro di Log Analytics o usare gli avvisi di Azure insieme al Centro sicurezza, configurare l'esportazione continua nell'area di lavoro di Log Analytics.If you want to analyze Azure Security Center data inside a Log Analytics workspace or use Azure alerts together with Security Center, setup continuous export to your Log Analytics workspace.

Per esportare in un'area di lavoro di Log Analytics, è necessario che le soluzioni Log Analytics del Centro sicurezza siano abilitate nell'area di lavoro. Se si usa il portale di Azure, la soluzione di livello gratuito del Centro sicurezza viene abilitata automaticamente quando si abilita l'esportazione continua. Tuttavia, se si stanno configurando le impostazioni di esportazione continua a livello di codice, è necessario selezionare manualmente il piano tariffario gratuito o standard per l'area di lavoro richiesta all'interno **di Impostazioni di & dei**prezzi .  

### <a name="log-analytics-tables-and-schemas"></a>Tabelle e schemi di Log Analytics

Gli avvisi e i suggerimenti per la sicurezza vengono archiviati rispettivamente nelle tabelle SecurityAlert e *SecurityRecommendations.Security* alerts and recommendations are stored in the *SecurityAlert* and SecurityRecommendations tables respectively. Il nome della soluzione log Analytics contenente queste tabelle dipende dal fatto che si è a livello gratuito o standard (vedere [prezzi):](security-center-pricing.md)Security('Security and Audit') o SecurityCenterFree.

![La tabella "SecurityAlert" in Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Per visualizzare gli schemi di evento dei tipi di dati esportati, visitare gli schemi della [tabella di Log Analytics](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Visualizzare gli avvisi e i suggerimenti per la sicurezza esportati in Monitoraggio di AzureView exported security alerts and recommendations in Azure Monitor

In alcuni casi, è possibile scegliere di visualizzare gli avvisi e/o i consigli di sicurezza esportati in [Monitoraggio di Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) 

Monitoraggio di Azure offre un'esperienza di avviso unificata per un'ampia gamma di avvisi di Azure, tra cui log di diagnostica, avvisi di metrica e avvisi personalizzati basati sulle query dell'area di lavoro di Log Analytics.Azure Monitor provides a unified alerting experience for a variety of Azure alerts including Diagnostic Log, Metric alerts, and custom alerts based on Log Analytics workspace queries.

Per visualizzare avvisi e consigli dal Centro sicurezza in Monitoraggio di Azure, configurare una regola di avviso basata sulle query di Log Analytics (avviso di log):To view alerts and recommendations from Security Center in Azure Monitor, configure an Alert rule based on Log Analytics queries (Log Alert):

1. Nella pagina **Avvisi** di Monitoraggio di Azure fare clic su **Nuova regola di avviso**.

    ![Pagina degli avvisi di Azure MonitorAzure Monitor's alerts page](./media/continuous-export/azure-monitor-alerts.png)

1. Nella pagina Crea regola configurare la nuova regola (nello stesso modo in cui si configura una regola di avviso del [log in Monitoraggio di Azure):](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)

    * Per **Risorsa**, selezionare l'area di lavoro di Log Analytics in cui sono stati esportati avvisi e suggerimenti per la sicurezza.

    * Per **Condizione**, selezionare **Ricerca log personalizzata**. Nella pagina visualizzata configurare la query, il periodo di ricerca e il periodo di frequenza. Nella query di ricerca è possibile digitare *SecurityAlert* o *SecurityRecommendation* per eseguire query sui tipi di dati esportati continuamente dal Centro sicurezza quando si abilita la funzionalità Esportazione continua in Log Analytics. 
    
    * Facoltativamente, configurare il gruppo di [azioni](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) che si desidera attivare. I gruppi di azioni possono attivare l'invio di posta elettronica, ticket ITSM, WebHook e altro ancora.
    ![Regola di avviso di Monitoraggio di AzureAzure Monitor alert rule](./media/continuous-export/azure-monitor-alert-rule.png)

Gli avvisi o i consigli del Centro sicurezza di Azure (a seconda della configurazione) verranno visualizzati in Avvisi di Monitoraggio di Azure, con attivazione automatica di un gruppo di azioni (se specificato).

## <a name="manual-one-time-export-of-security-alerts"></a>Esportazione una tantera manuale degli avvisi di sicurezza

Per scaricare un report CSV per avvisi o suggerimenti, apri la pagina **Avvisi di sicurezza** o **Consigli** e fai clic sul pulsante Scarica **report CSV.**

[![Scaricare i dati degli avvisi come file CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Questi report contengono avvisi e suggerimenti per le risorse delle sottoscrizioni attualmente selezionate.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come configurare le esportazioni continue di avvisi e suggerimenti. Hai anche imparato a scaricare i dati degli avvisi come file CSV. 

Per il materiale correlato, vedere la seguente documentazione: 

- [Documentazione di Hub eventi di AzureAzure Event Hubs documentation](https://docs.microsoft.com/azure/event-hubs/)
- [Documentazione di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentazione di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/)
- [Automazione del flusso di lavoro e schemi di tipi di dati di esportazione continua](https://aka.ms/ASCAutomationSchemas)
