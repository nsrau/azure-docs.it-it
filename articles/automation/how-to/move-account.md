---
title: Spostare l'account di automazione di Azure in un'altra sottoscrizione
description: Questo articolo descrive come spostare l'account di automazione in un'altra sottoscrizione
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8187e4c6f2c7dc721c178bad50b6c3ada2a65367
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717235"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Spostare l'account di automazione di Azure in un'altra sottoscrizione

Azure offre la possibilità di spostare alcune risorse in un nuovo gruppo di risorse o una nuova sottoscrizione. È possibile spostare le risorse tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per altre informazioni sul processo, vedere [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../../azure-resource-manager/resource-group-move-resources.md).

Gli account di automazione di Azure sono una delle risorse che è possibile spostare. Questo articolo illustra i passaggi per spostare gli account di automazione in un'altra risorsa o sottoscrizione.

Di seguito sono riportati i passaggi di alto livello per lo trasferimento dell'account di automazione:

1. Rimuovere le soluzioni.
2. Scollegare l'area di lavoro.
3. Spostare l'account di automazione.
4. Eliminare e ricreare gli account RunAs.
5. Abilitare nuovamente le soluzioni.

## <a name="remove-solutions"></a>Rimuovi soluzioni

Per scollegare l'area di lavoro dall'account di automazione, è necessario rimuovere queste soluzioni dall'area di lavoro:
- **Rilevamento modifiche e inventario**
- **Gestione degli aggiornamenti**
- **Avvia/arresta macchine virtuali durante le ore di indisponibilità**

Nel gruppo di risorse individuare ogni soluzione e selezionare **Elimina**. Nella pagina **Elimina risorse** confermare le risorse da rimuovere e selezionare **Elimina**.

![Elimina le soluzioni dal portale di Azure](../media/move-account/delete-solutions.png)

È possibile eseguire la stessa attività con il cmdlet [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) , come illustrato nell'esempio seguente:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Passaggi aggiuntivi per avviare/arrestare le macchine virtuali

Per la soluzione di **avvio/arresto di macchine virtuali** , è necessario rimuovere anche le regole di avviso create dalla soluzione.

Nel portale di Azure andare al gruppo di risorse e selezionare **monitoraggio** > **avvisi** > **Gestisci regole di avviso**.

![Pagina degli avvisi che mostra la selezione delle regole di gestione degli avvisi](../media/move-account/alert-rules.png)

Nella pagina **regole** verrà visualizzato un elenco degli avvisi configurati in tale gruppo di risorse. La soluzione **Avvia/arresta macchine virtuali** crea tre regole di avviso:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selezionare queste tre regole di avviso e quindi fare clic su **Elimina**. Questa azione rimuoverà queste regole di avviso.

