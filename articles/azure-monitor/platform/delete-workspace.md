---
title: Eliminare e recuperare un'area di lavoro Azure Log Analytics | Microsoft Docs
description: Informazioni su come eliminare l'area di lavoro Log Analytics, se ne è stata creata una in una sottoscrizione personale, o come ristrutturare il modello dell'area di lavoro.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 0858d448cf768dbe6ea48f07247725fac30da860
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95758905"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Eliminare e ripristinare l'area di lavoro di Azure Log Analytics

Questo articolo illustra il concetto di eliminazione temporanea dell'area di lavoro di Azure Log Analytics e come ripristinare l'area di lavoro eliminata.

## <a name="considerations-when-deleting-a-workspace"></a>Considerazioni in caso di eliminazione dell'area di lavoro

Quando si elimina un'area di lavoro di Log Analytics, viene eseguita un'operazione di eliminazione temporanea per consentire il ripristino dell'area di lavoro, inclusi i dati e gli agenti connessi entro 14 giorni, indipendentemente dal fatto che l'eliminazione sia stata accidentale o intenzionale. Dopo il periodo di eliminazione temporanea, la risorsa dell'area di lavoro e i relativi dati non sono recuperabili. I dati vengono accodati per l'eliminazione permanente e completamente eliminati entro 30 giorni. Il nome dell'area di lavoro viene "rilasciato" ed è possibile usarlo per creare una nuova area di lavoro.

