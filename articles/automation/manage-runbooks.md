---
title: Gestire runbook in Automazione di Azure
description: Questo articolo descrive come gestire runbook in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ad2a34691a00f217db6cf6835eefed18c8862d32
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547929"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gestire runbook in Automazione di Azure

È possibile aggiungere un runbook ad Automazione di Azure [creandone uno nuovo](#create-a-runbook) o [importandone uno esistente](#import-a-runbook) da un file o da [Runbook Gallery](automation-runbook-gallery.md). In questo articolo vengono fornite informazioni sulla creazione e importazione di runbook da un file. È possibile ottenere tutti i dettagli dell'accesso ai runbook e ai moduli della community in Runbook e nelle raccolte di [moduli per Azure Automation.](automation-runbook-gallery.md)

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="create-a-runbook"></a>Creare un runbook

È possibile creare un nuovo runbook in Automazione di Azure usando uno dei portali di Azure o Windows PowerShell. Dopo aver creato il runbook, è possibile modificarlo usando le informazioni contenute in [Informazioni sul flusso di lavoro di PowerShell](automation-powershell-workflow.md) e [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Creare un runbook nel portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. Nell'hub selezionare **Runbooks** in **Process Automation** per aprire l'elenco dei runbook.
3. Fare clic su **Crea un runbook**.
4. Immettere un nome per il runbook e selezionarne [il tipo](automation-runbook-types.md). Il nome del runbook deve iniziare con una lettera e può contenere lettere, numeri, caratteri di sottolineatura e trattini.
5. Fare clic su **Crea** per creare il runbook e aprire l'editor.

### <a name="create-a-runbook-with-powershell"></a>Creare un runbook con PowerShell

È possibile utilizzare il cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) per creare un [runbook del flusso di lavoro](automation-runbook-types.md#powershell-workflow-runbooks)di PowerShell vuoto. Utilizzare `Type` il parametro per specificare uno `New-AzAutomationRunbook`dei tipi di runbook definiti per .

Nell'esempio seguente viene illustrato come creare un nuovo runbook vuoto.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importare un runbook

È possibile creare un nuovo runbook in Automazione di Azure importando uno script di PowerShell o un flusso di lavoro di PowerShell (**ps1**), un runbook grafico esportato (**.graphrunbook**) o uno script Python 2 (**.py**).  È necessario specificare il [tipo di runbook](automation-runbook-types.md) che viene creato durante l'importazione tenendo conto delle considerazioni seguenti.

* Un file **con estensione ps1** che non contiene un flusso di lavoro può essere importato in un [runbook](automation-runbook-types.md#powershell-runbooks) di PowerShell o in un runbook del flusso di lavoro di [PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Se lo si importa in un runbook del flusso di lavoro di PowerShell, viene convertito in un flusso di lavoro. In questo caso, i commenti vengono inclusi nel runbook per descrivere le modifiche apportate.

* Un file **con estensione ps1** contenente un flusso di lavoro di PowerShell può essere importato solo in un runbook del flusso di lavoro di [PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Se il file contiene più flussi di lavoro di PowerShell, l'importazione non riesce. È necessario salvare ogni flusso di lavoro nel relativo file e importare ogni file separatamente.

* Un file **con estensione ps1** contenente un flusso di lavoro di PowerShell non deve essere importato in un runbook di [PowerShell,](automation-runbook-types.md#powershell-runbooks)poiché il modulo di gestione di script di PowerShell non è in grado di riconoscerlo.

* Un file **.graphrunbook** può essere importato solo in un nuovo [runbook grafico.](automation-runbook-types.md#graphical-runbooks) Si noti che è possibile creare un runbook grafico solo da un file **.graphrunbook.**

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Importare un runbook da un file con il portale di AzureImport a runbook from a file with the Azure portal

È possibile usare la procedura seguente per importare un file di script in Automazione di Azure.

> [!NOTE]
> È possibile importare solo un file **con estensione ps1** in un runbook del flusso di lavoro di PowerShell usando il portale.

1. Nel portale di Azure aprire l'account di automazione.
2. Nell'hub selezionare **Runbooks** in **Process Automation** per aprire l'elenco dei runbook.
3. Fare clic su **Importa un runbook**.
4. Fare clic su **File Runbook** e selezionare il file da importare.
5. Se il campo **Nome** è abilitato, è possibile modificare il nome del runbook. Il nome deve iniziare con una lettera e può contenere lettere, numeri, caratteri di sottolineatura e trattini.
6. Il [tipo di runbook](automation-runbook-types.md) viene selezionato automaticamente, ma è possibile modificarlo dopo aver valutato le restrizioni applicabili.
7. Fare clic su **Crea**. Il nuovo runbook verrà visualizzato nell'elenco dei runbook dell'account di automazione.
8. È necessario [pubblicare il runbook](#publish-a-runbook) prima di poterlo eseguire.

> [!NOTE]
> Dopo aver importato un runbook grafico o un runbook del flusso di lavoro di PowerShell grafico, è possibile convertirlo in un altro tipo. Tuttavia, non è possibile convertire uno di questi runbook grafici in un runbook testuale.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importare un runbook da un file di script con Windows PowerShellImport a runbook from a script file with Windows PowerShell

Utilizzare il cmdlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) per importare un file di script come runbook del flusso di lavoro di PowerShell. Se il runbook esiste già, l'importazione non riesce a meno che non si utilizzi il `Force` parametro con il cmdlet.

Nell'esempio seguente viene illustrato come importare un file di script in un runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testare un runbook

Quando si testa un Runbook, viene eseguita la [Versione bozza](#publish-a-runbook) e le azioni eseguite vengono completate. Non viene creata alcuna cronologia dei processi, ma i flussi [Output](automation-runbook-output-and-messages.md#output-stream) e [Avviso ed Errore](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati nel riquadro Output test. I messaggi per il flusso dettagliato vengono visualizzati `VerbosePreference` nel [riquadro](automation-runbook-output-and-messages.md#message-streams) Output solo se la variabile](automation-runbook-output-and-messages.md-preference-variables) è impostata su Continue.

Anche se si esegue la versione bozza, il runbook viene comunque eseguito normalmente ed effettua qualsiasi azione sulle risorse nell'ambiente. Per questo motivo, è necessario testare i runbook solo con risorse non di produzione.

La procedura per testare ogni [tipo di runbook](automation-runbook-types.md) è la stessa. Non c'è differenza nei test tra l'editor testuale e l'editor grafico nel portale di Azure.There's no difference in testing between the textual editor and the graphical editor in the Azure portal.

1. Aprire la versione Bozza del runbook [nell'editor testuale](automation-edit-textual-runbook.md) o nell'editor [grafico](automation-graphical-authoring-intro.md).
1. Fare clic sul pulsante **Test** per aprire la pagina Test.
1. Se il runbook dispone di parametri, questi vengono elencati nel riquadro sinistro, in cui è possibile fornire valori da utilizzare per il test.
1. Se si desidera eseguire il test su un ruolo di [lavoro ibrido per runbook,](automation-hybrid-runbook-worker.md)modificare **Impostazioni esecuzione** in **Lavoratore ibrido** e selezionare il nome del gruppo di destinazione.  In caso contrario, mantenere l'impostazione predefinita **Azure** per eseguire il test nel cloud.
1. Fare clic sul pulsante **Start** per avviare il test.
1. È possibile usare i pulsanti nel riquadro Output per arrestare o sospendere un flusso di lavoro di [PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o un runbook [grafico](automation-runbook-types.md#graphical-runbooks) durante il test. Quando si sospende il runbook, esso completa l'attività corrente prima di essere sospeso. Una volta che il runbook viene sospeso, è possibile arrestarlo o riavviarlo.
1. Esaminare l'output del runbook nel riquadro Output.Inspect the output from the runbook in the Output pane.

## <a name="publish-a-runbook"></a>Pubblicare un runbook

Quando si crea o si importa un nuovo runbook, è necessario pubblicarlo prima di poterlo eseguire. Ogni runbook in Automazione di Azure ha una versione bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre è possibile modificare solo la versione bozza. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Quando la versione bozza deve essere resa disponibile, la si pubblica, sovrascrivendo la versione pubblicata corrente con la versione bozza.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Pubblicare un runbook nel portale di AzurePublish a runbook in the Azure portal

1. Aprire il runbook nel portale di Azure.
2. Fare clic su **Edit**.
3. Fare clic su **Pubblica** e quindi **su Sì** in risposta al messaggio di verifica.

### <a name="publish-a-runbook-using-powershell"></a>Pubblicare un runbook tramite PowerShellPublish a runbook using PowerShell

Utilizzare il cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) per pubblicare un runbook con Windows PowerShell. Nell'esempio seguente viene illustrato come pubblicare un runbook di esempio.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

### <a name="schedule-a-runbook-in-the-azure-portal"></a>Pianificare un runbook nel portale di AzureSchedule a runbook in the Azure portal

Quando il runbook è stato pubblicato, è possibile pianificarlo per il funzionamento.

1. Aprire il runbook nel portale di Azure.
2. Selezionare **Pianificazioni** in **Risorse**.
3. Selezionare **Aggiungi pianificazione**.
4. Nel riquadro Pianificazione del runbook selezionare **Collega una pianificazione al runbook**.
5. Scegliere **Crea una nuova pianificazione** nel riquadro Pianificazione.
6. Immettere un nome, una descrizione e altri parametri nel riquadro Nuova pianificazione. 
7. Una volta creata la pianificazione, evidenziarla e fare clic su **OK**. Ora dovrebbe essere collegato al tuo runbook.
8. Cercare un messaggio di posta elettronica nella cassetta postale per notificare lo stato del runbook.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come trarre vantaggio dalla raccolta di moduli runbook e PowerShell, vedere Raccolte di [moduli e runbook per Automazione di Azure.](automation-runbook-gallery.md)
* Per altre informazioni sulla modifica dei runbook di PowerShell e del flusso di lavoro di PowerShell con un editor testuale, vedere [Modifica dei runbook testuali in Automazione di Azure.To](automation-edit-textual-runbook.md)learn more about editing PowerShell and PowerShell Workflow runbooks with a textual editor, see Editing textual runbooks in Azure Automation.
* Per altre informazioni sulla creazione di runbook grafici, vedere [Creazione grafica in Automazione di Azure.To](automation-graphical-authoring-intro.md)learn more about graphical runbook authoring, see Graphical authoring in Azure Automation .
