---
title: Spostare l'account di Automazione di Azure in un'altra sottoscrizioneMove your Azure Automation account to another subscription
description: Questo articolo descrive come spostare l'account di Automazione in un'altra sottoscrizione
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969837"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Spostare l'account di Automazione di Azure in un'altra sottoscrizioneMove your Azure Automation account to another subscription

Azure offre la possibilità di spostare alcune risorse in un nuovo gruppo di risorse o sottoscrizione. È possibile spostare le risorse tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per altre informazioni sul processo, vedere [Spostare le risorse in un nuovo gruppo](../../azure-resource-manager/management/move-resource-group-and-subscription.md)di risorse o in una nuova sottoscrizione.

Gli account di Automazione di Azure sono una delle risorse che possono essere spostate. In questo articolo verranno illustrati i passaggi per spostare gli account di automazione in un'altra risorsa o sottoscrizione.

I passaggi generali per spostare l'account Automation sono:

1. Rimuovere le soluzioni.
2. Scollegare l'area di lavoro.
3. Spostare l'account di automazione.
4. Eliminare e ricreare gli account RunAs.
5. Riattivare le soluzioni.

## <a name="remove-solutions"></a>Rimuovere le soluzioni

Per scollegare l'area di lavoro dall'account di automazione, è necessario rimuovere queste soluzioni dall'area di lavoro:
- **Rilevamento modifiche e inventario**
- **Gestione degli aggiornamenti**
- **Avviare/arrestare le macchine virtuali durante le ore non lavorativeStart/Stop VMs during off hours**

Nel gruppo di risorse individuare ogni soluzione e selezionare **Elimina**. Nella pagina **Elimina risorse** confermare le risorse da rimuovere e selezionare **Elimina**.

![Eliminare soluzioni dal portale di AzureDelete solutions from the Azure portal](../media/move-account/delete-solutions.png)

È possibile eseguire la stessa attività con il cmdlet [Remove-AzureRmResource,](/powershell/module/azurerm.resources/remove-azurermresource) come illustrato nell'esempio seguente:You can accomplish the same task with the Remove-AzureRmResource cmdlet as shown in the following example:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Passaggi aggiuntivi per Start/Stop VMs

Per la soluzione **Avvia/Arresta macchine virtuali,** è inoltre necessario rimuovere le regole di avviso create dalla soluzione.

Nel portale di Azure passare al gruppo di risorse e selezionare **Monitoraggio** > **avvisi** > **Gestisci regole di avviso.**

![Pagina Avvisi con la selezione delle regole Gestisci avvisi](../media/move-account/alert-rules.png)

Nella pagina **Regole** dovrebbe essere visualizzato un elenco degli avvisi configurati in tale gruppo di risorse. La soluzione **Avvia/Arresta macchine virtuali** crea tre regole di avviso:The Start/Stop VMs solution creates three alert rules:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selezionare queste tre regole di avviso e quindi **Elimina**. Questa azione rimuoverà queste regole di avviso.

