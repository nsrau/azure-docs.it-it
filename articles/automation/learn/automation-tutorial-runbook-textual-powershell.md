---
title: Creare un runbook PowerShell in Automazione di Azure
description: Questo articolo illustra come creare, testare e pubblicare un semplice runbook di PowerShell.
keywords: Azure PowerShell, esercitazione sugli script di PowerShell, automazione di PowerShell
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: a1b0dff9421f493958554c659043c49ff2874379
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015001"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Esercitazione: Creare un runbook di PowerShell

Questa esercitazione illustra la creazione di un [runbook PowerShell](../automation-runbook-types.md#powershell-runbooks) in Automazione di Azure. I runbook di PowerShell sono basati su Windows PowerShell. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure.

> [!div class="checklist"]
> * Creare un runbook PowerShell semplice
> * Testare e pubblicare il runbook
> * Eseguire il processo del runbook e monitorarne lo stato
> * Aggiornare il runbook per avviare una macchina virtuale di Azure con i parametri del runbook

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../automation-quickstart-create-account.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. Poiché la macchina virtuale viene arrestata e avviata, non deve essere una macchina virtuale di produzione.
* Se necessario, [importare i moduli di Azure](../shared-resources/modules.md) o [aggiornare i moduli](../automation-update-azure-modules.md) in base ai cmdlet usati.

## <a name="differences-from-powershell-workflow-runbooks"></a>Differenze rispetto ai runbook del flusso di lavoro PowerShell

I runbook PowerShell hanno lo stesso ciclo di vita, le stesse funzionalità e la stessa modalità di gestione dei runbook Flusso di lavoro PowerShell, ma con alcune differenze e limitazioni.

| Caratteristica  | Runbook PowerShell | Runbook del flusso di lavoro PowerShell |
| ------ | ----- | ----- |
| speed | L'esecuzione è veloce perché non usano un passaggio di compilazione. | L'esecuzione è più lenta. |
| Checkpoint | Non supportano i checkpoint. Un runbook PowerShell può solo riprendere un'operazione dall'inizio. | Usa i checkpoint che consentono a una cartella di lavoro di riprendere l'operazione da qualsiasi punto. |
| Esecuzione comando | Supporta solo l'esecuzione in serie. | Supporta l'esecuzione in serie e parallela.|
| Spazio di esecuzione | Uno spazio di esecuzione singolo esegue tutti gli elementi in uno script. | Un spazio di esecuzione separato può essere usato per un'attività, un comando o un blocco di script. |

Oltre a queste differenze, i runbook PowerShell presentano alcune [differenze sintattiche](/previous-versions/technet-magazine/dn151046(v=msdn.10)) rispetto ai runbook del flusso di lavoro PowerShell.

## <a name="step-1---create-runbook"></a>Passaggio 1: Creare un runbook

Iniziare creando un runbook semplice che restituisce il testo `Hello World`.

1. Nel portale di Azure aprire l'account di automazione.

2. Selezionare **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.

3. Per creare un nuovo runbook, selezionare **Crea un runbook**.

4. Denominare il runbook **MyFirstRunbook-PowerShell**.

5. In questo caso, verrà creato un [Runbook PowerShell](../automation-runbook-types.md#powershell-runbooks). In **Tipo di runbook** selezionare **PowerShell**.

6. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="step-2---add-code-to-the-runbook"></a>Passaggio 2: - aggiungere un codice al runbook

È possibile digitare il codice direttamente nel runbook, oppure è possibile selezionare i cmdlet, i runbook e le risorse dal controllo della libreria e aggiungerle al runbook con tutti i parametri correlati. Per questa esercitazione si digiterà il codice direttamente nel runbook.

1. Il runbook è attualmente vuoto. Digitare `Write-Output "Hello World"` nel corpo dello script.

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Passaggio 3: Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione Bozza e visualizzarne l'output in modo interattivo.

1. Fare clic su **Pannello di test** per aprire il pannello di test.

2. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.

3. Si noti che viene creato un [processo del runbook](../automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.

   Lo stato iniziale del processo è In coda, per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passa ad Avvio quando un ruolo di lavoro richiede il processo. Infine, lo stato diventa In esecuzione quando viene avviata l'esecuzione effettiva del runbook.

4. Al termine del processo del runbook, viene visualizzato l'output nel riquadro di test. In questo caso, verrà visualizzato l'output `Hello World`.

   ![Output del riquadro di test](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.

2. Scorrere verso sinistra per visualizzare il runbook nella pagina Runbook e notare che il valore di **Stato creazione** è impostato su **Pubblicato**.

3. Scorrere verso destra per visualizzare la pagina **MyFirstRunbook-PowerShell**.
   
   Le opzioni nella parte superiore consentono di avviare il runbook adesso, pianificarlo per un'ora di inizo in futuro o creare un [webhook](../automation-webhooks.md) per poterlo avviare con una chiamata HTTP.

4. Selezionare **Avvia** e poi **Sì** quando richiesto per avviare il runbook. 

5. Verrà aperto un riquadro per il processo del runbook creato. È possibile chiudere questo riquadro, ma in questo caso lo si lascerà aperto per poter controllare lo stato del processo. Lo stato del processo viene visualizzato in **Riepilogo processi** e gli stati possibili corrispondono a quelli descritti per il test del runbook.

   ![Riepilogo dei processi](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Quando lo stato del runbook passa a Completato, fare clic su **Output** per aprire la pagina Output, in cui è possibile visualizzare `Hello World`.

   ![Output del processo](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Chiudere la pagina Output.

8. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output sarà visibile solo `Hello World`.

    Si noti che nel riquadro Flussi potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Dettagliato ed Errore, se il runbook esegue la scrittura in questi flussi.

   ![Tutti i log](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Chiudere il riquadro Flussi e il riquadro Processi per tornare alla pagina MyFirstRunbook-PowerShell.

10. In **Dettagli** fare clic su **Processi** per aprire la pagina dei processi per questo runbook. In questa pagina sono elencati tutti i processi creati dal runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.

   ![Elenco dei processi](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Fare clic sul nome del processo per aprire lo stesso riquadro Processo visualizzato quando è stato avviato il runbook. Utilizzare questo riquadro per visualizzare i dettagli di qualsiasi processo creato per il runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. A tale scopo, il runbook deve eseguire l'autenticazione usando l'account RunAs creato automaticamente durante la creazione dell'account di Automazione.

Come illustrato nell'esempio seguente, la connessione RunAs viene stabilita con il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Se si gestiscono risorse in più sottoscrizioni, è necessario usare il parametro `AzContext` con il cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Per i runbook PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per `Connect-AzAccount`. È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](../automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli, anche se è stato appena creato un nuovo account di Automazione.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
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

2. La riga `Write-Output` non è più necessaria ed è possibile eliminarla.

3. Digitare o copiare e incollare il codice seguente che gestisce l'autenticazione con l'account RunAs di Automazione.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. Fare clic su **Riquadro di test** in modo da testare il runbook.

5. Fare clic su **Avvia** per avviare il test. Al termine verrà visualizzato un output simile al seguente, con le informazioni di base dell'account. Questo output conferma che l'account RunAs è valido.

   ![Authenticate](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passaggio 6 - aggiungere il codice per avviare una macchina virtuale

Ora che il runbook esegue l'autenticazione per la sottoscrizione di Azure è possibile gestire le risorse. Verrà aggiunto un comando per avviare una macchina virtuale. È possibile selezionare una macchina virtuale qualsiasi nella sottoscrizione di Azure. Per ora il nome sarà hardcoded nel runbook.

1. Per avviare la macchina virtuale, aggiungere il cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) allo script del runbook. Come illustrato di seguito, il cmdlet avvia una macchina virtuale con il nome `VMName` e con un gruppo di risorse denominato `ResourceGroupName`.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Salvare il runbook e poi fare clic su **Riquadro di test** in modo da poterne eseguire il test.

3. Fare clic su **Avvia** per avviare il test. Al termine, assicurarsi che la macchina virtuale sia stata avviata.

## <a name="step-7---add-an-input-parameter"></a>Passaggio 7: Aggiungere un parametro di input

Il runbook avvia effettivamente la macchina virtuale hardcoded nel runbook. Il runbook è più utile se si specifica la macchina virtuale all'avvio del runbook. Per fornire questa funzionalità, occorre aggiungere parametri di input al runbook.

1. Nell'editor di testo modificare il cmdlet `Start-AzVM` affinché usi le variabili per i parametri `VMName` e `ResourceGroupName`. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Salvare il runbook e aprire il riquadro Test. Ora è possibile fornire i valori per le due variabili di input che verranno usate nel test.

3. Chiudere il riquadro Test.

4. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.

5. Arrestare la macchina virtuale avviata in precedenza.

6. Fare clic su **Avvia** per avviare il runbook. 

7. Digitare i valori per **VMName** e **ResourceGroupName** per la macchina virtuale da avviare e quindi fare clic su **OK**.

    ![Passare i parametri](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Al termine del runbook, verificare che la macchina virtuale sia stata avviata.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su PowerShell, incluse le informazioni di riferimento sul linguaggio e i moduli di apprendimento, vedere la [documentazione di PowerShell](/powershell/scripting/overview).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
* Per iniziare a usare runbook grafici, vedere [Creare un runbook grafico](automation-tutorial-runbook-graphical.md).
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Creare un runbook del flusso di lavoro PowerShell](automation-tutorial-runbook-textual.md).
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](../automation-runbook-types.md).
* Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere il blog relativo al [supporto di script PowerShell nativi in Automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
