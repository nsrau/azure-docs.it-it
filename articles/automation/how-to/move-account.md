---
title: Spostare l'account di Automazione di Azure in un'altra sottoscrizioneMove your Azure Automation account to another subscription
description: In questo articolo viene descritto come spostare l'account di Automazione in un'altra sottoscrizione.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681882"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Spostare l'account di Automazione di Azure in un'altra sottoscrizioneMove your Azure Automation account to another subscription

Automazione di Azure consente di spostare alcune risorse in un nuovo gruppo di risorse o sottoscrizione. È possibile spostare le risorse tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per altre informazioni sul processo, vedere [Spostare le risorse in un nuovo gruppo](../../azure-resource-manager/management/move-resource-group-and-subscription.md)di risorse o in una nuova sottoscrizione.

L'account di Automazione di Azure è una delle risorse che è possibile spostare. In questo articolo imparerai a spostare gli account di automazione in un'altra risorsa o sottoscrizione. I passaggi generali per lo spostamento dell'account Automation sono:

1. Rimuovere le soluzioni.
2. Scollegare l'area di lavoro.
3. Spostare l'account di automazione.
4. Eliminare e ricreare gli account RunAs.
5. Riattivare le soluzioni.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="remove-solutions"></a>Rimuovere le soluzioni

Per scollegare l'area di lavoro dall'account di automazione, è necessario rimuovere queste soluzioni dall'area di lavoro:

- Rilevamento modifiche e inventario
- Gestione degli aggiornamenti
- Avviare/arrestare le VM fuori orario

1. Individuare il gruppo di risorse nel portale di Azure.
2. Trovare ogni soluzione e fare clic su **Elimina** nella pagina Elimina risorse.

    ![Eliminare soluzioni dal portale di AzureDelete solutions from the Azure portal](../media/move-account/delete-solutions.png)

    Se si preferisce, è possibile eliminare le soluzioni utilizzando il cmdlet [Remove-AzResource:](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0)

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Rimuovere le regole di avviso per la soluzione Start/Stop VMs durante la soluzione off hoursRemove alert rules for the Start/Stop VMs during off hours solution

Per la soluzione Avvia/Arresta macchine virtuali durante le ore non lavorative, è inoltre necessario rimuovere le regole di avviso create dalla soluzione.

1. Nel portale di Azure passare al gruppo di risorse e selezionare **Monitoraggio** > **avvisi** > **Gestisci regole di avviso.**

![Pagina Avvisi con la selezione delle regole Gestisci avvisi](../media/move-account/alert-rules.png)

