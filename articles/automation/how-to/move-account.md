---
title: Spostare l'account di automazione di Azure in un'altra sottoscrizione
description: Questo articolo descrive come spostare l'account di automazione in un'altra sottoscrizione.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bfb2f2d1d0f6a0d11784847344cd3dbcafdb0959
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900989"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Spostare l'account di automazione di Azure in un'altra sottoscrizione

Automazione di Azure consente di spostare alcune risorse in un nuovo gruppo di risorse o una nuova sottoscrizione. È possibile spostare le risorse tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per altre informazioni sul processo, vedere [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

L'account di automazione è una delle risorse che è possibile spostare. In questo articolo si apprenderà come spostare gli account di automazione in un'altra risorsa o sottoscrizione. Di seguito sono riportati i passaggi di alto livello per lo trasferimento dell'account di automazione:

1. Rimuovere le soluzioni.
2. Scollegare l'area di lavoro.
3. Spostare l'account di automazione.
4. Eliminare e ricreare gli account RunAs.
5. Abilitare nuovamente le soluzioni.

>[!NOTE]
>In questo articolo si usa il Azure PowerShell AZ Module. È comunque possibile usare il modulo AzureRM. Per ulteriori informazioni su AZ Module e AzureRM Compatibility, vedere [la pagina relativa all'introduzione del nuovo Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Rimuovi soluzioni

Per scollegare l'area di lavoro dall'account di automazione, è necessario rimuovere queste soluzioni dall'area di lavoro:

- Rilevamento modifiche e inventario
- Gestione degli aggiornamenti
- Avviare/arrestare le VM fuori orario

1. Individuare il gruppo di risorse nel portale di Azure.
2. Trovare ogni soluzione e selezionare **Elimina** nella pagina **Elimina risorse** .

    ![Screenshot dell'eliminazione di soluzioni dal portale di Azure](../media/move-account/delete-solutions.png)

Se si preferisce, è possibile eliminare le soluzioni usando il cmdlet [Remove-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) :

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Rimuovere le regole di avviso per la soluzione di avvio/arresto di macchine virtuali durante le ore di indisponibilità

Per questa soluzione, è necessario rimuovere anche le regole di avviso create dalla soluzione.

1. Nel portale di Azure andare al gruppo di risorse e selezionare **monitoraggio** > **avvisi** > **Gestisci regole di avviso**.

   ![Screenshot della pagina degli avvisi, che mostra la selezione delle regole di avviso di gestione](../media/move-account/alert-rules.png)

2. Nella pagina **regole** verrà visualizzato un elenco degli avvisi configurati in tale gruppo di risorse. La soluzione crea le regole seguenti:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selezionare le regole una alla volta e selezionare **Elimina** per rimuoverle.

    ![Screenshot della pagina delle regole, richiesta di conferma dell'eliminazione per le regole selezionate](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Se non viene visualizzata alcuna regola di avviso nella pagina **regole** , impostare il campo **stato** su **disabilitato** per visualizzare gli avvisi disabilitati. Potrebbero essere state disabilitate.

4. Quando si rimuovono le regole di avviso, è necessario rimuovere il gruppo di azioni creato per le notifiche della soluzione "avvia/arresta macchine virtuali durante le ore di inattività". Nella portale di Azure selezionare **monitoraggio** > **avvisi** > **Gestisci gruppi di azioni**.

5. Selezionare **StartStop_VM_Notification**. 

6. Nella pagina gruppo di azione selezionare **Elimina**.

    ![Screenshot della pagina del gruppo di azioni](../media/move-account/delete-action-group.png)

Se si preferisce, è possibile eliminare il gruppo di azioni usando il cmdlet [Remove-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) :

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Scollega l'area di lavoro

A questo punto è possibile scollegare l'area di lavoro:

1.  > Nella portale di Azure selezionare**risorse correlate all'** **account** > di automazione**area di lavoro collegata**. 

2. Selezionare **Scollega area di lavoro** per scollegare l'area di lavoro dall'account di automazione.

    ![Screenshot dello scollegamento di un'area di lavoro da un account di automazione](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Spostare l'account di automazione

È ora possibile spostare l'account di automazione e il relativo manuali operativi. 

1. Nella portale di Azure passare al gruppo di risorse dell'account di automazione. Selezionare **Sposta** > **spostamento in un'altra sottoscrizione**.

    ![Screenshot della pagina del gruppo di risorse, passare a un'altra sottoscrizione](../media/move-account/move-resources.png)

2. Selezionare le risorse nel gruppo di risorse che si desidera spostare. Assicurarsi di includere l'account di automazione, manuali operativi e le risorse dell'area di lavoro Log Analytics.

## <a name="re-create-run-as-accounts"></a>Ricrea account RunAs

Gli [account RunAs](../manage-runas-account.md) creano un'entità servizio in Azure Active Directory per l'autenticazione con le risorse di Azure. Quando si modificano le sottoscrizioni, l'account di automazione non utilizza più l'account RunAs esistente. Per ricreare gli account RunAs:

1. Passare all'account di automazione nella nuova sottoscrizione e selezionare **account RunAs** in **Impostazioni account**. Si noterà che gli account RunAs vengono visualizzati come incompleti.

    ![Screenshot degli account RunAs, mostrando incompleto](../media/move-account/run-as-accounts.png)

2. Eliminare gli account RunAs, uno alla volta, selezionando **Elimina** nella pagina delle **Proprietà** . 

    > [!NOTE]
    > Se non si dispone delle autorizzazioni per creare o visualizzare gli account RunAs, viene visualizzato il messaggio seguente `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` : per ulteriori informazioni, vedere [autorizzazioni necessarie per configurare gli account RunAs](../manage-runas-account.md#permissions).

3. Dopo aver eliminato gli account RunAs, selezionare **Crea** in **account RunAs di Azure**. 

4. Nella pagina **Aggiungi account RunAs di Azure** selezionare **Crea** per creare l'account RunAs e l'entità servizio. 

5. Ripetere i passaggi precedenti con l'account RunAs classico di Azure.

## <a name="enable-solutions"></a>Abilitare soluzioni

Dopo aver ricreato gli account RunAs, è necessario riabilitare le soluzioni rimosse prima dello spostamento: 

1. Per attivare la soluzione "Rilevamento modifiche e inventario", selezionare **rilevamento modifiche e inventario** nell'account di automazione. Scegliere l'area di lavoro Log Analytics spostata e selezionare **Abilita**.

2. Ripetere il passaggio 1 per la soluzione "Gestione aggiornamenti".

    ![Screenshot della riabilitazione di soluzioni nell'account di automazione spostato](../media/move-account/reenable-solutions.png)

3. I computer caricati con le soluzioni sono visibili quando si è connessi all'area di lavoro Log Analytics esistente. Per attivare la soluzione di avvio/arresto di macchine virtuali durante le ore di indisponibilità, è necessario ridistribuire la soluzione. In **risorse correlate**selezionare **Avvia/arresta macchine virtuali** > **altre informazioni su e abilitare la creazione della soluzione** > **Create** per avviare la distribuzione.

4. Nella pagina **Aggiungi soluzione** scegliere l'area di lavoro log Analytics e l'account di automazione.

    ![Screenshot del menu Aggiungi soluzione](../media/move-account/add-solution-vm.png)

5. Configurare la soluzione come descritto in [avvio/arresto di macchine virtuali durante gli orari di indisponibilità della soluzione in automazione di Azure](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verificare lo spostamento

Al termine dello spostamento, verificare che siano abilitate le funzionalità elencate di seguito. 

|Funzionalità|Test|Risoluzione dei problemi|
|---|---|---|
|Runbook|Un Runbook può essere eseguito correttamente e connettersi alle risorse di Azure.|[Risolvere i problemi dei runbook](../troubleshoot/runbooks.md)
|Controllo del codice sorgente|È possibile eseguire una sincronizzazione manuale nel repository del controllo del codice sorgente.|[Integrazione del controllo del codice sorgente](../source-control-integration.md)|
|Rilevamento modifiche e inventario|Verificare che vengano visualizzati i dati di inventario correnti dai computer.|[Risolvere i problemi del rilevamento delle modifiche](../troubleshoot/change-tracking.md)|
|Gestione degli aggiornamenti|Verificare che i computer siano visualizzati e che siano integri.</br>Eseguire una distribuzione degli aggiornamenti software di test.|[Risolvere i problemi relativi alla gestione degli aggiornamenti](../troubleshoot/update-management.md)|
|Risorse condivise|Verificare che vengano visualizzate tutte le risorse condivise, ad esempio le [credenziali](../shared-resources/credentials.md) e le [variabili](../shared-resources/variables.md).|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sullo spostamento di risorse in Azure in generale, vedere [spostare le risorse in Azure](../../azure-resource-manager/management/move-support-resources.md).
