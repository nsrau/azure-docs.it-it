---
title: Spostare l'account di automazione di Azure a un'altra sottoscrizione
description: Questo articolo descrive come spostare l'account di automazione in un'altra sottoscrizione
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225961"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Spostare l'account di automazione di Azure a un'altra sottoscrizione

Azure offre la possibilità di spostare alcune risorse in un nuovo gruppo di risorse o sottoscrizione. È possibile spostare le risorse tramite il portale di Azure, PowerShell, CLI Azure o l'API REST. Per altre informazioni sul processo, vedere [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../../azure-resource-manager/resource-group-move-resources.md). 

Account di automazione di Azure sono una delle risorse che possono essere spostate. In questo articolo si apprenderà i passaggi per spostare gli account di automazione in un'altra risorsa o una sottoscrizione.

I passaggi generali per lo spostamento di account di automazione sono:

1. Rimuovere le soluzioni.
2. Scollegare l'area di lavoro.
3. Spostare l'account di automazione.
4. Eliminare e ricreare gli account RunAs.
5. Abilitare nuovamente le soluzioni.

## <a name="remove-solutions"></a>Rimuovere le soluzioni

Per scollegare l'area di lavoro dall'account di automazione, queste soluzioni devono essere rimossi dall'area di lavoro:
- **Rilevamento delle modifiche e inventario**
- **Gestione degli aggiornamenti** 
- **Avviare/arrestare VM durante gli orari di minore** 

Nel gruppo di risorse, individuare ogni soluzione e selezionare **Elimina**. Nel **eliminare risorse** pagina, verificare che le risorse in modo da rimuovere, quindi scegliere **eliminare**.

![Eliminare le soluzioni nel portale di Azure](../media/move-account/delete-solutions.png)

È possibile eseguire la stessa attività con il [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) cmdlet, come illustrato nell'esempio seguente:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Passaggi aggiuntivi per avviare/arrestare VM

Per il **avviare/arrestare VM** soluzione, è necessario rimuovere anche le regole di avviso create dalla soluzione.

Nel portale di Azure, passare al gruppo di risorse e selezionare **Monitoring** > **avvisi** > **Gestisci regole di avviso**.

![Selezione di visualizzazione pagina delle regole di avviso gestire avvisi](../media/move-account/alert-rules.png)

Nel **regole** pagina, verrà visualizzato un elenco degli avvisi configurati in tale gruppo di risorse. Il **avviare/arrestare VM** soluzione crea tre regole di avviso:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selezionare queste tre regole di avviso e quindi selezionare **Elimina**. Questa azione rimuoverà queste regole di avviso.