2. Nella pagina Regole dovrebbe essere visualizzato un elenco degli avvisi configurati in tale gruppo di risorse. La soluzione crea queste regole:The solution creates these rules:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selezionare le regole una alla volta e fare clic su **Elimina** per rimuoverle.

    ![Pagina Regole che richiede la conferma dell'eliminazione per le regole selezionate](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Se nella pagina Regole non è presente alcuna regola di avviso, modificare il campo **Stato** in Disabilitato per visualizzare gli avvisi disabilitati, perché potrebbero essere stati disabilitati.

4. Quando le regole di avviso vengono rimosse, è necessario rimuovere il gruppo di azioni creato per avviare/arrestare le macchine virtuali durante le notifiche della soluzione non lavorative. Nel portale di Azure selezionare **Monitora** > **avvisi** > **Gestisci gruppi di azioni.**

5. Selezionare **StartStop_VM_Notification**. 

6. Nella pagina del gruppo di azioni selezionare **Elimina**.

    ![Pagina Gruppo di azioni](../media/move-account/delete-action-group.png)

    Se si preferisce, è possibile eliminare il gruppo di azioni utilizzando il cmdlet [Remove-AzActionGroup:](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0)

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Scollegare l'area di lavoro

Ora è possibile scollegare l'area di lavoro:

1. Nel portale di Azure selezionare Area di > **lavoro collegata**Risorse correlate **all'account** > **Related Resources**di automazione. 

2. Selezionare **Scollega area di lavoro** per scollegare l'area di lavoro dall'account di Automazione.

    ![Scollegare un'area di lavoro da un account di Automazione](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Spostare l'account di automazione

Ora puoi spostare il tuo account Automation e i suoi runbook. 

1. Nel portale di Azure passare al gruppo di risorse dell'account di automazione. Selezionare **Sposta** > **spostamento in un'altra sottoscrizione**.

    ![Pagina Gruppo di risorse, passare a un'altra sottoscrizioneResource group page, move to another subscription](../media/move-account/move-resources.png)

2. Selezionare le risorse nel gruppo di risorse che si desidera spostare. Assicurati di includere il tuo account di Automazione automazione, i runbook e le risorse dell'area di lavoro di Log Analytics.

## <a name="recreate-run-as-accounts"></a>Ricreare gli account RunAs

[Gli account RunAs](../manage-runas-account.md) creano un'entità servizio in Azure Active Directory per l'autenticazione con le risorse di Azure.Run As accounts create a service principal in Azure Active Directory to authenticate with Azure resources. Quando si modificano le sottoscrizioni, l'account di automazione non utilizza più l'account RunAs esistente. Per ricreare gli account RunAs:

1. Passare all'account di Automazione nella nuova sottoscrizione e selezionare **Esegui come account** in Impostazioni **account**. Vedrai che gli account RunAs vengono visualizzati come incompleti ora.

    ![Gli account RunAs sono incompleti](../media/move-account/run-as-accounts.png)

2. Eliminare gli account RunAs uno alla volta utilizzando il pulsante **Elimina** nella pagina Proprietà. 

    > [!NOTE]
    > Se non si dispone delle autorizzazioni necessarie per creare o visualizzare `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` gli account RunAs, viene visualizzato il seguente messaggio: Per informazioni sulle autorizzazioni necessarie per configurare un account RunAs, vedere [Autorizzazioni necessarie per configurare gli account RunAs](../manage-runas-account.md#permissions).

3. Dopo aver eliminato gli account RunAs, selezionare **Crea** in **Account RunAs**di Azure. 

4. Nella pagina Aggiungi account RunAs di Azure selezionare **Crea** per creare l'account RunAs e l'entità servizio. 

5. Ripetere i passaggi precedenti con l'account RunAs classico di Azure.Repeat the steps above with the Azure Classic RunAs account.

## <a name="enable-solutions"></a>Abilitare soluzioni

Dopo aver ricreato gli account RunAs, è necessario riattivare le soluzioni rimosse prima dello spostamento: 

1. Per attivare la soluzione Rilevamento modifiche e inventario, seleziona Rilevamento modifiche e inventario nel tuo account Automation. Scegliere l'area di lavoro di Log Analytics su cui è stato spostato e selezionare **Abilita**.

2. Ripetere il passaggio 1 per la soluzione di gestione degli aggiornamenti.

    ![Riattivare le soluzioni nell'account Di automazione spostato](../media/move-account/reenable-solutions.png)

3. I computer dotati di connessione con le soluzioni sono visibili dopo aver connesso l'area di lavoro di Log Analytics esistente. Per attivare la soluzione Start/Stop VMs durante le ore non lavorative, è necessario ridistribuire la soluzione. In **Risorse correlate**selezionare **Avvia/Arresta macchine virtuali** > **Ulteriori informazioni e abilitare la soluzione** > **Crea** per avviare la distribuzione.

4. Nella pagina Aggiungi soluzione scegliere l'area di lavoro di Log Analytics e l'account di automazione.

    ![Menu Aggiungi soluzione](../media/move-account/add-solution-vm.png)

5. Configurare la soluzione come descritto in [Avviare/arrestare le macchine virtuali durante la soluzione fuori orario in Automazione di Azure.](../automation-solution-vm-management.md)

## <a name="verify-the-move"></a>Verificare lo spostamento

Al termine dello spostamento, verificare che le funzionalità elencate di seguito siano abilitate. 

|Funzionalità|Test|Risoluzione dei problemi|
|---|---|---|
|Runbook|Un runbook può essere eseguito e connesso correttamente alle risorse di Azure.A runbook can successfully run and connect to Azure resources.|[Risolvere i problemi dei runbook](../troubleshoot/runbooks.md)
|Controllo del codice sorgente|È possibile eseguire una sincronizzazione manuale nel repository del controllo del codice sorgente.|[Integrazione del controllo del codice sorgente](../source-control-integration.md)|
|Rilevamento delle modifiche e inventario|Verificare che siano visualizzati i dati di inventario correnti dalle macchine.|[Risolvere i problemi relativi al rilevamento delle modificheTroubleshoot change](../troubleshoot/change-tracking.md)|
|Gestione degli aggiornamenti|Verificare che i computer siano visualizzati e che siano integri.</br>Eseguire una distribuzione di aggiornamento software di test.|[Risolvere i problemi relativi alla gestione degli aggiornamentiTroubleshoot update management](../troubleshoot/update-management.md)|
|Risorse condivise|Verificare che siano visualizzate tutte le risorse condivise, ad esempio [credenziali,](../shared-resources/credentials.md) [variabili](../shared-resources/variables.md)e così via.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo spostamento di risorse in Azure, vedere [Spostare le risorse in Azure.To](../../azure-resource-manager/management/move-support-resources.md)learn more about moving resources in Azure, see Move resources in Azure.
