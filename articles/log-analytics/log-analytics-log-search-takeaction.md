---
title: Operazioni su un runbook di automazione di Azure avviato dall'utente in Log Analytics | Microsoft Docs
description: Questo articolo descrive come eseguire un runbook di automazione on demand dai risultati della ricerca di Log Analytics.
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: ff938697add98f3d21b4971175432335ee2e39ba
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Eseguire operazioni con un runbook di automazione dai risultati della ricerca log di Log Analytics

Dai risultati della ricerca log di Azure Log Analytics è ora possibile selezionare **Take action** (Intervieni) per eseguire un runbook di automazione.  Il runbook consente di risolvere il problema o eseguire altre operazioni, come raccogliere informazioni sulla risoluzione del problema, inviare un messaggio di posta elettronica o creare una richiesta di servizio. 

## <a name="components-and-features-used"></a>Componenti e le funzionalità usati
* [Account di automazione di Azure](../automation/automation-offering-get-started.md)
* [Area di lavoro di Log Analytics](../log-analytics/log-analytics-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Per avviare il runbook dalla ricerca log

Per intervenire su un evento e avviare un runbook dai risultati della ricerca log, è necessario prima creare una ricerca log, dai cui risultati è possibile richiamare un runbook on demand.  A questo scopo, è possibile usare la funzionalità di ricerca log disponibile in Azure o nel [portale di OMS](../log-analytics/log-analytics-log-searches.md).  In questo esempio si eseguirà una ricerca log dal portale di Azure con una dimostrazione di base di questa funzionalità.

1. Nel portale di Azure scegliere **Altri servizi** dal menu Hub e selezionare **Log Analytics**.  
2. Nel pannello di Log Analytics selezionare l'area di lavoro personale e nel pannello dell'area di lavoro selezionare **Log search** (Ricerca log).  
3. Nel pannello di ricerca log eseguire una ricerca log.  
4. Nei risultati della ricerca log fare clic sui puntini di sospensione a sinistra di uno dei campi disponibili e nella finestra popup visualizzata selezionare **Take action on** (Intervieni su).<br><br> ![Selezione dell'azione di intervento dai risultati della ricerca](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
5. Nel pannello Take action (Intervieni) selezionare **Run a runbook** **(Esegui un runbook)** e nel pannello visualizzato selezionare un runbook da eseguire.  È possibile selezionare qualsiasi runbook disponibile nell'account di automazione associato all'area di lavoro Log Analytics.  Tenere presente quanto segue:

    * I runbook sono organizzati per tag
    * I valori dei parametri di input dei runbook possono essere selezionati direttamente dai campi dei risultati della ricerca.  Viene visualizzato un elenco a discesa con tutti i campi disponibili nella pagina dei risultati tra cui scegliere.  
    * È possibile scegliere di eseguire il runbook su un [ruolo di lavoro ibrido per runbook](../automation/automation-hybrid-runbook-worker.md) installato nel computer in cui si è verificato il problema, se si dispone di un gruppo di ruoli di lavoro ibridi corrispondente in cui sia contenuto solo questo computer come membro.  Se il nome del gruppo di ruoli di lavoro ibridi corrisponde al nome del computer presente nei risultati della ricerca log, il gruppo viene selezionato automaticamente.    

6. Dopo aver fatto clic su **Esegui** verrà visualizzato il pannello del processo di runbook, da cui sarà possibile rivedere lo stato del processo.   

Se si seleziona un runbook configurato per essere [chiamato da un avviso di Log Analytics](../automation/automation-invoke-runbook-from-omsla-alert.md), presenta un parametro di input denominato **WebhookData** di tipo **Oggetto**.  Se il parametro di input è obbligatorio, è necessario passare i risultati della ricerca al runbook, in modo che possa convertire la stringa in formato JSON in un tipo di oggetto che consenta di filtrare in base a elementi specifici a cui si farà riferimento nelle attività del runbook.  A questo scopo, è necessario scegliere **Search result (Object)** (Risultati ricerca (Oggetto)) dall'elenco a discesa.<br><br> ![Selezionare l'oggetto dati Webhook per il parametro del runbook](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Passaggi successivi

* Vedere il [riferimento alla ricerca nei log di Log Analytics](log-analytics-search-reference.md) per visualizzare tutti i campi di ricerca e i facet disponibili in Log Analytics.
* Per informazioni su come richiamare automaticamente un runbook di automazione, rivedere l'articolo [Chiamata di un runbook di automazione di Azure da un avviso di OMS Log Analytics](../automation/automation-invoke-runbook-from-omsla-alert.md).  