---
title: Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure su SIEMs | Microsoft Docs
description: Questo articolo illustra come configurare l'esportazione continua degli avvisi di sicurezza e le raccomandazioni per SIEM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 3bfaa9f2961dca2b8b717b1506d112943910485a
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042324"
---
# <a name="export-security-alerts-and-recommendations"></a>Esportare avvisi e raccomandazioni di sicurezza

Il Centro sicurezza di Azure genera avvisi e raccomandazioni di sicurezza dettagliati. È possibile visualizzarli nel portale o tramite gli strumenti programmatici. Potrebbe anche essere necessario esportare queste informazioni o inviarle ad altri strumenti di monitoraggio nell'ambiente in uso. 

Questo articolo descrive il set di strumenti che consentono di esportare avvisi e consigli manualmente o in modo continuo e continuo.

Con questi strumenti è possibile:

* Esporta continuamente in aree di lavoro Log Analytics
* Esportazione continua in hub eventi di Azure (per integrazioni con SIEM di terze parti)
* Esporta in CSV (una volta)



## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Disponibile a livello generale|
|Prezzi|Livello gratuito|
|Ruoli e autorizzazioni necessari:|**Reader** nella sottoscrizione che contiene la configurazione di esportazione<br>**Ruolo di amministratore della sicurezza** nel gruppo di risorse (o **proprietario**)<br>Deve disporre anche delle autorizzazioni di scrittura per la risorsa di destinazione|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Gov per la Cina, altri gov|
|||



## <a name="setting-up-a-continuous-export"></a>Impostazione di un'esportazione continua

I passaggi seguenti sono necessari se si sta configurando un'esportazione continua in Log Analytics area di lavoro o hub eventi di Azure.

1. Dall'intestazione laterale del Centro sicurezza selezionare **prezzi & impostazioni**.

1. Selezionare la sottoscrizione specifica per la quale si desidera configurare l'esportazione dei dati.
    
