---
title: Estendere (copiare) avvisi dal portale di OMS ad Azure | Microsoft Docs
description: Panoramica del processo per la copia di avvisi dal portale di OMS in Avvisi di Azure e informazioni sulle principali preoccupazioni dei clienti.
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
ms.openlocfilehash: 356c1343443b33e565c65ef0693b8d8455ff1d1b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="extend-copy-alerts-from-oms-portal-into-azure"></a>Estendere (copiare) avvisi dal portale di OMS ad Azure
Nel portale di Operations Management Suite (OMS) vengono visualizzati solo gli avvisi di Log Analytics.  La nuova esperienza Avvisi è ora integrata nei diversi servizi e componenti di Microsoft Azure. La nuova esperienza disponibile come **Avvisi** in Monitoraggio di Azure all'interno del portale di Azure contiene avvisi del log attività, avvisi delle metriche e avvisi relativi ai log per Log Analytics e Application Insights. 


Alcuni utenti, tuttavia, usano Log Analytics e le funzionalità correlate, come gli avvisi, attraverso il [portale di Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Per consentire loro di gestire facilmente le altre risorse di Azure e al tempo stesso usare Log Analytics, Microsoft ha sempre controllato sistematicamente che le funzionalità del portale di OMS fossero disponibili anche nel portale di Azure. A tale proposito, il servizio Avvisi di Azure consente già agli utenti di gestire gli avvisi basati su query per Log Analytics. Per altre informazioni, vedere [Avvisi del log in Avvisi di Azure](monitor-alerts-unified-log.md). Nel servizio Avvisi disponibile in Monitoraggio di Azure, gli avvisi creati nel portale di OMS sono già elencati nell'area di lavoro appropriata di Log Analytics. Tuttavia, per modificare gli avvisi creati nel portale di OMS, gli utenti devono lasciare Azure e usare il portale di OMS e quindi tornare ad Azure se devono gestire un altro servizio. Per ovviare a questa difficoltà, Microsoft consente ora agli utenti di estendere gli avvisi dal portale di OMS ad Azure.

## <a name="benefits-of-extending-your-alerts"></a>Vantaggi offerti dall'estensione degli avvisi
Oltre a evitare la necessità di uscire dal portale di Azure, l'estensione degli avvisi dal portale di OMS ad Azure offre altri vantaggi rilevanti

- A differenza del portale di OMS, dove possono essere creati e visualizzati solo 250 avvisi, in Avvisi di Azure la limitazione relativa al numero di avvisi non è presente
- In Avvisi di Azure è possibile gestire, enumerare e visualizzare tutti i tipi di avvisi e non solo gli avvisi di Log Analytics come nel caso del portale di OMS
- In Avvisi di Azure vengono usati [gruppi di azioni](monitoring-action-groups.md), che consentono di associare a ogni avviso più azioni, incluse SMS, Chiamata vocale, Runbook di Automazione, Webhook, Connettore di Gestione dei servizi IT e altre ancora. Per gli avvisi di Log Analytics sono previste invece limitazioni sia per il numero di avvisi sia per il tipo di azioni possibili

## <a name="process-of-extending-your-alerts"></a>Processo di estensione degli avvisi
Il processo di estensione degli avvisi dal portale di OMS ad Azure **non** comporta la modifica della definizione, della query o della configurazione degli avvisi. L'unica modifica necessaria è dovuta al fatto che in Azure, tutte le azioni, come la notifica tramite posta elettronica, la chiamata webhook, l'esecuzione del runbook di automazione o la connessione allo strumento Gestione dei servizi IT, vengono eseguite tramite Gruppo di azioni. Pertanto, se l'avviso è associato al gruppo di azioni appropriato, verrà esteso ad Azure.

Poiché il processo di estensione non è distruttivo e non comporta interruzioni, Microsoft estenderà automaticamente gli avvisi creati nel portale di OMS agli avvisi di Azure a partire dal **23 aprile 2018**. Da questa data, Microsoft inizierà a pianificare l'estensione degli avvisi ad Azure e abiliterà gradualmente la gestione degli avvisi presenti nel portale di OMS dal portale di Azure. 

Quando vengono pianificati per l'estensione ad Azure, gli avvisi presenti in un'area di lavoro di Log Analytics continuano a funzionare, **senza** compromettere in alcun modo le attività di monitoraggio. Se pianificati, gli avvisi possono risultare temporaneamente non disponibili per la modifica, ma in questo breve intervallo è possibile continuare a creare nuovi avvisi in Azure. In questo breve periodo, se gli utenti provano a modificare o creare avvisi dal portale di OMS, hanno la possibilità di proseguire l'operazione in Azure Log Analytics o in Avvisi di Azure.

 ![Durante il periodo pianificato, le azioni dell'utente sugli avvisi vengono reindirizzate ad Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Per l'estensione degli avvisi dal portale di OMS ad Azure non è previsto alcun addebito e per l'uso di Avvisi di Azure per la generazione di avvisi di Log Analytics basati su query non verranno addebitati costi, purché siano rispettati i limiti e le condizioni indicati in [Prezzi di Monitoraggio di Azure ](https://azure.microsoft.com/en-us/pricing/details/monitor/)  

Gli utenti possono usufruire dei vantaggi offerti dall'estensione degli avvisi prima di questa data scegliendo volontariamente di rendere gli avvisi gestibili in Azure.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Come estendere volontariamente gli avvisi
Per consentire agli utenti di OMS di passare facilmente ad Avvisi di Azure, Microsoft ha creato strumenti per abilitare l'estensione degli avvisi. I clienti del portale di OMS possono estendere i propri avvisi ad Azure usando una procedura guidata nel portale di OMS o adottando un approccio a livello di codice basato su una nuova API. Per altre informazioni, vedere [Estensione degli avvisi ad Azure tramite il portale di OMS e l'API](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>Uso dopo l'estensione degli avvisi
Dopo l'estensione ad Azure, gli avvisi creati in Microsoft Operations Management Suite potranno essere gestiti direttamente da Azure, anche se continueranno a essere elencati nella sezione relativa all'impostazione degli avvisi nel portale di OMS, come illustrato di seguito:

 ![Avvisi elencati nel portale di OMS dopo l'estensione ad Azure](./media/monitor-alerts-extend/PostExtendList.png)

Per qualsiasi operazione di modifica o creazione degli avvisi nel portale di OMS, gli utenti verranno indirizzati in modo trasparente ad Avvisi di Azure. Gli avvisi continueranno a essere creati dall'[API di Log Analytics](../log-analytics/log-analytics-api-alerts.md) come in precedenza. L'unica piccola differenza è data dal fatto che, dopo l'estensione degli avvisi ad Azure, nella pianificazione è necessario associare i gruppi di azioni.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli strumenti per [avviare l'estensione degli avvisi da OMS ad Azure](monitoring-alerts-extend-tool.md)
* Altre informazioni sulla nuova [esperienza degli avvisi di Azure](monitoring-overview-unified-alerts.md)
* Informazioni sugli [avvisi del log in Avvisi di Azure](monitor-alerts-unified-log.md)
