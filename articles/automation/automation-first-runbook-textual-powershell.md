---
title: Il primo runbook PowerShell in Automazione di Azure
description: Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook PowerShell.
keywords: Azure PowerShell, esercitazione sugli script di PowerShell, automazione di PowerShell
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: b9808ddc3b61b0055642c5a0f2a82b0dc7553b33
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384849"
---
# <a name="my-first-powershell-runbook"></a>Il primo runbook PowerShell

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Questa esercitazione illustra la creazione di un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) in Automazione di Azure. Iniziare con un Runbook semplice che è possibile testare e pubblicare, mentre si apprende come tenere traccia dello stato del processo del Runbook. Modificare quindi il Runbook per gestire effettivamente le risorse di Azure, in questo caso avviando una macchina virtuale di Azure. Completare l'esercitazione per rendere il Runbook più affidabile aggiungendo i parametri di Runbook.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prerequisites

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-quickstart-create-account.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. Poiché si arresta e si avvia questo computer, non dovrebbe essere una macchina virtuale di produzione.
* Se necessario, [importare](shared-resources/modules.md) i moduli di Azure o [aggiornare i moduli](automation-update-azure-modules.md) in base ai cmdlet usati.

## <a name="differences-from-powershell-workflow-runbooks"></a>Differenze rispetto a manuali operativi del flusso di lavoro PowerShell

Manuali operativi di PowerShell hanno lo stesso ciclo di vita, funzionalità e gestione del flusso di lavoro PowerShell manuali operativi. Tuttavia, esistono alcune differenze e limitazioni.

