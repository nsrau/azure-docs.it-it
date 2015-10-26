<properties 
	pageTitle="Creazione o importazione di un runbook in Automazione di Azure"
	description="In questo articolo viene descritto come creare un nuovo runbook in Automazione di Azure o importarne uno da un file."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/22/2015"
	ms.author="bwren" />

# Creazione o importazione di un runbook in Automazione di Azure

Per aggiungere un runbook ad Automazione di Azure, è possibile [creare un nuovo runbook](#creating-a-new-runbook) oppure importare un runbook esistente da un file o dalla [raccolta di runbook](automation-runbook-gallery.md). In questo articolo vengono fornite informazioni sulla creazione e importazione di runbook da un file. È possibile ottenere tutti i dettagli sull'accesso ai runbook e ai moduli della community in [Raccolte di runbook e moduli per l'automazione di Azure](automation-runbook-gallery.md).

## Creazione di un nuovo runbook

È possibile creare un nuovo runbook in Automazione di Azure usando uno dei portali di Azure o Windows PowerShell. Dopo aver creato il runbook, è possibile modificarlo usando informazioni contenute nella sezione [Informazioni sul flusso di lavoro PowerShell](automation-powershell-workflow.md) e [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

### Per creare un nuovo runbook di Automazione di Azure nel portale di Azure

Nel portale di Azure è possibile usare solo i [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

1. Nel portale di Azure fare clic su **Nuovo**, **Servizi app**, **Automazione**, **Runbook**, **Creazione rapida**.
2. Immettere le informazioni necessarie e quindi fare clic su **Crea**. Il nome del runbook deve iniziare con una lettera e può includere lettere, numeri, caratteri di sottolineatura e trattini.
3. Se si desidera modificare il runbook ora, fare clic su **Modifica runbook**. In caso contrario, fare clic su **OK**.
4. Il nuovo runbook verrà visualizzato nella scheda **Runbook**.


### Per creare un nuovo runbook di Automazione di Azure nel portale di anteprima di Azure

1. Nel portale di anteprima di Azure aprire l'account di automazione. 
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Fare clic sul pulsante **Aggiungi un runbook** e quindi su **Crea un nuovo runbook**.
2. Digitare un **nome** per il runbook e selezionare il relativo [tipo](automation-runbook-types.md). Il nome del runbook deve iniziare con una lettera e può includere lettere, numeri, caratteri di sottolineatura e trattini.
3. Fare clic su **Crea** per creare il runbook e aprire l'editor.


### Per creare un nuovo runbook di Automazione di Azure con Windows PowerShell

È possibile usare il cmdlet [New-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690272.aspx) per creare un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vuoto. È possibile specificare il parametro **Name** per creare un runbook vuoto che è possibile modificare successivamente oppure il parametro **Path** per importare un file di script.

I comandi di esempio seguenti mostrano come creare un nuovo runbook vuoto.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    New-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName 

## Importazione di un runbook da un file in Automazione di Azure

È possibile creare un nuovo runbook in Automazione di Azure mediante l'importazione di uno script di PowerShell o un flusso di lavoro di PowerShell (con estensione PS1) o un runbook grafico esportato (con estensione GRAPHRUNBOOK). È necessario specificare il [tipo di runbook](automation-runbook-types.md) che verrà creato dal processo di importazione tenendo conto delle considerazioni seguenti.

- Un file con estensione GRAPHRUNBOOK può essere importato solo in un nuovo [runbook grafico](automation-runbook-types.md#graphical-runbooks), mentre i runbook grafici possono essere creati solo da un file con estensione GRAPHRUNBOOK.
- Un file con estensione PS1 che contiene un flusso di lavoro di PowerShell può essere importato solo in un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se il file contiene più flussi di lavoro PowerShell, l'importazione avrà esito negativo. È necessario salvare ogni flusso di lavoro nel relativo file e importare ogni file separatamente.
- Un file PS1 contenente un flusso di lavoro può essere importato in un [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) o in un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se viene importato in un runbook del flusso di lavoro PowerShell, il file verrà quindi convertito in un flusso di lavoro, dove verranno inclusi i commenti contenenti le modifiche apportate.

### Per importare un runbook da un file con il portale di Azure
È possibile usare la procedura seguente per importare un file di script in Automazione di Azure. Si noti che è possibile importare solo un file con estensione PS1 in un runbook del flusso di lavoro PowerShell con questo portale. È necessario usare il portale di anteprima di Azure per gli altri tipi.

1. Nel portale di gestione di Azure selezionare **Automazione** e quindi selezionare un account di automazione.
2. Fare clic su **Importa**.
3. Fare clic su **Cerca file** e individuare il file di script da importare.
4. Se si desidera modificare il runbook ora, fare clic su **Modifica runbook**. In caso contrario, fare clic su OK.
5. Il nuovo runbook verrà visualizzato nella scheda **Runbook** per l'account di automazione.
6. È necessario [pubblicare il runbook](#publishing-a-runbook) prima di poterlo eseguire.


### Per importare un runbook da un file con il portale di anteprima di Azure
È possibile usare la procedura seguente per importare un file di script in Automazione di Azure. Si noti che è possibile importare solo un file con estensione PS1 in un runbook del flusso di lavoro PowerShell con questo portale.

1. Nel portale di anteprima di Azure aprire l'account di automazione. 
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Fare clic sul pulsante **Aggiungi un runbook** e quindi su **Importa**.
4. Fare clic su **File di runbook** per selezionare il file da importare.
2. Se il campo **Nome** è abilitato, sarà possibile modificare il nome. Il nome del runbook deve iniziare con una lettera e può includere lettere, numeri, caratteri di sottolineatura e trattini.
3. Selezionare un [tipo di runbook](automation-runbook-types.md) considerando le restrizioni elencate in precedenza.
3. Il nuovo runbook verrà visualizzato nell'elenco di runbook per l'account di automazione.
4. È necessario [pubblicare il runbook](#publishing-a-runbook) prima di poterlo eseguire.

### Per importare un runbook da un file di script con Windows PowerShell

È possibile usare il cmdlet [Set-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690267.aspx) per importare un file di script nella versione bozza di un runbook esistente. Il file di script deve contenere un solo flusso di Windows PowerShell. Se il runbook dispone già di una versione bozza, l'importazione avrà esito negativo a meno che non si usi il parametro Overwrite. Dopo aver importato il runbook, è possibile pubblicarlo con il cmdlet [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx).

I comandi di esempio seguenti mostrano come importare un file di script in un runbook esistente e quindi pubblicarlo.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition –AutomationAccountName $automationAccountName –Name $runbookName –Path $ scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName


## Pubblicazione di un runbook

Quando si crea o si importa un nuovo runbook, è necessario pubblicarlo prima di poterlo eseguire. Ogni Runbook in Automazione di Azure include una versione bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre è possibile modificare solo la versione bozza. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Dopo aver reso disponibile la versione bozza, è possibile pubblicarla sovrascrivendo così la versione pubblicata.

## Per pubblicare un runbook usando il portale di Azure

1. Aprire il runbook nel portale di Azure.
1. Nella parte superiore della schermata fare clic su **Creazione**.
1. Nella parte inferiore della schermata fare clic su **Pubblica** e quindi **Sì** nel messaggio di verifica.

## Per pubblicare un runbook usando il portale di anteprima di Azure

1. Aprire il runbook nel portale di anteprima di Azure.
1. Fare clic sul pulsante **Modifica**.
1. Fare clic sul pulsante **Pubblica** e quindi su **Sì** nel messaggio di verifica.


## Per pubblicare un runbook usando Windows PowerShell

È possibile usare il cmdlet [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx) per pubblicare un runbook con Windows PowerShell. I comandi di esempio seguenti mostrano come pubblicare un runbook di esempio.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	
	Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName



## Articoli correlati

- [Raccolte di runbook e moduli per l'automazione di Azure](automation-runbook-gallery.md)
- [Modifica di runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md)
- [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)

<!---HONumber=Oct15_HO3-->