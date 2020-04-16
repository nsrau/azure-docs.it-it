---
title: Il primo runbook PowerShell in Automazione di Azure
description: Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook PowerShell.
keywords: Azure PowerShell, esercitazione sugli script di PowerShell, automazione di PowerShell
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0df2680a9f17fc0af950b0ce744a655348b4cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406072"
---
# <a name="my-first-powershell-runbook"></a>Il primo runbook PowerShell

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Questa esercitazione illustra la creazione di un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) in Automazione di Azure. Inizia con un semplice runbook che puoi testare e pubblicare, imparando a tenere traccia dello stato del processo del runbook. Modificare quindi il runbook per gestire effettivamente le risorse di Azure, in questo caso l'avvio di una macchina virtuale di Azure.Then modify the runbook to actually manage Azure resources, in this case starting an Azure virtual machine. Completare l'esercitazione per rendere il runbook più affidabile aggiungendo parametri del runbook.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-quickstart-create-account.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. Poiché si arresta e si avvia questa macchina, non dovrebbe essere una macchina virtuale di produzione.
* Se necessario, importare i moduli di [Azure](shared-resources/modules.md) o [aggiornare](automation-update-azure-modules.md) i moduli in base ai cmdlet utilizzati.

## <a name="differences-from-powershell-workflow-runbooks"></a>Differenze dai runbook del flusso di lavoro di PowerShellDifferences from PowerShell Workflow runbooks

I runbook di PowerShell hanno lo stesso ciclo di vita, le stesse funzionalità e la stessa gestione dei runbook del flusso di lavoro di PowerShell.PowerShell runbooks have the same lifecycle, capabilities, and management as PowerShell Workflow runbooks. Tuttavia, ci sono alcune differenze e limitazioni.

| Caratteristica  | Runbook di PowerShell | Runbook del flusso di lavoro di PowerShellPowerShell Workflow Runbooks |
| ------ | ----- | ----- |
| speed | Eseguire rapidamente in quanto non usano un passaggio di compilazione. | Eseguire più lentamente. |
| Checkpoint | Non supporta i checkpoint. Un runbook di PowerShell può riprendere l'operazione solo dall'inizio. | Utilizzare i checkpoint, che consentono a una cartella di lavoro di riprendere l'operazione da qualsiasi punto. |
| Esecuzione dei comandi | Supporta solo l'esecuzione seriale. | Supporta l'esecuzione sia seriale che parallela.|
| Spazio di esecuzione | Un singolo runspace esegue tutto in uno script. | Uno spazio di esecuzione separato può essere utilizzato per un'attività, un comando o un blocco di script. |

