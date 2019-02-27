---
title: Gestire runbook in Automazione di Azure
description: Questo articolo descrive come gestire runbook in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da3b09998d163ffcc16bfcbbf9f516467dd3311d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418436"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gestire runbook in Automazione di Azure

Per aggiungere un runbook ad Automazione di Azure, è possibile [creare un nuovo runbook](#creating-a-new-runbook) oppure importarne uno esistente da un file o dalla [raccolta di runbook](automation-runbook-gallery.md). In questo articolo vengono fornite informazioni sulla creazione e importazione di runbook da un file.  È possibile ottenere tutti i dettagli sull'accesso ai runbook e ai moduli della community in [Raccolte di runbook e moduli per l'automazione di Azure](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Creare un runbook

È possibile creare un nuovo runbook in Automazione di Azure usando uno dei portali di Azure o Windows PowerShell. Dopo aver creato il runbook, è possibile modificarlo usando le informazioni contenute in [Informazioni sul flusso di lavoro di PowerShell](automation-powershell-workflow.md) e [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Creare un runbook nel portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. Dall'hub selezionare **Runbook** per aprire l'elenco dei runbook.
3. Fare clic sul pulsante **Aggiungi runbook** e quindi su **Crea un nuovo runbook**.
4. Digitare un **nome** per il runbook e selezionare il relativo [tipo](automation-runbook-types.md). Il nome del runbook deve iniziare con una lettera e può includere lettere, numeri, caratteri di sottolineatura e trattini.
5. Fare clic su **Crea** per creare il runbook e aprire l'editor.

### <a name="create-a-runbook-with-powershell"></a>Creare un runbook con PowerShell

È possibile usare il cmdlet [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) per creare un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vuoto. Usare il parametro **Type** per specificare uno dei quattro tipi di runbook.

I comandi di esempio seguenti mostrano come creare un nuovo runbook vuoto.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importare un runbook

È possibile creare un nuovo runbook in Automazione di Azure mediante l'importazione di uno script di PowerShell o un flusso di lavoro PowerShell (con estensione ps1), un runbook grafico esportato (con estensione graphrunbook), o uno script Python 2 (con estensione py).  È necessario specificare il [tipo di runbook](automation-runbook-types.md) che viene creato durante l'importazione tenendo conto delle considerazioni seguenti.

* Un file `.graphrunbook` può essere importato solo in un nuovo [runbook grafico](automation-runbook-types.md#graphical-runbooks) e i runbook grafici possono essere creati solo da un file `.graphrunbook`.
* Un file `.ps1` contenente un flusso di lavoro di PowerShell può essere importato solo in un [runbook del flusso di lavoro di PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se il file contiene più flussi di lavoro PowerShell, l'importazione avrà esito negativo. È necessario salvare ogni flusso di lavoro nel relativo file e importare ogni file separatamente.
* Un file `.ps1` che non contiene un flusso di lavoro può essere importato in un [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) oppure in un [runbook del flusso di lavoro di PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se viene importato in un runbook del flusso di lavoro PowerShell, il file viene quindi convertito in un flusso di lavoro, dove sono inclusi i commenti contenenti le modifiche apportate.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Per importare un runbook da un file con il portale di Azure

È possibile usare la procedura seguente per importare un file di script in Automazione di Azure.  

> [!NOTE]
> Si noti che è possibile importare solo un file con estensione PS1 in un runbook del flusso di lavoro PowerShell con il portale.

1. Nel portale di Azure aprire l'account di automazione.
2. Dall'hub selezionare **Runbook** per aprire l'elenco dei runbook.
3. Fare clic sul pulsante **Aggiungi runbook** e quindi su **Importa**.
4. Fare clic su **File di runbook** per selezionare il file da importare.
5. Se il campo **Nome** è abilitato, sarà possibile modificare il nome.  Il nome del runbook deve iniziare con una lettera e può includere lettere, numeri, caratteri di sottolineatura e trattini.
6. Il [tipo di runbook](automation-runbook-types.md) viene selezionato automaticamente, ma è possibile modificarlo dopo aver valutato le restrizioni applicabili. 
7. Il nuovo runbook verrà visualizzato nell'elenco dei runbook dell'account di automazione.
8. È necessario [pubblicare il runbook](#publishing-a-runbook) prima di poterlo eseguire.

> [!NOTE]
> Dopo aver importato un runbook grafico o un runbook grafico del flusso di lavoro PowerShell, è possibile convertirlo in un altro tipo, se necessario. Non è possibile eseguire la conversione verso un runbook in formato di testo.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Per importare un runbook da un file di script con Windows PowerShell

È possibile usare il cmdlet [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) per importare un file di script come bozza di un runbook del flusso di lavoro PowerShell. Se il runbook esiste già, l'importazione non riesce, a meno che non si usi il parametro *-Force* .

I comandi di esempio seguenti mostrano come importare un file di script in un runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testare un runbook

Quando si testa un Runbook, viene eseguita la [Versione bozza](#publishing-a-runbook) e le azioni eseguite vengono completate. Non viene creata alcuna cronologia dei processi, ma nel pannello di output del test vengono visualizzati i flussi di [output](automation-runbook-output-and-messages.md#output-stream) e di [avviso ed errore](automation-runbook-output-and-messages.md#message-streams). I messaggi per il [flusso dettagliato](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati solo se nel pannello di output la [variabile $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) è impostata su Continua.

Anche se si esegue la versione bozza, il runbook viene comunque eseguito normalmente ed effettua qualsiasi azione sulle risorse nell'ambiente. Per questo motivo, è necessario testare i runbook solo con risorse non di produzione.

La procedura per testare ogni [tipo di runbook](automation-runbook-types.md) è la stessa e non vi è alcuna differenza tra l'eseguire il test nell'editor di testo e nell'editor grafico nel portale di Azure.  

1. Aprire la versione bozza del runbook in un [editor di testo](automation-edit-textual-runbook.md) o in un [editor grafico](automation-graphical-authoring-intro.md).
1. Fare clic sul pulsante **Test** per aprire la pagina Test.
1. Se il runbook dispone di parametri, essi verranno elencati nel riquadro sinistro, dove è possibile fornire valori da usare per il test.
1. Se si desidera eseguire il test su un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md), specificare per l'opzione **Impostazioni di esecuzione** il valore **Ruolo di lavoro ibrido** e selezionare il nome del gruppo di destinazione.  In caso contrario, mantenere l'impostazione predefinita **Azure** per eseguire il test nel cloud.
1. Fare clic sul pulsante **Start** per avviare il test.
1. Se il runbook è di tipo [Flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o [Grafico](automation-runbook-types.md#graphical-runbooks), è possibile arrestarlo o sospenderlo durante il test usando i pulsanti presenti sotto il riquadro di output. Quando si sospende il runbook, esso completa l'attività corrente prima di essere sospeso. Una volta che il runbook viene sospeso, è possibile arrestarlo o riavviarlo.
1. Esaminare l'output dal runbook nel pannello di output.

## <a name="publish-a-runbook"></a>Pubblicare un runbook

Quando si crea o si importa un nuovo runbook, è necessario pubblicarlo prima di poterlo eseguire.  Ogni runbook in Automazione include una versione bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre è possibile modificare solo la versione bozza. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Dopo aver reso disponibile la versione bozza, è possibile pubblicarla sovrascrivendo così la versione pubblicata.

### <a name="azure-portal"></a>Portale di Azure

1. Aprire il runbook nel portale di Azure.
2. Fare clic sul pulsante **Modifica** .
3. Fare clic sul pulsante **Pubblica** e quindi su **Sì** nel messaggio di verifica.

### <a name="powershell"></a>PowerShell

È possibile usare il cmdlet [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) per pubblicare un runbook con Windows PowerShell. I comandi di esempio seguenti mostrano come pubblicare un runbook di esempio.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come sfruttare al meglio le raccolte di runbook e moduli di PowerShell, vedere [Raccolte di runbook e moduli per l'automazione di Azure](automation-runbook-gallery.md)
* Per altre informazioni sulla modifica di runbook di PowerShell e del flusso di lavoro PowerShell con un editor di testo, vedere [Modifica di runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md)
* Per altre informazioni sulla creazione grafica di runbook, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)
