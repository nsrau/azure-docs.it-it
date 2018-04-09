---
title: Estendere gli avvisi da OMS ad Azure | Microsoft Docs
description: Panoramica del processo di estensione degli avvisi da OMS ad Avvisi di Azure con informazioni dettagliate sui problemi comuni dei clienti.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 045a7f97d9c4d380e83325c04c209a6afcc761a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="extend-alerts-from-oms-into-azure"></a>Estendere gli avvisi da OMS ad Azure
La nuova esperienza Avvisi è ora integrata nei diversi servizi e componenti di Microsoft Azure. La nuova esperienza disponibile come **Avvisi** in Monitoraggio di Azure nel portale raggruppa in una stessa posizione gli avvisi del log attività e quelli relativi alle metriche e ai log per Log Analytics e Application Insights. 

Alcuni utenti, tuttavia, usano Log Analytics e le funzionalità correlate, come gli avvisi, attraverso [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Per consentire loro di gestire facilmente le altre risorse di Azure e al tempo stesso usare Log Analytics, Microsoft ha sempre controllato sistematicamente che le funzionalità di OMS fossero disponibili anche nel portale di Azure. A tale proposito, il servizio Avvisi di Azure consente già agli utenti di gestire gli avvisi basati su query per Log Analytics. Per altre informazioni, vedere [Avvisi del log in Avvisi di Azure](monitor-alerts-unified-log.md). Nel servizio Avvisi disponibile in Monitoraggio di Azure, gli avvisi creati in OMS sono già elencati nell'area di lavoro appropriata di Log Analytics. Tuttavia, per modificare gli avvisi creati in OMS, gli utenti devono lasciare Azure e usare OMS, quindi tornare ad Azure se devono gestire un altro servizio. Per ovviare a questa difficoltà, Microsoft consente ora agli utenti di estendere gli avvisi da OMS ad Azure.

## <a name="benefits-of-extending-your-alerts"></a>Vantaggi offerti dall'estensione degli avvisi
Oltre a evitare la necessità di uscire dal portale di Azure, l'estensione degli avvisi da OMS ad Azure offre altri vantaggi rilevanti.

- A differenza di OMS, dove possono essere creati e visualizzati solo 250 avvisi, in Avvisi di Azure la limitazione relativa al numero di avvisi non è presente.
- In Avvisi di Azure è possibile gestire, enumerare e visualizzare tutti i tipi di avvisi e non solo gli avvisi di Log Analytics come nel caso di OMS.
- In Avvisi di Azure vengono utilizzati [gruppi di azioni](monitoring-action-groups.md), che consentono di associare a ogni avviso più azioni, incluse SMS, Chiamata vocale, Runbook di Automazione, Webhook, Connettore di Gestione dei servizi IT e altre ancora. In OMS, al contrario, sono previste limitazioni sia per il numero di avvisi sia per il tipo di azioni possibili.

## <a name="process-of-extending-your-alerts"></a>Processo di estensione degli avvisi
Il processo di estensione da OMS ad Azure **non** comporta la modifica della definizione, della query o della configurazione degli avvisi. L'unica modifica necessaria è dovuta al fatto che in Azure, tutte le azioni, come la notifica tramite posta elettronica, la chiamata webhook, l'esecuzione del runbook di automazione o la connessione allo strumento Gestione dei servizi IT, vengono eseguite tramite Gruppo di azioni. Pertanto, se l'avviso è associato al gruppo di azioni appropriato, verrà esteso ad Azure.

Poiché il processo di estensione non è distruttivo e non comporta interruzioni, Microsoft estenderà gli avvisi creati in Operations Management Suite (OMS) automaticamente ad Azure a partire dal **23 aprile 2018**. Da questa data, Microsoft inizierà a pianificare l'estensione degli avvisi ad Azure e abiliterà gradualmente la gestione degli avvisi presenti in OMS dal portale di Azure. 

Quando vengono pianificati per l'estensione ad Azure, gli avvisi presenti in un'area di lavoro OMS continuano a funzionare, **senza** compromettere in alcun modo le attività di monitoraggio. Se pianificati, gli avvisi possono risultare temporaneamente non disponibili per la modifica, ma in questo breve intervallo è possibile continuare a creare nuovi avvisi in Azure. In questo breve periodo, se gli utenti provano a modificare o creare avvisi da OMS, hanno la possibilità di proseguire l'operazione in Azure Log Analytics o in Avvisi di Azure.

 ![Durante il periodo pianificato, le azioni dell'utente sugli avvisi vengono reindirizzate ad Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Per l'estensione degli avvisi da Operations Management Suite (OMS) ad Azure non è previsto alcun addebito e per l'uso di Avvisi di Azure per la generazione di avvisi di Log Analytics basati su query non verranno addebitati costi, purché siano rispettati i limiti e le condizioni indicati in [Prezzi di Monitoraggio di Azure ](https://azure.microsoft.com/en-us/pricing/details/monitor/).  

Gli utenti possono usufruire dei vantaggi offerti dall'estensione degli avvisi prima di questa data scegliendo volontariamente di rendere gli avvisi gestibili in Azure.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Come estendere volontariamente gli avvisi
Per consentire agli utenti di OMS di passare facilmente ad Avvisi di Azure, Microsoft ha creato strumenti per abilitare l'estensione degli avvisi. I clienti di Microsoft Operations Management Suite (OMS) possono estendere i propri avvisi ad Azure usando una procedura guidata nel portale di OMS o adottando un approccio a livello di codice basato su una nuova API. Per altre informazioni, vedere [Estensione degli avvisi ad Azure tramite il portale di OMS e l'API](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>Uso dopo l'estensione degli avvisi
Dopo l'estensione ad Azure, gli avvisi creati in Microsoft Operations Management Suite potranno essere gestiti direttamente da Azure, anche se continueranno a essere elencati nella sezione relativa all'impostazione degli avvisi nel portale di OMS, come illustrato di seguito:

 ![Avvisi elencati nel portale di OMS dopo l'estensione ad Azure](./media/monitor-alerts-extend/PostExtendList.png)

Per qualsiasi operazione di modifica o creazione degli avvisi nel portale di OMS, gli utenti verranno indirizzati in modo trasparente ad Avvisi di Azure. Gli avvisi continueranno a essere creati dall'[API di Log Analytics](../log-analytics/log-analytics-api-alerts.md) come in precedenza. L'unica piccola differenza è data dal fatto che, dopo l'estensione degli avvisi ad Azure, nella pianificazione è necessario associare i gruppi di azioni.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli strumenti per [avviare l'estensione degli avvisi da OMS ad Azure](monitoring-alerts-extend-tool.md)
* Altre informazioni sulla nuova [esperienza degli avvisi di Azure](monitoring-overview-unified-alerts.md)
* Informazioni sugli [avvisi del log in Avvisi di Azure](monitor-alerts-unified-log.md)