![Pagina Regole che richiede la conferma dell'eliminazione per le regole selezionate](../media/move-account/delete-rules.png)

> [!NOTE]
> Se nella pagina **Regole** non è presente alcuna regola di avviso, modificare **lo stato** in modo da visualizzare gli avvisi **disabilitati,** perché potrebbero essere stati disabilitati.

Quando le regole di avviso vengono rimosse, rimuovere il gruppo di azioni creato per le notifiche della soluzione **Avvia/Arresta macchine virtuali.**

Nel portale di Azure selezionare **Monitora** > **avvisi** > **Gestisci gruppi di azioni.**

Selezionare **StartStop_VM_Notification** dall'elenco. Nella pagina del gruppo di azioni selezionare **Elimina**.

![Gruppo di azioni, selezionare Elimina](../media/move-account/delete-action-group.png)

Analogamente, è possibile eliminare il gruppo di azioni usando PowerShell con il cmdlet [Remove-AzureRmActionGroup,](/powershell/module/azurerm.insights/remove-azurermactiongroup) come illustrato nell'esempio seguente:Similarly, you can delete your action group by using PowerShell with the Remove-AzureRmActionGroup cmdlet, as seen in the following example:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Scollegare l'area di lavoro

Nel portale di Azure selezionare Area di > **lavoro collegata**Risorse correlate **all'account** > **Related Resources**di automazione. Selezionare **Scollega area di lavoro** per scollegare l'area di lavoro dall'account di Automazione.

![Scollegare un'area di lavoro da un account di Automazione](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Spostare l'account di automazione

Dopo aver rimosso gli elementi precedenti, puoi continuare a rimuovere il tuo account Automation e i relativi runbook. Nel portale di Azure passare al gruppo di risorse dell'account di automazione. Selezionare **Sposta** > **spostamento in un'altra sottoscrizione**.

![Pagina Gruppo di risorse, passare a un'altra sottoscrizioneResource group page, move to another subscription](../media/move-account/move-resources.png)

Selezionare le risorse nel gruppo di risorse che si desidera spostare. Assicurarsi di includere **l'account di Automazione dati,** le risorse dell'area di **lavoro Runbook**e **Log Analytics.**

Una volta completato lo spostamento, sono necessari ulteriori passaggi per far funzionare tutto.

## <a name="re-create-run-as-accounts"></a>Ricreare gli account RunAs

[Gli account RunAs](../manage-runas-account.md) creano un'entità servizio in Azure Active Directory per l'autenticazione con le risorse di Azure.Run As accounts create a service principal in Azure Active Directory to authenticate with Azure resources. Quando si modificano le sottoscrizioni, l'account di automazione non utilizza più l'account RunAs esistente.

Passare all'account di Automazione nella nuova sottoscrizione e selezionare **Esegui come account** in Impostazioni **account**. Vedrai che gli account RunAs vengono visualizzati come incompleti ora.

![Gli account RunAs sono incompleti](../media/move-account/run-as-accounts.png)

Selezionare ogni account RunAs. Nella pagina **Proprietà** selezionare **Elimina** per eliminare l'account RunAs.

> [!NOTE]
> Se non si dispone delle autorizzazioni necessarie per creare o visualizzare gli `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` account RunAs, verrà visualizzato il seguente messaggio: Per informazioni sulle autorizzazioni necessarie per configurare un account RunAs, vedere [Autorizzazioni necessarie per configurare gli account RunAs](../manage-runas-account.md#permissions).

Dopo l'eliminazione degli account RunAs, selezionare **Crea** in **Account Di Azure RunAs**. Nella pagina **Aggiungi account RunAs** di Azure selezionare **Crea** per creare l'account RunAs e l'entità servizio. Ripetere i passaggi precedenti con **l'account RunAs classico**di Azure.

## <a name="enable-solutions"></a>Abilitare soluzioni

Dopo aver ricreato gli account RunAs, sarà necessario riattivare le soluzioni rimosse prima dello spostamento. Per attivare **il rilevamento delle modifiche e** la gestione dell'inventario e **degli aggiornamenti,** selezionare la rispettiva funzionalità nell'account di automazione. Scegliere l'area di lavoro di Log Analytics su cui si è spostati e selezionare **Abilita**.

![Riattivare le soluzioni nell'account Di automazione spostato](../media/move-account/reenable-solutions.png)

I computer dotati di onboarding con le soluzioni saranno visibili dopo aver connesso l'area di lavoro di Log Analytics esistente.

Per attivare la soluzione **Start/Stop VMs** durante le ore non orari, è necessario ridistribuire la soluzione. In **Risorse correlate**selezionare **Avvia/Arresta macchine virtuali** > **Ulteriori informazioni e abilitare la soluzione** > **Crea** per avviare la distribuzione.

Nella pagina **Aggiungi soluzione** scegliere l'account Log Analytics Workspace e Automation.

![Menu Aggiungi soluzione](../media/move-account/add-solution-vm.png)

Per istruzioni dettagliate sulla configurazione della soluzione, vedere Avviare/arrestare le macchine virtuali durante la soluzione fuori orario in Automazione di Azure.For detailed instructions on configuring the solution, see [Start/Stop VMs during off-hours solution in Azure Automation.](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>Verifica post-spostamento

Al termine dello spostamento, controllare il seguente elenco di attività che devono essere verificate:

|Funzionalità|Test|Collegamento per la risoluzione dei problemi|
|---|---|---|
|Runbook|Un runbook può essere eseguito e connesso correttamente alle risorse di Azure.A runbook can successfully run and connect to Azure resources.|[Risolvere i problemi relativi ai runbook](../troubleshoot/runbooks.md)
|Controllo del codice sorgente|È possibile eseguire una sincronizzazione manuale nel repository del controllo del codice sorgente.|[Integrazione del controllo del codice sorgente](../source-control-integration.md)|
|Rilevamento delle modifiche e inventario|Verificare di vedere i dati di inventario correnti dalle macchine.|[Risolvere i problemi relativi al rilevamento delle modificheTroubleshoot change](../troubleshoot/change-tracking.md)|
|Gestione degli aggiornamenti|Verificare che siano stati visualizzati i computer e che siano integri.</br>Eseguire una distribuzione di aggiornamento software di test.|[Risolvere i problemi relativi alla gestione degli aggiornamentiTroubleshoot update management](../troubleshoot/update-management.md)|
|Risorse condivise|Verificare che siano visualizzate tutte le risorse condivise, ad esempio [Credenziali,](../shared-resources/credentials.md) [Variabili](../shared-resources/variables.md)e così via.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo spostamento di risorse in Azure, vedere [Spostare le risorse in Azure.To](../../azure-resource-manager/management/move-support-resources.md)learn more about moving resources in Azure, see Move resources in Azure.
