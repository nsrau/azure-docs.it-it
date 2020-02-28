---
title: Eliminare e ripristinare l'area di lavoro di Azure Log Analytics | Microsoft Docs
description: Informazioni su come eliminare l'area di lavoro Log Analytics, se ne è stata creata una in una sottoscrizione personale, o come ristrutturare il modello dell'area di lavoro.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 6f50450702c9ecdc1c1d910514d94e0a759176b8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670475"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Eliminare e ripristinare l'area di lavoro di Azure Log Analytics

Questo articolo illustra il concetto di eliminazione temporanea dell'area di lavoro di Azure Log Analytics e come ripristinare l'area di lavoro eliminata. 

## <a name="considerations-when-deleting-a-workspace"></a>Considerazioni sull'eliminazione di un'area di lavoro

Quando si elimina un'area di lavoro Log Analytics, viene eseguita un'operazione di eliminazione temporanea per consentire il ripristino dell'area di lavoro, inclusi i dati e gli agenti connessi entro 14 giorni, indipendentemente dal fatto che l'eliminazione sia stata accidentale o intenzionale. Dopo il periodo di eliminazione temporanea, la risorsa dell'area di lavoro e i relativi dati sono non recuperabili. i dati vengono accodati per l'eliminazione permanente e completamente eliminati entro 30 giorni. Il nome dell'area di lavoro è "rilasciato" ed è possibile usarlo per creare una nuova area di lavoro.

> [!NOTE]
> Se si vuole eseguire l'override del comportamento di eliminazione temporanea ed eliminare definitivamente l'area di lavoro, seguire la procedura descritta in [eliminazione permanente dell'area di lavoro](#permanent-workspace-delete).

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

### <a name="azure-portal"></a>Portale di Azure

1. Per accedere, passare alla [portale di Azure](https://portal.azure.com). 
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.
3. Nell'elenco delle aree di lavoro Log Analytics selezionare un'area di lavoro, quindi fare clic su **Elimina** nella parte superiore del riquadro centrale.
   ![Delete option from Workspace properties pane](media/delete-workspace/log-analytics-delete-workspace.png) (Opzione Elimina dal riquadro delle proprietà dell'area di lavoro)
4. Fare clic su **Sì** nella finestra di messaggio di conferma visualizzata che richiede di confermare l'eliminazione dell'area di lavoro.
   ![Confermare l'eliminazione dell'area di lavoro](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Eliminazione permanente dell'area di lavoro
Il metodo di eliminazione temporanea potrebbe non rientrare in alcuni scenari, ad esempio sviluppo e test, in cui è necessario ripetere una distribuzione con le stesse impostazioni e il nome dell'area di lavoro. In questi casi è possibile eliminare definitivamente l'area di lavoro e "eseguire l'override" del periodo di eliminazione temporanea. L'operazione di eliminazione dell'area di lavoro permanente rilascia il nome dell'area di lavoro ed è possibile creare una nuova area di lavoro con lo stesso nome.


> [!IMPORTANT]
> Usare l'operazione di eliminazione dell'area di lavoro permanente con cautela poiché è irreversibile e non sarà possibile ripristinare l'area di lavoro e i relativi dati.

L'eliminazione permanente dell'area di lavoro può attualmente essere eseguita tramite l'API REST.

> [!NOTE]
> Qualsiasi richiesta API deve includere un token di autorizzazione di porta nell'intestazione della richiesta.
>
> È possibile acquisire il token usando:
> - [Registrazioni per l'app](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Passare a portale di Azure usando la console per gli sviluppatori (F12) nel browser. Esaminare una delle istanze di **batch?** per la stringa di autenticazione in **intestazioni della richiesta**. Questa operazione sarà nel criterio *autorizzazione: bearer <token>* . Copiare e aggiungere questo oggetto alla chiamata API, come illustrato negli esempi.
> - Passare al sito della documentazione REST di Azure. Premere **prova** su qualsiasi API, copiare il token di porta e aggiungerlo alla chiamata API.
Per eliminare definitivamente l'area di lavoro, usare le [aree di lavoro-Elimina]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) chiamata all'API REST con un Tag Force:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Dove ' eyJ0eXAiOiJKV1Qi. .' rappresenta il token di autorizzazione completo.

## <a name="recover-workspace"></a>Ripristina area di lavoro

Se si dispone delle autorizzazioni di collaboratore per la sottoscrizione e il gruppo di risorse in cui l'area di lavoro è stata associata prima dell'operazione di eliminazione temporanea, è possibile ripristinarla durante il periodo di eliminazione temporanea, inclusi i dati, la configurazione e gli agenti connessi. Dopo il periodo di eliminazione temporanea, l'area di lavoro non è reversibile e assegnata per l'eliminazione permanente. I nomi delle aree di lavoro eliminate vengono conservati durante il periodo di eliminazione temporanea e non possono essere usati quando si tenta di creare una nuova area di lavoro.  

Per ripristinare un'area di lavoro, è possibile ricrearla usando i metodi di creazione dell'area di lavoro seguenti: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) o l' [API REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) , purché le proprietà seguenti vengano popolate con i dettagli dell'area di lavoro eliminati:

* ID sottoscrizione
* Nome del gruppo di risorse
* Nome dell'area di lavoro
* Region

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

L'area di lavoro e tutti i relativi dati vengono restituiti dopo l'operazione di ripristino. Le soluzioni e i servizi collegati sono stati rimossi definitivamente dall'area di lavoro al momento dell'eliminazione ed è necessario riconfigurarli per portare l'area di lavoro allo stato configurato in precedenza. Alcuni dati potrebbero non essere disponibili per la query dopo il ripristino dell'area di lavoro fino a quando le soluzioni associate non vengono reinstallate e i relativi schemi vengono aggiunti all'area di lavoro.

> [!NOTE]
> * Il ripristino dell'area di lavoro non è supportato nell' [portale di Azure](https://portal.azure.com). 
> * La ricreazione di un'area di lavoro durante il periodo di eliminazione temporanea indica che il nome dell'area di lavoro è già in uso. 
> 