1. Dall'intestazione laterale della pagina impostazioni per la sottoscrizione selezionare **esportazione continua**.

    [ ![ Opzioni di esportazione nel centro sicurezza di Azure](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) qui è possibile visualizzare le opzioni di esportazione. È disponibile una scheda per ogni destinazione di esportazione disponibile. 

1. Selezionare il tipo di dati che si desidera esportare e scegliere tra i filtri in ogni tipo (ad esempio, esportare solo gli avvisi con livello di gravità elevato).

1. Dall'area "Esporta destinazione" scegliere il percorso in cui salvare i dati. I dati possono essere salvati in una destinazione in una sottoscrizione diversa, ad esempio in un'istanza centrale dell'hub eventi o in un'area di lavoro centrale Log Analytics.

1. Fare clic su **Salva**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Configurazione dell'integrazione SIEM tramite hub eventi di Azure

Hub eventi di Azure è un'ottima soluzione per a livello che utilizzano i dati di streaming. Per gli avvisi e le raccomandazioni del Centro sicurezza di Azure, si tratta del metodo preferito per l'integrazione con SIEM di terze parti.

> [!NOTE]
> Il metodo più efficace per trasmettere i dati di monitoraggio agli strumenti esterni nella maggior parte dei casi consiste nell'usare hub eventi di Azure. [Questo articolo](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fornisce una breve descrizione di come è possibile trasmettere i dati di monitoraggio da origini diverse a un hub eventi e collegamenti a istruzioni dettagliate.

> [!NOTE]
> Se in precedenza sono stati esportati avvisi del Centro sicurezza a un SIEM usando il log attività di Azure, la procedura seguente sostituisce tale metodologia.

Per visualizzare gli schemi di eventi dei tipi di dati esportati, vedere gli [schemi degli eventi dell'hub eventi](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Per l'integrazione con un SIEM 

Dopo aver configurato l'esportazione continua dei dati del Centro sicurezza scelto in hub eventi di Azure, è possibile configurare il connettore appropriato per SIEM:

* **Azure Sentinel** : usare il [connettore dati](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) avvisi del Centro sicurezza di Azure nativo disponibile.
* **Splunk** -usare il [componente aggiuntivo monitoraggio di Azure per Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** : usare [un'origine di log configurata manualmente](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** : USA [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Inoltre, per spostare automaticamente i dati esportati dall'hub eventi configurato in Esplora dati di Azure, seguire le istruzioni in inserire i [dati dall'hub eventi in azure Esplora dati](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Esportazione continua in un'area di lavoro Log Analytics

Per analizzare i dati del Centro sicurezza di Azure all'interno di un'area di lavoro Log Analytics o usare gli avvisi di Azure insieme al centro sicurezza, configurare l'esportazione continua nell'area di lavoro Log Analytics.

Per eseguire l'esportazione in un'area di lavoro Log Analytics, è necessario che siano abilitate le soluzioni Log Analytics del Centro sicurezza nell'area di lavoro. Se si usa il portale di Azure, la soluzione del livello gratuito del Centro sicurezza viene abilitata automaticamente quando si Abilita l'esportazione continua. Tuttavia, se si configurano le impostazioni di esportazione continua a livello di codice, è necessario selezionare manualmente il piano tariffario gratuito o standard per l'area di lavoro necessaria dall'interno dei **prezzi & impostazioni**.  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabelle e schemi

Gli avvisi di sicurezza e le raccomandazioni vengono archiviati rispettivamente nelle tabelle *SecurityAlert* e *SecurityRecommendations* . Il nome della soluzione Log Analytics contenente queste tabelle varia a seconda che si tratti del livello gratuito o standard (vedere [prezzi](security-center-pricing.md)): sicurezza (' sicurezza e controllo ') o SecurityCenterFree.

![La tabella * SecurityAlert * in Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Per visualizzare gli schemi di eventi dei tipi di dati esportati, visitare gli [schemi della tabella log Analytics](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Visualizzare le raccomandazioni e gli avvisi di sicurezza esportati in monitoraggio di Azure

In alcuni casi, è possibile scegliere di visualizzare gli avvisi di sicurezza e/o le raccomandazioni esportati in [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Monitoraggio di Azure offre un'esperienza unificata per gli avvisi per un'ampia gamma di avvisi di Azure, tra cui log di diagnostica, avvisi sulle metriche e avvisi personalizzati basati sulle query Log Analytics area di lavoro.

Per visualizzare gli avvisi e le raccomandazioni dal centro sicurezza in monitoraggio di Azure, configurare una regola di avviso basata su query Log Analytics (avviso log):

1. Dalla pagina degli **avvisi** di monitoraggio di Azure fare clic su **nuova regola di avviso**.

    ![Pagina degli avvisi di monitoraggio di Azure](./media/continuous-export/azure-monitor-alerts.png)

1. Nella pagina Crea regola configurare la nuova regola, nello stesso modo in cui si configura una [regola di avviso del log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log):

    * Per **risorsa**selezionare l'area di lavoro log Analytics alla quale sono stati esportati gli avvisi di sicurezza e le raccomandazioni.

    * In **condizione**selezionare **Ricerca log personalizzata**. Nella pagina visualizzata configurare la query, il periodo lookback e il periodo di frequenza. Nella query di ricerca è possibile digitare *SecurityAlert* o *SecurityRecommendation* per eseguire una query sui tipi di dati a cui il Centro sicurezza Esporta continuamente quando si Abilita l'esportazione continua in log Analytics funzionalità. 
    
    * Facoltativamente, configurare il [gruppo di azioni](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) che si vuole attivare. I gruppi di azioni possono attivare l'invio di posta elettronica, i ticket ITSM, i webhook e altro ancora.
    ![Regola di avviso di monitoraggio di Azure](./media/continuous-export/azure-monitor-alert-rule.png)

Verranno ora visualizzati nuovi avvisi del Centro sicurezza di Azure o consigli (a seconda della configurazione) negli avvisi di monitoraggio di Azure, con attivazione automatica di un gruppo di azioni (se specificato).

## <a name="manual-one-time-export-of-security-alerts"></a>Esportazione manuale di avvisi di sicurezza

Per scaricare un report CSV per avvisi o raccomandazioni, aprire la pagina avvisi o **raccomandazioni** di **sicurezza** e fare clic sul pulsante **Scarica report CSV** .

[![Scarica i dati degli avvisi come file CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Questi report contengono avvisi e consigli per le risorse delle sottoscrizioni attualmente selezionate.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come configurare le esportazioni continue dei consigli e degli avvisi. Si è inoltre appreso come scaricare i dati degli avvisi come file CSV. 

Per materiale correlato, vedere la documentazione seguente: 

- [Documentazione di Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/)
- [Documentazione di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentazione di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/)
- [Schemi di automazione del flusso di lavoro e tipi di dati di esportazione continua](https://aka.ms/ASCAutomationSchemas)