Oltre a queste differenze, i runbook di PowerShell presentano alcune [differenze sintattiche](https://technet.microsoft.com/magazine/dn151046.aspx) rispetto ai runbook del flusso di lavoro di PowerShell.In addition to these differences, PowerShell runbooks have some syntactic differences from PowerShell Workflow runbooks.

## <a name="step-1---create-runbook"></a>Passaggio 1: Creare un runbook

Iniziare creando un runbook semplice che `Hello World`restituisce il testo .

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Runbooks** in **Process Automation** per aprire l'elenco dei runbook.
3. Creare un nuovo runbook selezionando **Create a runbook**.
4. Denominare il runbook **MyFirstRunbook-PowerShell**.
5. In questo caso, si creerà un runbook di [PowerShell](automation-runbook-types.md#powershell-runbooks). Selezionare **PowerShell** per **tipo di Runbook**.
6. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="step-2---add-code-to-the-runbook"></a>Passaggio 2: - aggiungere un codice al runbook

È possibile digitare il codice direttamente nel runbook, oppure è possibile selezionare i cmdlet, i runbook e le risorse dal controllo della libreria e aggiungerle al runbook con tutti i parametri correlati. Per questa esercitazione, si intende digitare il codice direttamente nel runbook.

1. Il runbook è attualmente vuoto. Digitare `Write-Output "Hello World"` il corpo dello script.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Passaggio 3 - Testare il runbookStep 3 - Test the runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, è consigliabile testarlo per assicurarsi che funzioni correttamente. Il test di un runbook esegue la versione Bozza e consente di visualizzarne l'output in modo interattivo.

1. Fare clic su **Pannello di test** per aprire il pannello di test.
2. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
3. Si noti che viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.

   Lo stato del processo inizia come In coda, a indicare che il processo è in attesa della disponibilità di un ruolo di lavoro del runbook nel cloud. Lo stato diventa Inizio quando un lavoratore rivendica il processo. Infine, lo stato diventa In esecuzione quando inizia l'esecuzione del runbook.

4. Al termine del processo del runbook, nel riquadro Test viene visualizzato l'output. In questo caso, `Hello World`viene visualizzato .

   ![Output del riquadro di test](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità Bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.
1. Scorrere verso sinistra per visualizzare il runbook nella pagina Runbooks e notare che il valore **Stato creazione** creazione è impostato su **Pubblicato**.
1. Scorrere verso destra per visualizzare la pagina di **MyFirstRunbook-PowerShell**.
   
   Le opzioni nella parte superiore consentono di avviare il runbook ora, pianificare un'ora di inizio futura o creare un [webhook](automation-webhooks.md) in modo che il runbook possa essere avviato tramite una chiamata HTTP.
1. Selezionare **Avvia** e poi **Sì** quando richiesto per avviare il runbook. 
1. Viene aperto un riquadro Processo per il processo runbook creato. Sebbene sia possibile chiudere questo riquadro, lasciarlo aperto in questo momento in modo da poter controllare lo stato di avanzamento del processo. Lo stato del processo viene visualizzato in **Riepilogo processo**e i possibili stati sono come descritto per il test del runbook.

   ![Riepilogo dei processi](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Quando lo stato del runbook è Completato, fare clic `Hello World` su **Output** per aprire la pagina Output, in cui è possibile visualizzare.

   ![Output del processo](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Chiudere la pagina Output.
1. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Si dovrebbe `Hello World` vedere solo nel flusso di output.

    Si noti che il riquadro Flussi può mostrare altri flussi per un processo del runbook, ad esempio flussi Verbose ed Error, se il runbook scrive in essi.

   ![Tutti i log](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Chiudere il riquadro Flussi e il riquadro Processo per tornare alla pagina MyFirstRunbook-PowerShell.
1. In **Dettagli**fare clic su **Processi** per aprire la pagina Processi per questo runbook. Questa pagina elenca tutti i processi creati dal runbook. Verrà visualizzato un solo processo nell'elenco, poiché il processo è stato eseguito una sola volta.

   ![Elenco dei processi](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Fare clic sul nome del processo per aprire lo stesso riquadro Processo visualizzato all'avvio del runbook. Utilizzare questo riquadro per visualizzare i dettagli di qualsiasi processo creato per il runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. A tale scopo, il runbook deve essere in grado di eseguire l'autenticazione utilizzando l'account RunAs creato automaticamente al momento della creazione dell'account di Automazione.

Come illustrato nell'esempio seguente, la connessione RunAs viene effettuata con il cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Se si gestiscono risorse tra più sottoscrizioni, `AzContext` è necessario utilizzare il parametro con [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Per i runbook `Add-AzAccount` `Add-AzureRMAccount` di PowerShell `Connect-AzAccount`e sono alias per . È possibile utilizzare questi cmdlet [oppure aggiornare i moduli](automation-update-azure-modules.md) nell'account di automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli anche se è stato appena creato un nuovo account di automazione.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. Aprire l'editor di testo facendo clic su **Modifica** nella pagina MyFirstRunbook-PowerShell.
1. Non hai più `Write-Output` bisogno della linea. Basta andare avanti ed eliminarlo.
1. Digitare o copiare e incollare il codice seguente, che gestisce l'autenticazione con l'account RunAs di automazione.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Fare clic sul **riquadro Test** per testare il runbook.
1. Fare clic su **Avvia** per avviare il test. Una volta completato, dovresti vedere un output simile al seguente, che visualizza le informazioni di base del tuo account. Questo output conferma che l'account RunAs è valido.

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passaggio 6 - aggiungere il codice per avviare una macchina virtuale

Ora che il runbook esegue l'autenticazione per la sottoscrizione di Azure è possibile gestire le risorse. Aggiungiamo un comando per avviare una macchina virtuale. È possibile selezionare qualsiasi macchina virtuale nella sottoscrizione di Azure e per il momento è sufficiente il nome nel runbook.

1. Allo script runbook aggiungere il cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) per avviare la macchina virtuale. Come illustrato di seguito, il cmdlet `VMName` avvia una macchina `ResourceGroupName`virtuale con il nome e con un gruppo di risorse denominato .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Salvare il runbook e poi fare clic su **Riquadro di test** in modo da poterne eseguire il test.
1. Fare clic su **Avvia** per avviare il test. Al termine, assicurarsi che la macchina virtuale sia stata avviata.

## <a name="step-7---add-an-input-parameter"></a>Passaggio 7 - Aggiungere un parametro di inputStep 7 - Add an input parameter

Il runbook avvia attualmente la macchina virtuale hardcoded nel runbook. Il runbook è più utile se si specifica la macchina virtuale all'avvio del runbook. Aggiungiamo parametri di input al runbook per fornire tale funzionalità.

1. Nell'editor di testo modificare `Start-AzVM` il cmdlet per `VMName` utilizzare `ResourceGroupName`le variabili per i parametri e . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Salvare il runbook e aprire il riquadro Test. Ora è possibile fornire i valori per le due variabili di input che verranno usate nel test.
1. Chiudere il riquadro Test.
1. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
1. Arrestare la macchina virtuale avviata in precedenza.
1. Fare clic su **Avvia** per avviare il runbook. 
1. Digitare i valori per **VMNAME** e **RESOURCEGROUPNAME** per la macchina virtuale che si sta per avviare e quindi fare clic su **OK**.<br><br> ![Passare i parametri](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Al termine del runbook, verificare che la macchina virtuale sia stata avviata.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la documentazione di [PowerShell](/powershell/scripting/overview).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
* Per iniziare a utilizzare i runbook grafici, vedere [Il mio primo runbook grafico](automation-first-runbook-graphical.md).
* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per altre informazioni sui tipi di runbook e sui relativi vantaggi e limitazioni, vedere [Tipi di runbook di Automazione di Azure.](automation-runbook-types.md)
* Per altre informazioni sulla funzionalità di supporto degli script di PowerShell, vedere Supporto di [script di PowerShell nativi in Automazione di Azure.For](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)more information on the PowerShell script support feature, see Native PowerShell script support in Azure Automation .