> [!NOTE]
> Se si vuole eseguire l'override del comportamento di eliminazione temporanea ed eliminare definitivamente l'area di lavoro, seguire la procedura descritta in [eliminazione permanente dell'area di lavoro](#permanent-workspace-delete).

Prestare particolare attenzione quando si elimina un'area di lavoro perché potrebbero essere presenti dati e configurazioni importanti che possono influire negativamente sulle operazioni del servizio. Esaminare gli agenti, le soluzioni e gli altri servizi di Azure che archiviano i dati in Log Analytics, ad esempio:

* Soluzioni di gestione
* Automazione di Azure
* Agenti in esecuzione in macchine virtuali Windows e Linux
* Agenti in esecuzione in computer Windows e Linux nell'ambiente
* System Center Operations Manager

L'operazione di eliminazione temporanea elimina la risorsa dell'area di lavoro e le autorizzazioni degli utenti associati vengono interrotte. Se gli utenti sono associati ad altre aree di lavoro, potranno continuare a usare Log Analytics con tali aree di lavoro.

## <a name="soft-delete-behavior"></a>Comportamento della funzione di eliminazione temporanea

L'operazione di eliminazione dell'area di lavoro consente di rimuovere la risorsa di Resource Manager dell'area di lavoro, ma la configurazione e i dati vengono conservati per 14 giorni, assicurando allo stesso tempo l'eliminazione dell'area di lavoro. Tutti gli agenti e i gruppi di gestione di System Center Operations Manager configurati per l'invio di informazioni all'area di lavoro rimangono in uno stato orfano durante il periodo di eliminazione temporanea. Il servizio fornisce inoltre un meccanismo per il ripristino dell'area di lavoro eliminata, inclusi i dati e le risorse connesse, annullando essenzialmente l'eliminazione.

> [!NOTE] 
> Le soluzioni installate e i servizi collegati, ad esempio l'account di Automazione di Azure, vengono rimossi definitivamente dall'area di lavoro al momento dell'eliminazione e non possono essere recuperati. Queste devono essere riconfigurate dopo l'operazione di ripristino per portare l'area di lavoro allo stato configurato in precedenza.

È possibile eliminare un'area di lavoro usando [PowerShell](/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0&preserve-view=true), l'[API REST](/rest/api/loganalytics/workspaces/delete) o nel [portale di Azure](https://portal.azure.com).

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.
3. Nell'elenco delle aree di lavoro di Log Analytics selezionare un'area di lavoro e quindi fare clic su **Elimina** nella parte superiore del riquadro centrale.
4. Viene visualizzata una pagina di conferma che mostra l'inserimento dei dati nell'area di lavoro nell'ultima settimana. 
5. Se si desidera eliminare definitivamente l'area di lavoro rimuovendo l'opzione per ripristinarla in un secondo momento, selezionare la casella di controllo **Elimina definitivamente l'area di lavoro** .
6. Digitare il nome dell'area di lavoro da confermare, quindi fare clic su **Elimina**.

   ![Confermare l'eliminazione dell'area di lavoro](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Eliminazione permanente dell'area di lavoro
Il metodo di eliminazione temporanea potrebbe non rientrare in alcuni scenari, ad esempio sviluppo e test, in cui è necessario ripetere una distribuzione con le stesse impostazioni e il nome dell'area di lavoro. In questi casi è possibile eliminare definitivamente l'area di lavoro ed "eseguire l'override" del periodo di eliminazione temporanea. L'operazione di eliminazione permanente dell'area di lavoro rilascia il nome dell'area di lavoro ed è possibile creare una nuova area di lavoro con lo stesso nome.

> [!IMPORTANT]
> Usare l'operazione di eliminazione permanente dell'area di lavoro con cautela poiché è irreversibile e non sarà possibile ripristinare l'area di lavoro e i relativi dati.

Per eliminare definitivamente un'area di lavoro usando il portale di Azure, selezionare la casella di controllo **Elimina definitivamente l'area di lavoro** prima di fare clic sul pulsante **Elimina** .

Per eliminare definitivamente un'area di lavoro usando PowerShell, aggiungere il tag '-ForceDelete ' per eliminare definitivamente l'area di lavoro. L'opzione '-ForceDelete ' è attualmente disponibile con AZ. OperationalInsights 2.3.0 o versione successiva. 

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -ForceDelete
```

## <a name="recover-workspace"></a>Recuperare l'area di lavoro
Quando si elimina un'area di lavoro di Log Analytics accidentalmente o intenzionalmente, il servizio posiziona l'area di lavoro in uno stato di eliminazione temporanea rendendola inaccessibile a qualsiasi operazione. Il nome dell'area di lavoro eliminata viene mantenuto durante il periodo di eliminazione temporanea e non può essere usato per la creazione di una nuova area di lavoro. Dopo il periodo di eliminazione temporanea, l'area di lavoro non è recuperabile, viene pianificata per l'eliminazione permanente e il nome rilasciato e può essere usato per creare una nuova area di lavoro.

È possibile ripristinare l'area di lavoro durante il periodo di eliminazione temporanea, inclusi i dati, la configurazione e gli agenti connessi. Prima di eseguire l'operazione di eliminazione temporanea sono necessarie le autorizzazioni di Collaboratore per la sottoscrizione e il gruppo di risorse in cui l'area di lavoro è stata posizionata. Il ripristino dell'area di lavoro viene eseguito creando un'area di lavoro di Log Analytics con i dettagli dell'area di lavoro eliminata, tra cui:

- ID sottoscrizione
- Nome del gruppo di risorse
- Nome dell'area di lavoro
- Region

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**. Viene visualizzato l'elenco delle aree di lavoro presenti nell'ambito selezionato.
3. Fare clic su **Ripristina** nel menu in alto a sinistra per aprire una pagina con le aree di lavoro in stato di eliminazione temporanea che è possibile ripristinare.

   ![Screenshot della schermata aree di lavoro Log Analytics in portale di Azure con ripristino evidenziato nella barra dei menu.](media/delete-workspace/recover-menu.png)

4. Selezionare l'area di lavoro e fare clic su **Ripristina** per ripristinare l'area di lavoro.

   ![Screenshot della finestra di dialogo Ripristina aree di lavoro eliminate Log Analytics in portale di Azure con un'area di lavoro evidenziata e il pulsante Ripristina selezionato.](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

L'area di lavoro e tutti i relativi dati vengono ripristinati dopo l'operazione di ripristino. Le soluzioni e i servizi collegati erano stati rimossi definitivamente dall'area di lavoro al momento dell'eliminazione ed è necessario riconfigurarli per portare l'area di lavoro allo stato configurato in precedenza. Alcuni dati potrebbero non essere disponibili per la query dopo il ripristino dell'area di lavoro fino a quando le soluzioni associate non vengono reinstallate e i relativi schemi vengono aggiunti all'area di lavoro.

> [!NOTE]
> * Se viene ricreata un'area di lavoro durante il periodo di eliminazione temporanea viene indicato che il nome dell'area di lavoro è già in uso. 
 
## <a name="troubleshooting"></a>Risoluzione dei problemi

Per eliminare un'area di lavoro è necessario disporre di autorizzazioni come *Collaboratore di Log Analytics*.

* Se non si è certi che l'area di lavoro eliminata sia in stato di eliminazione temporanea e possa essere ripristinata, fare clic su [Ripristina](#recover-workspace) nella pagina *aree di lavoro log Analytics* per visualizzare un elenco delle aree di lavoro eliminate temporaneamente per ogni sottoscrizione. Le aree di lavoro eliminate in modo permanente non sono incluse nell'elenco.
* Se si riceve un messaggio di errore "*Questo nome di dominio è già in uso*" o *"Conflitto"* durante la creazione di un'area di lavoro, la causa potrebbe essere:
  * Il nome dell'area di lavoro non è disponibile e viene usato da un utente dell'organizzazione o da un altro cliente.
  * L'area di lavoro è stata eliminata negli ultimi 14 giorni e il nome è mantenuto riservato per il periodo di eliminazione temporanea. Per eseguire l'override dell'eliminazione temporanea ed eliminare definitivamente l'area di lavoro per creare una nuova area di lavoro con lo stesso nome, attenersi alla procedura seguente per ripristinare prima l'area di lavoro ed eseguire l'eliminazione permanente:<br>
    1. [Recuperare](#recover-workspace) l'area di lavoro.
    2. [Eliminare definitivamente](#permanent-workspace-delete) l'area di lavoro.
    3. Creare una nuova area di lavoro usando il nome della stessa area di lavoro.
* Se viene visualizzato un codice di risposta 204 che indica che la *risorsa non è stata trovata*, è possibile che si verifichi un tentativo consecutivo di utilizzare l'operazione Elimina area di lavoro. 204 è una risposta vuota, che in genere indica che la risorsa non esiste, quindi l'eliminazione è stata completata senza eseguire alcuna operazione.
  Al termine della chiamata di eliminazione nel back-end, è possibile ripristinare l'area di lavoro e completare l'operazione di eliminazione permanente in uno dei metodi suggeriti in precedenza.