* PowerShell manuali operativi viene eseguito in modo rapido rispetto ai manuali operativi del flusso di lavoro PowerShell perché non usano un passaggio di compilazione.
* Le cartelle di lavoro di PowerShell non supportano i checkpoint, usati nei manuali operativi del flusso di lavoro PowerShell. Usando i checkpoint, i manuali operativi del flusso di lavoro PowerShell possono riprendere le operazioni da qualsiasi punto. Il Runbook di PowerShell può riprendere solo l'operazione dall'inizio.
* I runbook di PowerShell possono solo eseguire i comandi in modo seriale. Manuali operativi del flusso di lavoro PowerShell supportano sia l'esecuzione seriale che quella parallela.
* Per un Runbook di PowerShell, tutti gli elementi in uno script vengono eseguiti in un singolo spazio. In un runbook del flusso di lavoro PowerShell, un'attività, un comando o un blocco di script possono avere un proprio spazio di esecuzione. 
* Manuali operativi di PowerShell presentano alcune [differenze sintattiche](https://technet.microsoft.com/magazine/dn151046.aspx) rispetto a manuali operativi del flusso di lavoro PowerShell.

## <a name="step-1---create-runbook"></a>Passaggio 1: Creare un runbook

Iniziare creando un runbook semplice che restituisce il testo **Hello World**.

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **manuali operativi** in **automazione processi** per aprire l'elenco di manuali operativi.
3. Creare un nuovo Runbook selezionando **Crea una Runbook**.
4. Denominare il runbook **MyFirstRunbook-PowerShell**.
5. In questo caso verrà creato un [Runbook di PowerShell](automation-runbook-types.md#powershell-runbooks). Selezionare **PowerShell** per **tipo di Runbook**.
6. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="step-2---add-code-to-the-runbook"></a>Passaggio 2: - aggiungere un codice al runbook

È possibile digitare il codice direttamente nel runbook, oppure è possibile selezionare i cmdlet, i runbook e le risorse dal controllo della libreria e aggiungerle al runbook con tutti i parametri correlati. Per questa esercitazione si digitarà il codice direttamente nel runbook.

1. Il Runbook è attualmente vuoto. Digitare `Write-Output "Hello World"` nel corpo dello script.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a> Passaggio 3: testare il Runbook

Prima di pubblicare il Runbook per renderlo disponibile nell'ambiente di produzione, è necessario testarlo per verificare che funzioni correttamente. Il test di un Runbook esegue la versione bozza e consente di visualizzarne l'output in modo interattivo.

1. Fare clic su **Pannello di test** per aprire il pannello di test.
2. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
3. Si noti che viene creato un [processo del Runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.

   Lo stato del processo viene avviato come **accodato**, a indicare che il processo è in attesa che un Runbook Worker nel cloud diventi disponibile. Lo stato passa a **avvio** quando un thread di lavoro dichiara il processo. Infine, lo stato viene **eseguito** quando il Runbook inizia effettivamente a essere eseguito.

4. Al termine del processo del Runbook, nel riquadro test viene visualizzato l'output. In questo caso viene visualizzato **Hello World**.

   ![Output del riquadro di test](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il Runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.
1. Scorrere verso sinistra per visualizzare il Runbook nella pagina manuali operativi e notare che il valore **dello stato di creazione** è impostato su **pubblicato**.
1. Scorrere verso destra per visualizzare il riquadro **MyFirstRunbook-PowerShell**.
   
   Le opzioni nella parte superiore consentono di avviare il Runbook ora, pianificare un'ora di avvio futura o creare un [webhook](automation-webhooks.md) in modo che Runbook possa essere avviato tramite una chiamata http.
1. Selezionare **Avvia** e poi **Sì** quando richiesto per avviare il runbook. 
1. Viene aperto un riquadro del processo per il processo Runbook creato. Sebbene sia possibile chiudere questo riquadro, lasciarlo aperto per poter controllare lo stato di avanzamento del processo. Lo stato del processo è visualizzato in **Riepilogo processi**e gli stati possibili sono descritti per il test di Runbook.

   ![Riepilogo dei processi](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Una volta visualizzato lo stato **Runbook, fare**clic su **output** per aprire la pagina output, in cui è possibile visualizzare **Hello World** .

   ![Output del processo](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Chiudere la pagina Output.
1. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output dovrebbe essere visibile solo **Hello World** .

    Si noti che nel riquadro flussi possono essere visualizzati altri flussi per un processo Runbook, ad esempio flussi dettagliati e di errore, se il Runbook scrive in essi.

   ![Tutti i log](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Chiudere il riquadro dei flussi e il riquadro dei processi per tornare alla pagina **MyFirstRunbook-PowerShell** .
1. In **Dettagli**fare clic su **processi** per aprire la pagina processi per questo runbook. Questa pagina elenca tutti i processi creati dal Runbook. Dovrebbe essere visualizzato un solo processo, perché il processo è stato eseguito una sola volta.

   ![Elenco dei processi](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Fare clic sul nome del processo per aprire lo stesso riquadro del processo visualizzato quando è stato avviato il Runbook. Utilizzare questo riquadro per visualizzare i dettagli di qualsiasi processo creato per il Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. A tale scopo, il Runbook deve essere in grado di eseguire l'autenticazione usando l'account RunAs creato automaticamente durante la creazione dell'account di automazione.

Come illustrato nell'esempio seguente, la connessione RunAs viene eseguita con il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) . Se si gestiscono risorse tra più sottoscrizioni, è necessario usare il parametro *AzContext* insieme a [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Per PowerShell manuali operativi, **Add-AzAccount** e **Add-AzureRMAccount** sono alias per **Connect-AzAccount**. È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](automation-update-azure-modules.md) nell'account di automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli anche se è stato appena creato un nuovo account di automazione.

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
1. Aprire l'editor di testo facendo clic su **modifica** nella pagina **MyFirstRunbook-PowerShell** .
1. La riga **Write-Output** non è più necessaria. Basta procedere ed eliminarlo.
1. Digitare o copiare e incollare il codice seguente che gestisce l'autenticazione con l'account RunAs di automazione.

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

1. Fare clic su **Riquadro di test** in modo da testare il runbook.
1. Fare clic su **Avvia** per avviare il test. Al termine, verrà visualizzato un output simile al seguente, in cui vengono visualizzate le informazioni di base del proprio account. Questo output conferma che l'account RunAs è valido.

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passaggio 6 - aggiungere il codice per avviare una macchina virtuale

Ora che il runbook esegue l'autenticazione per la sottoscrizione di Azure è possibile gestire le risorse. È ora possibile aggiungere un comando per avviare una macchina virtuale. È possibile selezionare qualsiasi macchina virtuale nella sottoscrizione di Azure e solo impostare come hardcoded tale nome in Runbook per il momento.

1. Per avviare la macchina virtuale, aggiungere il cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) per lo script Runbook. Come illustrato di seguito, il cmdlet avvia una macchina virtuale con il nome **VMName** e un gruppo di risorse denominato **ResourceGroupName**.

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

## <a name="step-7---add-an-input-parameter"></a>Passaggio 7: aggiungere un parametro di input

Il Runbook avvia attualmente la macchina virtuale che è stata codificata in Runbook. Il Runbook sarà più utile se si specifica la macchina virtuale all'avvio del Runbook. Aggiungere i parametri di input al Runbook per fornire tale funzionalità.

1. Nell'editor di testo modificare il cmdlet **Start-AzVM** per usare le variabili per i parametri *VMName* e *ResourceGroupName*. 

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
1. Digitare i valori per **VMName** e **RESOURCEGROUPNAME** per la macchina virtuale che si intende avviare, quindi fare clic su **OK**.<br><br> ![Passare i parametri](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Al termine del Runbook, verificare che la macchina virtuale sia stata avviata.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su PowerShell, inclusi i riferimenti al linguaggio e i moduli di formazione, vedere la [documentazione di PowerShell](/powershell/scripting/overview).
* Per iniziare a usare manuali operativi grafici, vedere [il primo Runbook grafico](automation-first-runbook-graphical.md).
* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per altre informazioni sui tipi di Runbook e i relativi vantaggi e limitazioni, vedere [tipi di Runbook di automazione di Azure](automation-runbook-types.md).
* Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere [supporto degli script PowerShell nativi in automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
