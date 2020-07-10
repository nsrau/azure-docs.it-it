---
title: Spostare l'account di Automazione di Azure in un'altra sottoscrizione
description: Questo articolo descrive come spostare l'account di Automazione in un'altra sottoscrizione.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 562ea5e0e9e4851ed59bd3ef917be2f9c48cd2a7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185552"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Spostare l'account di Automazione di Azure in un'altra sottoscrizione

Automazione di Azure consente di spostare alcune risorse in un nuovo gruppo di risorse o una nuova sottoscrizione. È possibile spostare le risorse tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per altre informazioni sul processo, vedere [Spostare le risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

L'account di Automazione è una delle risorse che è possibile spostare. In questo articolo si apprenderà come spostare gli account di Automazione in un'altra risorsa o sottoscrizione. I passaggi generali per spostare l'account di Automazione sono i seguenti:

1. Disabilitare le funzionalità.
2. Scollegare l'area di lavoro.
3. Spostare l'account di Automazione.
4. Eliminare e ricreare gli account RunAs.
5. Abilitare di nuovo le funzionalità.

## <a name="remove-features"></a>Rimuovere le funzionalità

Per scollegare l'area di lavoro dall'account di Automazione, è necessario rimuovere le risorse delle funzionalità nell'area di lavoro:

- Rilevamento modifiche e inventario
- Gestione degli aggiornamenti
- Avviare/arrestare VM durante gli orari di minore attività

1. Individuare il gruppo di risorse nel portale di Azure.
2. Trovare ogni funzionalità e selezionare **Elimina** nella pagina **Elimina risorse**.

    ![Screenshot dell'eliminazione delle risorse delle funzionalità dal portale di Azure](../media/move-account/delete-solutions.png)

Se si preferisce, è possibile eliminare le risorse usando il cmdlet [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0):

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Rimuovere le regole di avviso per Avvio/Arresto di macchine virtuali durante gli orari di minore attività

Per Avvio/Arresto di macchine virtuali durante gli orari di minore attività, è necessario rimuovere anche le regole di avviso create dalla funzionalità.

1. Nel portale di Azure passare al gruppo di risorse e selezionare **Monitoraggio** > **Avvisi** > **Gestisci regole di avviso**.

   ![Screenshot della pagina Avvisi, che mostra la selezione di Gestisci regole di avviso](../media/move-account/alert-rules.png)

2. Nella pagina Regole verrà visualizzato un elenco degli avvisi configurati in tale gruppo di risorse. La funzionalità crea queste regole:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selezionare le regole una alla volta e selezionare **Elimina** per rimuoverle.

    ![Screenshot della pagina delle regole con richiesta di conferma dell'eliminazione per le regole selezionate](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Se non viene visualizzata alcuna regola di avviso nella pagina Regole, modificare il campo **Stato** in **Disabilitato** per visualizzare gli avvisi disabilitati. 

4. Quando si rimuovono le regole di avviso, è necessario rimuovere il gruppo di azioni creato per le notifiche Avvio/Arresto di macchine virtuali durante gli orari di minore attività. Nel portale di Azure selezionare **Monitoraggio** > **Avvisi** > **Gestisci gruppi di azioni**.

5. Selezionare **StartStop_VM_Notification**. 

6. Nella pagina Gruppo di azioni selezionare **Elimina**.

    ![Screenshot della pagina Gruppo di azioni](../media/move-account/delete-action-group.png)

Se si preferisce, è possibile eliminare il gruppo di azioni usando il cmdlet [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0):

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Scollegare l'area di lavoro

A questo punto è possibile scollegare l'area di lavoro:

1. Nel portale di Azure selezionare **Account di Automazione** > **Risorse correlate** > **Area di lavoro collegata**. 

2. Selezionare **Scollega area di lavoro** per scollegare l'area di lavoro dall'account di Automazione.

    ![Screenshot dello scollegamento di un'area di lavoro da un account di Automazione](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Spostare l'account di Automazione

È ora possibile spostare l'account di Automazione e i relativi runbook. 

1. Nel portale di Azure passare al gruppo di risorse dell'account di Automazione. Selezionare **Sposta** > **Sposta in un'altra sottoscrizione**.

    ![Screenshot della pagina Gruppo di risorse, Sposta in un'altra sottoscrizione](../media/move-account/move-resources.png)

2. Selezionare le risorse nel gruppo di risorse che si vuole spostare. Assicurarsi di includere l'account di Automazione, i runbook e le risorse dell'area di lavoro Log Analytics.

## <a name="re-create-run-as-accounts"></a>Ricreare gli account RunAs

Gli [account RunAs](../manage-runas-account.md) creano un'entità servizio in Azure Active Directory per l'autenticazione per le risorse di Azure. Quando si modificano le sottoscrizioni, l'account di Automazione non usa più l'account RunAs esistente. Per ricreare gli account RunAs:

1. Passare all'account di Automazione nella nuova sottoscrizione e selezionare **Account RunAs** in **Impostazioni account**. Si noterà che gli account RunAs vengono ora visualizzati come incompleti.

    ![Screenshot degli account RunAs visualizzati come incompleti](../media/move-account/run-as-accounts.png)

2. Eliminare gli account RunAs, uno alla volta, selezionando **Elimina** nella pagina **Proprietà**. 

    > [!NOTE]
    > Se non si hanno le autorizzazioni per creare o visualizzare gli account RunAs, viene visualizzato il messaggio seguente: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Per altre informazioni, vedere [Autorizzazioni necessarie per configurare gli account RunAs](../manage-runas-account.md#permissions).

3. Dopo aver eliminato gli account RunAs, selezionare **Crea** in **Account RunAs di Azure**. 

4. Nella pagina Aggiungi account RunAs di Azure selezionare **Crea** per creare l'account RunAs e l'entità servizio. 

5. Ripetere i passaggi precedenti con l'account RunAs classico di Azure.

## <a name="enable-features"></a>Abilitare le funzionalità

Dopo aver ricreato gli account RunAs, è necessario riabilitare le funzionalità rimosse prima dello spostamento:

1. Per attivare Rilevamento modifiche e inventario, selezionare **Rilevamento modifiche e inventario** nell'account di Automazione. Scegliere l'area di lavoro Log Analytics spostata e selezionare **Abilita**.

2. Ripetere il passaggio 1 per Gestione aggiornamenti.

    ![Screenshot della riabilitazione delle funzionalità nell'account di Automazione spostato](../media/move-account/reenable-solutions.png)

3. I computer abilitati con le funzionalità sono visibili dopo la connessione all'area di lavoro Log Analytics esistente. Per attivare la funzionalità di Avvio/Arresto di macchine virtuali durante gli orari di minore attività, è necessario abilitarla di nuovo. In **Risorse correlate** selezionare **Avvia/Arresta macchine virtuali**  > **Altre informazioni sulla soluzione e abilitare la soluzione** > **Crea** per avviare la distribuzione.

4. Nella pagina Aggiungi soluzione scegliere l'area di lavoro Log Analytics e l'account di Automazione.

    ![Screenshot del menu Aggiungi soluzione](../media/move-account/add-solution-vm.png)

5. Configurare la funzionalità come descritto in [Panoramica di Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verificare lo spostamento

Al termine dello spostamento, verificare che siano abilitate le funzionalità elencate di seguito. 

|Funzionalità|Test|Risoluzione dei problemi|
|---|---|---|
|Runbook|Un runbook può essere eseguito correttamente e connettersi alle risorse di Azure.|[Risolvere i problemi dei runbook](../troubleshoot/runbooks.md)
|Controllo del codice sorgente|È possibile eseguire una sincronizzazione manuale nel repository del controllo del codice sorgente.|[Integrazione del controllo del codice sorgente](../source-control-integration.md)|
|Rilevamento modifiche e inventario|Verificare che vengano visualizzati i dati di inventario correnti dai computer.|[Risolvere i problemi di Rilevamento modifiche e Inventario](../troubleshoot/change-tracking.md)|
|Gestione degli aggiornamenti|Verificare che i computer siano visualizzati e che siano integri.</br>Eseguire una distribuzione degli aggiornamenti software di test.|[Risolvere i problemi di Gestione aggiornamenti](../troubleshoot/update-management.md)|
|Risorse condivise|Verificare che vengano visualizzate tutte le risorse condivise, ad esempio [credenziali](../shared-resources/credentials.md) e [variabili](../shared-resources/variables.md).|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sullo spostamento di risorse in Azure, vedere [Spostare le risorse in Azure](../../azure-resource-manager/management/move-support-resources.md).