![Pagina regole che richiede la conferma dell'eliminazione per le regole selezionate](../media/move-account/delete-rules.png)

> [!NOTE]
> Se non viene visualizzata alcuna regola di avviso nella pagina **regole** , modificare lo **stato** per visualizzare gli avvisi **disabilitati** , perché potrebbero essere stati disabilitati.

Quando le regole di avviso vengono rimosse, rimuovere il gruppo di azioni creato per le notifiche di **avvio/arresto** della soluzione VM.

Nella portale di Azure selezionare **monitoraggio** > **avvisi** > **Gestisci gruppi di azioni**.

Selezionare **StartStop_VM_Notification** dall'elenco. Nella pagina gruppo di azione selezionare **Elimina**.

![Pagina gruppo di azione, selezionare Elimina](../media/move-account/delete-action-group.png)

Analogamente, è possibile eliminare il gruppo di azioni usando PowerShell con il cmdlet [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) , come illustrato nell'esempio seguente:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Scollega l'area di lavoro

Nella portale di Azure selezionare > **risorse** > correlate all' **account di automazione** **area di lavoro collegata**. Selezionare **Scollega area di lavoro** per scollegare l'area di lavoro dall'account di automazione.

![Scollegare un'area di lavoro da un account di automazione](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Spostare l'account di automazione

Dopo aver rimosso gli elementi precedenti, è possibile continuare a rimuovere l'account di automazione e il relativo manuali operativi. Nella portale di Azure passare al gruppo di risorse dell'account di automazione. Selezionare **Sposta** > **spostamento in un'altra sottoscrizione**.

![Pagina del gruppo di risorse, passare a un'altra sottoscrizione](../media/move-account/move-resources.png)

Selezionare le risorse nel gruppo di risorse che si desidera spostare. Assicurarsi di includere l' **account di automazione**, **Runbook**e le risorse dell' **area di lavoro log Analytics** .

Al termine dello spostamento, sono necessari passaggi aggiuntivi per eseguire tutte le operazioni.

## <a name="re-create-run-as-accounts"></a>Ricrea account RunAs

Gli [account RunAs](../manage-runas-account.md) creano un'entità servizio in Azure Active Directory per l'autenticazione con le risorse di Azure. Quando si modificano le sottoscrizioni, l'account di automazione non utilizza più l'account RunAs esistente.

Passare all'account di automazione nella nuova sottoscrizione e selezionare **account RunAs** in **Impostazioni account**. Si noterà che gli account RunAs vengono visualizzati come incompleti.

![Gli account RunAs sono incompleti](../media/move-account/run-as-accounts.png)

Selezionare ogni account RunAs. Nella pagina **Proprietà** selezionare **Elimina** per eliminare l'account RunAs.

> [!NOTE]
> Se non si dispone delle autorizzazioni per creare o visualizzare gli account RunAs, verrà visualizzato il messaggio seguente: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`Per informazioni sulle autorizzazioni necessarie per configurare un account RunAs, vedere [autorizzazioni necessarie per configurare gli account RunAs](../manage-runas-account.md#permissions).

Una volta eliminati gli account RunAs, selezionare **Crea** in **account RunAs di Azure**. Nella pagina **Aggiungi account RunAs di Azure** selezionare **Crea** per creare l'account RunAs e l'entità servizio. Ripetere i passaggi precedenti con l' **account RunAs classico di Azure**.

## <a name="enable-solutions"></a>Abilitare soluzioni

Dopo aver ricreato gli account RunAs, verranno riabilitate le soluzioni rimosse prima dello spostamento. Per attivare **rilevamento modifiche e l'inventario** e **Gestione aggiornamenti**, selezionare la rispettiva funzionalità nell'account di automazione. Scegliere l'area di lavoro Log Analytics spostata e selezionare **Abilita**.

![Abilitare nuovamente le soluzioni nell'account di automazione spostato](../media/move-account/reenable-solutions.png)

I computer caricati con le soluzioni saranno visibili quando si è connessi all'area di lavoro Log Analytics esistente.

Per attivare la soluzione di **avvio/arresto di macchine virtuali** durante gli orari di indisponibilità, è necessario ridistribuire la soluzione. In **risorse correlate**selezionare **Avvia/arresta macchine virtuali** > **altre informazioni su e abilitare la creazione della soluzione** > per avviare la distribuzione.

Nella pagina **Aggiungi soluzione** scegliere l'area di lavoro log Analytics e l'account di automazione.

![Menu Aggiungi soluzione](../media/move-account/add-solution-vm.png)

Per istruzioni dettagliate sulla configurazione della soluzione, vedere [avviare/arrestare VM durante gli orari di indisponibilità in automazione di Azure](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Verifica post-spostamento

Al termine dello spostamento, controllare l'elenco seguente di attività da verificare:

|Capacità|Test|Collegamento per la risoluzione dei problemi|
|---|---|---|
|Runbook|Un Runbook può essere eseguito correttamente e connettersi alle risorse di Azure.|[Risolvere i problemi dei runbook](../troubleshoot/runbooks.md)
|Controllo del codice sorgente|È possibile eseguire una sincronizzazione manuale sul repository del controllo del codice sorgente.|[Integrazione del controllo del codice sorgente](../source-control-integration.md)|
|Rilevamento modifiche e inventario|Verificare che vengano visualizzati i dati di inventario correnti dai computer.|[Risolvere i problemi del rilevamento delle modifiche](../troubleshoot/change-tracking.md)|
|Gestione degli aggiornamenti|Verificare che i computer siano visualizzati e che siano integri.</br>Eseguire una distribuzione degli aggiornamenti software di test.|[Risolvere i problemi relativi alla gestione degli aggiornamenti](../troubleshoot/update-management.md)|
|Risorse condivise|Verificare che vengano visualizzate tutte le risorse condivise, ad esempio [credenziali](../shared-resources/credentials.md), [variabili](../shared-resources/variables.md)e così via.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo spostamento di risorse in Azure, vedere [spostare le risorse in Azure](../../azure-resource-manager/move-support-resources.md).
