---
title: Eliminare e ripristinare l'area di lavoro di Azure Log Analytics | Microsoft Docs
description: Informazioni su come eliminare l'area di lavoro Log Analytics, se ne è stata creata una in una sottoscrizione personale, o come ristrutturare il modello dell'area di lavoro.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/11/2019
ms.openlocfilehash: f15e9c2a5980c8fb6d98f7bf9187b030e6910523
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932378"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Eliminare e ripristinare l'area di lavoro di Azure Log Analytics

Questo articolo illustra il concetto di eliminazione temporanea dell'area di lavoro di Azure Log Analytics e come ripristinare l'area di lavoro eliminata. 

## <a name="considerations-when-deleting-a-workspace"></a>Considerazioni sull'eliminazione di un'area di lavoro

Quando si elimina un'area di lavoro Log Analytics, viene eseguita un'operazione di eliminazione temporanea per consentire il ripristino dell'area di lavoro, inclusi i dati e gli agenti connessi entro 14 giorni, indipendentemente dal fatto che l'eliminazione sia stata accidentale o intenzionale. Dopo il periodo di eliminazione temporanea, l'area di lavoro e i relativi dati non sono recuperabili e vengono accodati per l'eliminazione permanente entro 30 giorni.

Si desidera prestare attenzione quando si elimina un'area di lavoro perché potrebbero essere presenti dati e configurazioni importanti che potrebbero influire negativamente sull'operazione del servizio. Esaminare gli agenti, le soluzioni e gli altri servizi e origini di Azure che archiviano i dati in Log Analytics, ad esempio:

* Soluzioni di gestione
* Automazione di Azure
* Agenti in esecuzione in macchine virtuali Windows e Linux
* Agenti in esecuzione in computer Windows e Linux nell'ambiente
* System Center Operations Manager

L'operazione di eliminazione temporanea Elimina la risorsa dell'area di lavoro e le autorizzazioni degli utenti associati vengono interrotte. Se gli utenti sono associati ad altre aree di lavoro, possono continuare a usare Log Analytics con le altre aree di lavoro.

## <a name="soft-delete-behavior"></a>Comportamento della funzione di eliminazione temporanea

L'operazione di eliminazione dell'area di lavoro consente di rimuovere l'area di lavoro Gestione risorse risorsa, ma la configurazione e i dati vengono conservati per 14 giorni, assicurando allo stesso tempo l'eliminazione dell'area di lavoro. Gli agenti e i gruppi di gestione System Center Operations Manager configurati per l'area di lavoro rimangono in uno stato orfano durante il periodo di eliminazione temporanea. Il servizio fornisce inoltre un meccanismo per il ripristino dell'area di lavoro eliminata, inclusi i dati e le risorse connesse, evitando essenzialmente l'eliminazione.

> [!NOTE] 
> Le soluzioni installate e i servizi collegati come l'account di automazione di Azure vengono rimossi definitivamente dall'area di lavoro in fase di eliminazione e non possono essere recuperati. Queste devono essere riconfigurate dopo l'operazione di ripristino per portare l'area di lavoro allo stato configurato in precedenza.

È possibile eliminare un'area di lavoro usando [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), l' [API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)o nel [portale di Azure](https://portal.azure.com).

### <a name="delete-workspace-in-azure-portal"></a>Elimina area di lavoro in portale di Azure

1. Per accedere, passare alla [portale di Azure](https://portal.azure.com). 
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.
3. Nell'elenco delle aree di lavoro Log Analytics selezionare un'area di lavoro, quindi fare clic su **Elimina** nella parte superiore del riquadro centrale.
   ![Delete option from Workspace properties pane](media/delete-workspace/log-analytics-delete-workspace.png) (Opzione Elimina dal riquadro delle proprietà dell'area di lavoro)
4. Fare clic su **Sì** nella finestra di messaggio di conferma visualizzata che richiede di confermare l'eliminazione dell'area di lavoro.
   ![Confermare l'eliminazione dell'area di lavoro](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>Ripristina area di lavoro

Se si dispone delle autorizzazioni di collaboratore per la sottoscrizione e il gruppo di risorse in cui l'area di lavoro è stata associata prima dell'operazione di eliminazione temporanea, è possibile ripristinarla durante il periodo di eliminazione temporanea, inclusi i dati, la configurazione e gli agenti connessi. Dopo il periodo di eliminazione temporanea, l'area di lavoro non è reversibile e assegnata per l'eliminazione permanente. I nomi delle aree di lavoro eliminate vengono conservati durante il periodo di eliminazione temporanea e non possono essere usati quando si tenta di creare una nuova area di lavoro.  

Per ripristinare un'area di lavoro, è possibile ricreare l'area di lavoro usando l'area di lavoro crea metodi [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) o l' [API REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) , purché queste proprietà vengano popolate con i dettagli dell'area di lavoro eliminati, tra cui:

* ID sottoscrizione
* Nome del gruppo di risorse
* Nome dell'area di lavoro
* Area geografica

> [!NOTE]
> * Il ripristino dell'area di lavoro non è supportato nell' [portale di Azure](https://portal.azure.com). 
> * La ricreazione di un'area di lavoro durante il periodo di eliminazione temporanea indica che il nome dell'area di lavoro è già in uso. 
> 
