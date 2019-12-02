---
title: Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure su SIEMs | Microsoft Docs
description: Questo articolo illustra come configurare l'esportazione continua degli avvisi di sicurezza e le raccomandazioni per SIEM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f994f4ec6d41fa0aab37e36d713eaefb22e85b28
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665066"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>Esportare avvisi e raccomandazioni sulla sicurezza (anteprima)

Il Centro sicurezza di Azure genera avvisi e raccomandazioni di sicurezza dettagliati. È possibile visualizzarli nel portale o tramite gli strumenti programmatici. Potrebbe anche essere necessario esportare queste informazioni o inviarle ad altri strumenti di monitoraggio nell'ambiente in uso. 

Questo articolo descrive il set di strumenti (anteprima) che consentono di esportare avvisi e raccomandazioni manualmente o in modo continuo.

Con questi strumenti è possibile:

* Generare report dettagliati come CSV
* Esporta in aree di lavoro Log Analytics
* Esportare in hub eventi di Azure (per integrazioni con SIEM di terze parti)

## <a name="setting-up-a-continuous-export"></a>Impostazione di un'esportazione continua

1. Dall'intestazione laterale del Centro sicurezza fare clic su **prezzi & impostazioni**.

1. Selezionare la sottoscrizione specifica per la quale si desidera configurare l'esportazione dei dati.
    
1. Dalla barra laterale della pagina impostazioni per la sottoscrizione selezionare **esportazione continua (anteprima)** .

    [Opzioni di esportazione![nel centro sicurezza di Azure](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Qui vengono visualizzate le opzioni di esportazione. È disponibile una scheda per ogni destinazione di esportazione disponibile. 

1. Selezionare il tipo di dati che si desidera esportare e scegliere tra i filtri in ogni tipo (ad esempio, esportare solo gli avvisi con livello di gravità elevato).

1. Dall'area "Esporta destinazione" scegliere il percorso in cui salvare i dati. I dati possono essere salvati in una destinazione in una sottoscrizione diversa, ad esempio in un'istanza centrale dell'hub eventi o in un'area di lavoro centrale Log Analytics.

1. Fare clic su **Salva**

## <a name="continuous-export-through-azure-event-hubs"></a>Esportazione continua tramite hub eventi di Azure  

> [!NOTE]
> Il metodo più efficace per trasmettere i dati di monitoraggio agli strumenti esterni nella maggior parte dei casi consiste nell'usare hub eventi di Azure. [Questo articolo](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fornisce una breve descrizione di come è possibile trasmettere i dati di monitoraggio da origini diverse a un hub eventi e collegamenti a istruzioni dettagliate.

> [!NOTE]
> Se in precedenza sono stati esportati avvisi del Centro sicurezza a un SIEM usando il log attività di Azure, la procedura seguente sostituisce tale metodologia.

### <a name="to-integrate-with-a-siem"></a>Per l'integrazione con un SIEM 

Dopo aver configurato l'esportazione continua dei dati del Centro sicurezza scelto nell'hub eventi di Azure, è possibile configurare il connettore appropriato in SIEM seguendo le istruzioni riportate di seguito.

Seguire le istruzioni relative al SIEM da [Questa pagina](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) e usare il connettore pertinente:

* **Splunk** -usare il [componente aggiuntivo monitoraggio di Azure per Splunk](https://splunkbase.splunk.com/app/3534/)
* **IBM QRadar** : usare [un'origine di log configurata manualmente](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)
* **ArcSight** : USA [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Se si usa **Azure Sentinel**, usare il [connettore dati](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) avvisi del Centro sicurezza di Azure nativo disponibile.

Inoltre, per spostare automaticamente i dati esportati dall'hub eventi configurato in Esplora dati di Azure, seguire le istruzioni in inserire i [dati dall'hub eventi in azure Esplora dati](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).


## <a name="continuous-export-to-log-analytics-workspace"></a>Esportazione continua nell'area di lavoro Log Analytics

Per eseguire l'esportazione nell'area di lavoro Log Analytics, è necessario che le soluzioni Log Analytics livello gratuito o standard del Centro sicurezza siano abilitate nell'area di lavoro. Se si usa il portale di Azure, la soluzione del livello gratuito del Centro sicurezza viene abilitata automaticamente quando si Abilita l'esportazione continua. Tuttavia, se si configurano le impostazioni di esportazione continua a livello di codice, è necessario selezionare manualmente il piano tariffario gratuito o standard per l'area di lavoro necessaria dall'interno dei **prezzi & impostazioni**.  

Gli avvisi di sicurezza e le raccomandazioni vengono archiviati rispettivamente nelle tabelle *SecurityAlert* e *SecurityRecommendations* . Il nome della soluzione Log Analytics contenente queste tabelle varia a seconda che si tratti del livello gratuito o standard (vedere [prezzi](security-center-pricing.md)): Security o SecurityCenterFree.

![La tabella * SecurityAlert * in Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

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
    ![regola di avviso di monitoraggio di Azure](./media/continuous-export/azure-monitor-alert-rule.png)

Verranno ora visualizzati nuovi avvisi del Centro sicurezza di Azure o consigli (a seconda della configurazione) negli avvisi di monitoraggio di Azure, con attivazione automatica di un gruppo di azioni (se specificato).

## <a name="manual-one-time-export-of-security-alerts"></a>Esportazione manuale di avvisi di sicurezza

Per scaricare un report CSV per avvisi o raccomandazioni, aprire la pagina **avvisi** o **raccomandazioni** di sicurezza e fare clic sul pulsante **Scarica report CSV (anteprima)** .

[![scaricare i dati degli avvisi come file CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Questi report contengono avvisi e consigli per le risorse delle sottoscrizioni attualmente selezionate nel filtro directory + sottoscrizione nel portale di Azure: ![il filtro per la selezione di directory + sottoscrizione](./media/continuous-export/filter-for-export-csv.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come configurare le esportazioni continue dei consigli e degli avvisi. Si è inoltre appreso come scaricare i dati degli avvisi come file CSV. 

Per materiale correlato, vedere la documentazione seguente: 

- [Documentazione di hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/)
- [Documentazione di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentazione di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/)