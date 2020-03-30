---
title: Eliminare e ripristinare l'area di lavoro di Azure Log Analytics. Documenti Microsoft
description: Informazioni su come eliminare l'area di lavoro Log Analytics, se ne è stata creata una in una sottoscrizione personale, o come ristrutturare il modello dell'area di lavoro.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: ead0ac04fbd2244fce97dd043ebd44f24fb0f67f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054926"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Eliminare e ripristinare l'area di lavoro di Azure Log AnalyticsDelete and recover Azure Log Analytics workspace

Questo articolo illustra il concetto di eliminazione temporanea dell'area di lavoro di Azure Log Analytics e come ripristinare l'area di lavoro eliminata. 

## <a name="considerations-when-deleting-a-workspace"></a>Considerazioni sull'eliminazione di un'area di lavoro

Quando si elimina un'area di lavoro di Log Analytics, viene eseguita un'operazione di eliminazione temporanea per consentire il ripristino dell'area di lavoro, inclusi i dati e gli agenti connessi entro 14 giorni, indipendentemente dal fatto che l'eliminazione sia stata accidentale o intenzionale. Dopo il periodo di eliminazione temporanea, la risorsa dell'area di lavoro e i relativi dati non sono recuperabili: i dati vengono accodati per l'eliminazione permanente e completamente eliminati entro 30 giorni. Il nome dell'area di lavoro è 'rilasciato' ed è possibile utilizzarlo per creare una nuova area di lavoro.