![Pagina delle regole di richiesta di conferma dell'eliminazione per le regole selezionate](../media/move-account/delete-rules.png)

> [!NOTE]
> Se non viene visualizzata alcuna regola di avviso nel **regole** pagina, modificare il **stato** per mostrare **disabilitato** avvisi, in quanto è possibile che abbia disabilitato li.

Quando vengono rimosse le regole di avviso, rimuovere il gruppo di azioni che è stato creato per il **avviare/arrestare VM** le notifiche di soluzione.

Nel portale di Azure, selezionare **Monitor** > **avvisi** > **Gestisci gruppi di azioni**.

Selezionare **StartStop_VM_Notification** dall'elenco. Nella pagina del gruppo di azione, selezionare **Elimina**.

![Pagina gruppo di azione selezionare Elimina](../media/move-account/delete-action-group.png)

Analogamente, è possibile eliminare il gruppo di azioni usando PowerShell con il [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet, come illustrato nell'esempio seguente:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Scollegare l'area di lavoro

Nel portale di Azure, selezionare **account di automazione** > **le risorse correlate** > **dell'area di lavoro collegato**. Selezionare **Scollega area di lavoro** scollegare l'area di lavoro dall'account di automazione.

![Scollega un'area di lavoro da un account di automazione](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Spostare l'account di automazione

Dopo la rimozione degli elementi precedenti, è possibile continuare a rimuovere l'account di automazione e i runbook. Nel portale di Azure, passare al gruppo di risorse dell'account di automazione. Selezionare **spostare** > **spostare in un'altra sottoscrizione**.

![Pagina gruppo di risorse, move in un'altra sottoscrizione](../media/move-account/move-resources.png)

Selezionare le risorse nel gruppo di risorse che si desidera spostare. Assicurarsi includere il **account di automazione**, **Runbook**, e **area di lavoro di Log Analitica** le risorse.

Dopo aver completato lo spostamento, sono necessari passaggi aggiuntivi necessari per rendere tutti gli elementi di lavoro.

## <a name="re-create-run-as-accounts"></a>Ricreare gli account RunAs

[Gli account RunAs](../manage-runas-account.md) creare un'entità servizio in Azure Active Directory per l'autenticazione con le risorse di Azure. Quando si modificano le sottoscrizioni, account di automazione non usa più l'account RunAs esistente.

Passare all'account di automazione nella nuova sottoscrizione e selezionare **degli account RunAs** sotto **impostazioni Account**. Si noterà che gli account RunAs mostrano ora incompleti.

![Gli account RunAs sono incompleti](../media/move-account/run-as-accounts.png)

Selezionare ogni account RunAs. Nel **delle proprietà** pagina, selezionare **eliminare** per eliminare l'account RunAs.

> [!NOTE]
> Se non hai le autorizzazioni per creare o visualizzare gli account RunAs, si verrà visualizzato il messaggio seguente: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Per altre informazioni sulle autorizzazioni necessarie per configurare un account RunAs, vedere [autorizzazioni necessarie per configurare gli account RunAs](../manage-runas-account.md#permissions).

Dopo l'eliminazione di account RunAs, selezionare **Create** sotto **account RunAs di Azure**. Nel **Aggiungi account RunAs** pagina, selezionare **crea** per creare l'account RunAs e un servizio dell'entità. Ripetere i passaggi precedenti con il **Azure account RunAs classico**.

## <a name="enable-solutions"></a>Abilitare soluzioni

Dopo aver ricreato l'account RunAs, si saranno riabilitare le soluzioni che rimosse prima dello spostamento. Per attivare **rilevamento modifiche e inventario** e **gestione degli aggiornamenti**, selezionare la rispettiva funzionalità nell'account di automazione. Scegliere l'area di lavoro di Log Analitica è spostato su e selezionare **abilitare**.

![Abilitare nuovamente le soluzioni nell'account di automazione spostata](../media/move-account/reenable-solutions.png)

Le macchine che siano state caricate con le soluzioni saranno visibili quando un utente si connette l'area di lavoro di Log Analitica esistente.

Per attivare i **avviare/arrestare VM** durante orari di minore attività, è necessario ridistribuire la soluzione. Sotto **le risorse correlate**, selezionare **Avvia/Arresta macchine virtuali** > **maggiori informazioni e abilitare la soluzione** > **crea** per avviare la distribuzione.

Nel **Aggiungi soluzione** pagina, scegliere l'account di automazione e dell'area di lavoro di Log Analitica.  

![Aggiungere menu soluzioni](../media/move-account/add-solution-vm.png)

Per istruzioni dettagliate sulla configurazione della soluzione, vedere [avviare/arrestare VM durante la soluzione di orari di minore attività in automazione di Azure](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Verifica post-move

Una volta completato lo spostamento, controllare il seguente elenco di attività che devono essere verificati:

|Funzionalità|Test|Risoluzione dei problemi di collegamento|
|---|---|---|
|Runbook|Un runbook è stato possibile eseguire e connettersi alle risorse di Azure.|[Risolvere i problemi dei runbook](../troubleshoot/runbooks.md)
| Controllo del codice sorgente|È possibile eseguire una sincronizzazione manuale nel repository di controllo di origine.|[Integrazione del controllo del codice sorgente](../source-control-integration.md)|
|Rilevamento delle modifiche e inventario|Verificare che sia visualizzato corrente dei dati di inventario dai computer.|[Risolvere i problemi di rilevamento delle modifiche](../troubleshoot/change-tracking.md)|
|Gestione degli aggiornamenti|Verificare noterete che le macchine e risultano integri.</br>Eseguire una distribuzione di aggiornamenti software di test.|[Risolvere i problemi di gestione degli aggiornamenti](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo spostamento delle risorse in Azure, vedere [spostare le risorse in Azure](../../azure-resource-manager/move-support-resources.md).
