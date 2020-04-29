---
title: Gestire runbook in Automazione di Azure
description: Questo articolo descrive come gestire runbook in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: a1229ee389b41625554fb2869089b08a3cb9cb6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676504"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gestire runbook in Automazione di Azure

È possibile aggiungere una Runbook ad automazione di Azure [creandone una nuova](#creating-a-runbook) o [importandone una esistente](#importing-a-runbook) da un file o dalla [raccolta Runbook](automation-runbook-gallery.md). In questo articolo vengono fornite informazioni sulla creazione e importazione di runbook da un file. È possibile ottenere tutti i dettagli relativi all'accesso ai manuali operativi e ai moduli della community in [Runbook e nelle raccolte di moduli per automazione di Azure](automation-runbook-gallery.md).

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="creating-a-runbook"></a>Creazione di un Runbook

È possibile creare un nuovo runbook in Automazione di Azure usando uno dei portali di Azure o Windows PowerShell. Dopo aver creato il runbook, è possibile modificarlo usando le informazioni contenute in [Informazioni sul flusso di lavoro di PowerShell](automation-powershell-workflow.md) e [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Creare un runbook nel portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. Dall'hub selezionare **manuali operativi** in **automazione processi** per aprire l'elenco di manuali operativi.
3. Fare clic su **Create a Runbook**.
4. Immettere un nome per il Runbook e selezionare il relativo [tipo](automation-runbook-types.md). Il nome del Runbook deve iniziare con una lettera e può contenere lettere, numeri, caratteri di sottolineatura e trattini.
5. Fare clic su **Crea** per creare il runbook e aprire l'editor.

### <a name="create-a-runbook-with-powershell"></a>Creare un runbook con PowerShell

È possibile usare il cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) per creare un [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)vuoto. Usare il `Type` parametro per specificare uno dei tipi Runbook definiti per `New-AzAutomationRunbook`.

Nell'esempio seguente viene illustrato come creare un nuovo Runbook vuoto.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook"></a>Importazione di un Runbook

È possibile creare un nuovo Runbook in automazione di Azure importando uno script di PowerShell o un flusso di lavoro di PowerShell (con**estensione ps1**), un Runbook grafico esportato (con**estensione graphrunbook**) o uno script python2 (con**estensione py**).  È necessario specificare il [tipo di runbook](automation-runbook-types.md) che viene creato durante l'importazione tenendo conto delle considerazioni seguenti.

* È possibile importare un file con **estensione ps1** che non contiene un flusso di lavoro in un [Runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) o in un [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se viene importato in un Runbook del flusso di lavoro PowerShell, questo viene convertito in un flusso di lavoro. In questo caso, i commenti vengono inclusi in Runbook per descrivere le modifiche apportate.

* È possibile importare solo un file con **estensione ps1** contenente un flusso di lavoro PowerShell in un [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se il file contiene più flussi di lavoro di PowerShell, l'importazione avrà esito negativo. È necessario salvare ogni flusso di lavoro nel relativo file e importare ogni file separatamente.

* Non importare un file con **estensione ps1** contenente un flusso di lavoro PowerShell in un [Runbook di PowerShell](automation-runbook-types.md#powershell-runbooks), perché il motore di script di PowerShell non è in grado di riconoscerlo.

* È possibile importare solo un file con **estensione graphrunbook** in un nuovo [Runbook grafico](automation-runbook-types.md#graphical-runbooks). Si noti che è possibile creare un Runbook grafico solo da un file con **estensione graphrunbook** .

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Importare un Runbook da un file con il portale di Azure

È possibile usare la procedura seguente per importare un file di script in Automazione di Azure.

> [!NOTE]
> È possibile importare solo un file con **estensione ps1** in un Runbook del flusso di lavoro PowerShell usando il portale.

1. Nel portale di Azure aprire l'account di automazione.
2. Dall'hub selezionare **manuali operativi** in **automazione processi** per aprire l'elenco di manuali operativi.
3. Fare clic su **Import a Runbook**.
4. Fare clic su **file Runbook** e selezionare il file da importare.
5. Se il campo **nome** è abilitato, è possibile modificare il nome del Runbook. Il nome deve iniziare con una lettera e può contenere lettere, numeri, caratteri di sottolineatura e trattini.
6. Il [tipo di runbook](automation-runbook-types.md) viene selezionato automaticamente, ma è possibile modificarlo dopo aver valutato le restrizioni applicabili.
7. Scegliere **Crea**. Il nuovo runbook verrà visualizzato nell'elenco dei runbook dell'account di automazione.
8. È necessario [pubblicare il runbook](#publishing-a-runbook) prima di poterlo eseguire.

> [!NOTE]
> Dopo aver importato un Runbook grafico o un Runbook grafico del flusso di lavoro PowerShell, è possibile convertirlo in un altro tipo. Tuttavia, non è possibile convertire uno di questi manuali operativi grafici in un Runbook testuale.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importare un Runbook da un file di script con Windows PowerShell

Usare il cmdlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) per importare un file di script come bozza Runbook del flusso di lavoro PowerShell. Se il Runbook esiste già, l'importazione ha esito negativo a meno `Force` che non si usi il parametro con il cmdlet.

Nell'esempio seguente viene illustrato come importare un file di script in un Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="testing-a-runbook"></a>Test di un Runbook

Quando si testa un Runbook, viene eseguita la [Versione bozza](#publishing-a-runbook) e le azioni eseguite vengono completate. Non viene creata alcuna cronologia processo, ma i flussi di [output](automation-runbook-output-and-messages.md#output-stream) e di [avviso e di errore](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati nel riquadro di output del test. I messaggi nel [flusso dettagliato](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati nel riquadro di output solo se la variabile [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) è impostata su `Continue`.

Anche se si esegue la versione bozza, il runbook viene comunque eseguito normalmente ed effettua qualsiasi azione sulle risorse nell'ambiente. Per questo motivo, è necessario testare i runbook solo con risorse non di produzione.

La procedura per testare ogni [tipo di Runbook](automation-runbook-types.md) è la stessa. Non esiste alcuna differenza nei test tra l'editor di testo e l'editor grafico nel portale di Azure.

1. Aprire la versione bozza del Runbook nell' [editor di testo](automation-edit-textual-runbook.md) o nell' [Editor grafico](automation-graphical-authoring-intro.md).
1. Fare clic sul pulsante **Test** per aprire la pagina Test.
1. Se il Runbook dispone di parametri, questi vengono elencati nel riquadro a sinistra, in cui è possibile specificare i valori da usare per il test.
1. Se si vuole eseguire il test in un ruolo di [lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md), modificare le **impostazioni di esecuzione** nel ruolo di **lavoro ibrido** e selezionare il nome del gruppo di destinazione.  In caso contrario, mantenere l'impostazione predefinita **Azure** per eseguire il test nel cloud.
1. Fare clic sul pulsante **Start** per avviare il test.
1. È possibile usare i pulsanti nel riquadro output per arrestare o sospendere un [flusso di lavoro di PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o un Runbook [grafico](automation-runbook-types.md#graphical-runbooks) durante il test. Quando si sospende il runbook, esso completa l'attività corrente prima di essere sospeso. Una volta che il runbook viene sospeso, è possibile arrestarlo o riavviarlo.
1. Esaminare l'output del Runbook nel riquadro di output.

## <a name="publishing-a-runbook"></a>Pubblicazione di un runbook

Quando si crea o si importa un nuovo runbook, è necessario pubblicarlo prima di poterlo eseguire. Ogni Runbook in automazione di Azure include una versione bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre è possibile modificare solo la versione bozza. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Quando la versione bozza deve essere resa disponibile, è possibile pubblicarla, sovrascrivendo la versione pubblicata corrente con la versione bozza.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Pubblicare un Runbook nel portale di Azure

1. Aprire il runbook nel portale di Azure.
2. Fare clic su **Edit**.
3. Fare clic su **pubblica** e quindi su **Sì** in risposta al messaggio di verifica.

### <a name="publish-a-runbook-using-powershell"></a>Pubblicare un runbook con PowerShell

Usare il cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) per pubblicare un runbook con Windows PowerShell. Nell'esempio seguente viene illustrato come pubblicare un Runbook di esempio.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="scheduling-a-runbook-in-the-azure-portal"></a>Pianificazione di un Runbook nella portale di Azure

Quando il Runbook è stato pubblicato, è possibile pianificarlo per l'operazione.

1. Aprire il runbook nel portale di Azure.
2. Selezionare **pianificazioni** in **risorse**.
3. Selezionare **Aggiungi pianificazione**.
4. Nel riquadro Pianifica Runbook selezionare **collega una pianificazione al Runbook**.
5. Scegliere **Crea una nuova pianificazione** nel riquadro pianificazione.
6. Immettere un nome, una descrizione e altri parametri nel riquadro nuova pianificazione. 
7. Una volta creata la pianificazione, evidenziarla e fare clic su **OK**. A questo punto dovrebbe essere collegato a Runbook.
8. Cercare un messaggio di posta elettronica nella cassetta postale per notificare lo stato di Runbook.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come sfruttare i vantaggi offerti dalla raccolta di moduli Runbook e PowerShell, vedere [raccolte di Runbook e moduli per automazione di Azure](automation-runbook-gallery.md).
* Per altre informazioni sulla modifica di PowerShell e manuali operativi del flusso di lavoro PowerShell con un editor di testo, vedere [modifica di manuali operativi testuali in automazione di Azure](automation-edit-textual-runbook.md).
* Per altre informazioni sulla creazione di Runbook grafici, vedere [creazione grafica in automazione di Azure](automation-graphical-authoring-intro.md).