> [!NOTE]
> Se si desidera ignorare il comportamento di eliminazione temporanea ed eliminare l'area di lavoro in modo permanente, seguire la procedura descritta in [Eliminazione permanente dell'area di lavoro](#permanent-workspace-delete).

Si desidera prestare attenzione quando si elimina un'area di lavoro perché potrebbero esserci dati e configurazioni importanti che potrebbero influire negativamente sull'operazione del servizio. Esaminare gli agenti, le soluzioni e altri servizi e origini di Azure che archiviano i dati in Log Analytics, ad esempio:Review what agents, solutions, and other Azure services and sources that store their data in Log Analytics, such as:

* Soluzioni di gestione
* Automazione di Azure
* Agenti in esecuzione in macchine virtuali Windows e Linux
* Agenti in esecuzione in computer Windows e Linux nell'ambiente
* System Center Operations Manager

L'operazione di eliminazione temporanea elimina la risorsa dell'area di lavoro e l'autorizzazione di tutti gli utenti associati viene interrotta. Se gli utenti sono associati ad altre aree di lavoro, possono continuare a usare Log Analytics con le altre aree di lavoro.

## <a name="soft-delete-behavior"></a>Comportamento della funzione di eliminazione temporanea

L'operazione di eliminazione dell'area di lavoro rimuove la risorsa Resource Manager dell'area di lavoro, ma la configurazione e i dati vengono mantenuti per 14 giorni, assegnando l'aspetto che l'area di lavoro viene eliminata. Tutti gli agenti e i gruppi di gestione di System Center Operations Manager configurati per la creazione di report nell'area di lavoro rimangono in uno stato orfano durante il periodo di eliminazione temporanea. Il servizio fornisce inoltre un meccanismo per il ripristino dell'area di lavoro eliminata, inclusi i dati e le risorse connesse, essenzialmente annullando l'eliminazione.

> [!NOTE] 
> Le soluzioni installate e i servizi collegati come l'account di Automazione di Azure vengono rimossi definitivamente dall'area di lavoro in fase di eliminazione e non possono essere recuperati. Questi devono essere riconfigurati dopo l'operazione di ripristino per portare l'area di lavoro allo stato configurato in precedenza.

È possibile eliminare un'area di lavoro tramite [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [l'API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)o nel portale di [Azure.](https://portal.azure.com)

### <a name="azure-portal"></a>Portale di Azure

1. Per accedere, passare al portale di [Azure](https://portal.azure.com). 
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.
3. Nell'elenco delle aree di lavoro di Log Analytics selezionare un'area di lavoro e quindi fare clic su **Elimina** nella parte superiore del riquadro centrale.
   ![Delete option from Workspace properties pane](media/delete-workspace/log-analytics-delete-workspace.png) (Opzione Elimina dal riquadro delle proprietà dell'area di lavoro)
4. Fare clic su **Sì** nella finestra di messaggio di conferma visualizzata che richiede di confermare l'eliminazione dell'area di lavoro.
   ![Confermare l'eliminazione dell'area di lavoro](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Eliminazione permanente dell'area di lavoro
Il metodo di eliminazione temporanea potrebbe non rientrare in alcuni scenari, ad esempio lo sviluppo e il test, in cui è necessario ripetere una distribuzione con le stesse impostazioni e lo stesso nome dell'area di lavoro. In questi casi è possibile eliminare definitivamente l'area di lavoro e "ignorare" il periodo di eliminazione temporanea. L'operazione di eliminazione permanente dell'area di lavoro rilascia il nome dell'area di lavoro ed è possibile creare una nuova area di lavoro con lo stesso nome.


> [!IMPORTANT]
> Utilizzare l'operazione di eliminazione permanente dell'area di lavoro con cautela poiché è irreversibile e non sarà possibile ripristinare l'area di lavoro e i relativi dati.

L'eliminazione permanente dell'area di lavoro può attualmente essere eseguita tramite l'API REST.

> [!NOTE]
> Qualsiasi richiesta API deve includere un token di autorizzazione Bearer nell'intestazione della richiesta.
>
> È possibile acquisire il token utilizzando:You can acquire the token using:
> - [Registrazioni per l'app](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Passare al portale di Azure usando la console dello sviluppatore (F12) nel browser. Cercare in una delle istanze **batch?** la stringa di autenticazione in **Intestazioni richiesta**. Questo sarà nel modello *di <token>autorizzazione: Bearer *. Copiare e aggiungere questo alla chiamata API come illustrato negli esempi.
> - Passare al sito della documentazione REST di Azure.Navigate to the Azure REST documentation site. premere **Provalo** su qualsiasi API, copiare il token Bearer e aggiungerlo alla chiamata API.
Per eliminare definitivamente l'area di lavoro, usare la chiamata [all'API REST Workspaces - Delete]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) con un tag force:To permanently delete your workspace, use the Workspaces - Delete REST API call with a force tag:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Dove 'eyJ0eXAiOiJKV1Qi...' rappresenta il token di autorizzazione completo.

## <a name="recover-workspace"></a>Recuperare l'area di lavoro

Se si dispone delle autorizzazioni di collaboratore per la sottoscrizione e il gruppo di risorse a cui è stata associata l'area di lavoro prima dell'operazione di eliminazione temporanea, è possibile ripristinarla durante il periodo di eliminazione temporanea, inclusi i dati, la configurazione e gli agenti connessi. Dopo il periodo di eliminazione temporanea, l'area di lavoro non è recuperabile e assegnata per l'eliminazione permanente. I nomi delle aree di lavoro eliminate vengono mantenuti durante il periodo di eliminazione temporanea e non possono essere utilizzati quando si tenta di creare una nuova area di lavoro.  

È possibile ripristinare un'area di lavoro ricreandola utilizzando i metodi di creazione dell'area di lavoro seguenti: PowerShell o [l'API REST,]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) purché le proprietà seguenti siano popolate con i dettagli dell'area di lavoro eliminata:You can recover a workspace by re-creating it using the following workspace create methods: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) or REST API as long as the following properties are populated with the deleted workspace details:

* ID sottoscrizione
* Nome del gruppo di risorse
* Nome dell'area di lavoro
* Region

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

L'area di lavoro e tutti i relativi dati vengono riportati dopo l'operazione di ripristino. Le soluzioni e i servizi collegati sono stati rimossi definitivamente dall'area di lavoro quando è stata eliminata e devono essere riconfigurati per riportare l'area di lavoro allo stato configurato in precedenza. Alcuni dei dati potrebbero non essere disponibili per la query dopo il ripristino dell'area di lavoro finché le soluzioni associate non vengono reinstallate e i relativi schemi non vengono aggiunti all'area di lavoro.

> [!NOTE]
> * Il ripristino dell'area di lavoro non è supportato nel portale di [Azure.](https://portal.azure.com) 
> * La ricreazione di un'area di lavoro durante il periodo di eliminazione temporanea indica che il nome dell'area di lavoro è già in uso. 
> 
