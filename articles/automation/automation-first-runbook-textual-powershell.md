---
title: Il primo runbook PowerShell in Automazione di Azure
description: Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook PowerShell.
keywords: Azure PowerShell, esercitazione sugli script di PowerShell, automazione di PowerShell
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b08e1489cf337360e838a3b5d5531fa2d4c0073b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694261"
---
# <a name="my-first-powershell-runbook"></a>Il primo runbook PowerShell

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Questa esercitazione illustra la creazione di un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) in Automazione di Azure. Si inizia con un runbook semplice che viene testato e pubblicato, quindi viene illustrato come tenere traccia dello stato del processo del runbook. Si modifica quindi il runbook per gestire effettivamente le risorse di Azure, avviando in questo caso una macchina virtuale di Azure. Si rende infine il runbook più affidabile aggiungendo i relativi parametri.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-quickstart-create-account.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. La macchina virtuale viene arrestata e avviata, quindi non deve essere una macchina virtuale di produzione.

## <a name="create-new-runbook"></a>Creare un nuovo runbook

Si inizia creando un runbook semplice che restituisce il testo *Hello World*.

1. Nel portale di Azure aprire l'account di automazione.
2. Fare clic su **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.
3. Creare un nuovo runbook facendo clic sul pulsante **+ Aggiungi runbook** e quindi su **Crea un nuovo runbook**.
4. Denominare il runbook *MyFirstRunbook-PowerShell*.
5. In questo caso si sta creando un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks), quindi selezionare **Powershell** per **Tipo di runbook**.
6. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="add-code-to-the-runbook"></a>Aggiungere un codice al runbook

È possibile digitare il codice direttamente nel runbook, oppure è possibile selezionare i cmdlet, i runbook e le risorse dal controllo della libreria e aggiungerle al runbook con tutti i parametri correlati. Per questa procedura dettagliata si digiterà direttamente nel runbook.

1. Il runbook è attualmente vuoto; digitare *Write-Output "Hello World."* nel corpo dello script.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  

2. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"> </a> Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione **Bozza** e visualizzarne l'output in modo interattivo.

1. Fare clic su **Pannello di test** per aprire il pannello di test.
2. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
3. Viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato.

   Lo stato del processo sarà inizialmente *In coda* a indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passa ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  

4. Al termine del processo del runbook, viene visualizzato l'output. In questo caso dovrebbe essere visualizzato *Hello World*.

   ![Output del riquadro di test](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  

5. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="publish-and-start-the-runbook"></a>Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione.  Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.
1. Se si scorre verso sinistra per visualizzare il runbook nella pagina **Runbook**, come **Stato di creazione** viene visualizzato **Pubblicato**.
1. Scorrere verso destra per visualizzare il riquadro **MyFirstRunbook-PowerShell**.  
   Le opzioni nella parte superiore consentono di avviare il runbook, visualizzarlo, pianificarlo per l'avvio in un momento successivo o creare un [webhook](automation-webhooks.md) per poterlo avviare con una chiamata HTTP.
1. Per avviare il runbook, fare clic su **Avvia** e quindi su **OK** quando si apre la pagina Avvia runbook.
1. Verrà aperta una pagina per il processo del runbook creato. È possibile chiudere questo riquadro, ma in questo caso lo si lascerà aperto per poter controllare lo stato del processo.
1. Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook.

   ![Riepilogo dei processi](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Quando lo stato del runbook è *Completato*, in **Overview** fare clic su **Output**. Viene aperto il riquadro Output dove si può vedere il testo *Hello World*.

   ![Output del processo](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Chiudere la pagina Output.
1. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output dovrebbe essere visibile solo *Hello World*, ma potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Verbose ed Error se il runbook scrive in questi flussi.

   ![Tutti i log](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Chiudere la finestra Flussi e la pagina dei processi per tornare alla pagina MyFirstRunbook-PowerShell.
1. In **Dettagli** fare clic su **Processi** per aprire la pagina dei processi per questo runbook. In questa pagina sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.

   ![Elenco dei processi](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  

1. È possibile fare clic su questo processo per aprire lo stesso pannello visualizzato quando è stato avviato il runbook. Questa azione consente di tornare indietro nel tempo e visualizzare i dettagli di tutti i processi creati per un runbook particolare.

## <a name="add-authentication-to-manage-azure-resources"></a>Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. Non è in grado di eseguire questa operazione a meno che non venga autenticato con una connessione RunAs che viene creata automaticamente quando si crea l'account di Automazione. La connessione RunAs viene usata con il cmdlet **Connect-AzureRmAccount**. Se si gestiscono risorse in più sottoscrizioni, è necessario usare il parametro **-AzureRmContext** con [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process
   
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Aprire l'editor di testo facendo clic su **Modifica** nella pagina MyFirstRunbook-PowerShell.
1. Non è più necessaria la riga **Write-Output**, quindi andare avanti ed eliminarla.
1. Digitare o copiare e incollare il codice seguente che gestisce l'autenticazione con l'account RunAs di Automazione:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** e **Login-AzureRmAccount** sono ora alias per **Connect-AzureRMAccount**. Se il cmdlet **Connect-AzureRMAccount**  non esiste, è possibile usare **Add-AzureRmAccount**  o **Login-AzureRmAccount** oppure aggiornare i moduli nell'account di Automazione alle versioni più recenti.

1. Fare clic su **Riquadro di test** in modo da testare il runbook.
1. Fare clic su **Avvia** per avviare il test. Al termine verrà visualizzato un output simile al seguente, con le informazioni di base sull'account. Questo output conferma che l'account RunAs è valido.

   ![Autentica](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Aggiungere il codice per avviare una macchina virtuale

Ora che il runbook esegue l'autenticazione per la sottoscrizione di Azure è possibile gestire le risorse. Si aggiunge un comando per avviare una macchina virtuale. È possibile selezionare una macchina virtuale qualsiasi nella sottoscrizione di Azure. Per ora il nome sarà hardcoded nel runbook.

1. Dopo *Connect-AzureRmAccount* digitare *Start-AzureRmVM -Name 'NomeVM' -ResourceGroupName 'NomeGruppoDiRisorse'* specificando il nome e il nome del gruppo di risorse della macchina virtuale da avviare.  

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Salvare il runbook e poi fare clic su **Riquadro di test** in modo da poterne eseguire il test.
1. Fare clic su **Avvia** per avviare il test. Dopo aver completato l'attività, controllare che la macchina virtuale sia stata avviata.

## <a name="add-an-input-parameter"></a>Aggiungere un parametro di input

Ora il runbook avvia la macchina virtuale specificata nel runbook, ma sarebbe più utile se si potesse specificare la macchina virtuale quando si avvia il runbook. Per fornire questa funzionalità, si aggiungeranno dei parametri di input al runbook.

1. Aggiungere parametri per *VMName* e *ResourceGroupName* al runbook e usare queste variabili con il cmdlet **Start-AzureRmVM** come nell'esempio seguente.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Salvare il runbook e aprire il riquadro Test. È ora possibile fornire i valori per le due variabili di input usate nel test.
1. Chiudere il riquadro Test.
1. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
1. Arrestare la macchina virtuale avviata nel passaggio precedente.
1. Fare clic su **OK** per avviare il runbook. Digitare **VMName** e **ResourceGroupName** per la macchina virtuale da avviare.<br><br> ![Passare i parametri](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
1. Quando il runbook viene completato, controllare che la macchina virtuale sia stata avviata.

## <a name="differences-from-powershell-workflow"></a>Differenze rispetto al flusso di lavoro PowerShell

I runbook PowerShell hanno lo stesso ciclo di vita, le stesse funzionalità e la stessa modalità di gestione dei runbook Flusso di lavoro PowerShell, ma con alcune differenze e limitazioni:

1. L'esecuzione dei runbook PowerShell è più rapida rispetto ai runbook Flusso di lavoro PowerShell, perché non prevedono un passaggio di compilazione.
2. I runbook del flusso di lavoro PowerShell supportano i checkpoint. L'uso dei checkpoint consente ai runbook del flusso di lavoro PowerShell di riprendere da qualsiasi punto del runbook. I runbook di PowerShell possono riprendere solo dall'inizio.
3. I runbook del flusso di lavoro PowerShell supportano l'esecuzione parallela e seriale. I runbook di PowerShell possono solo eseguire i comandi in modo seriale.
4. In un runbook del flusso di lavoro PowerShell, un'attività, un comando o un blocco di script possono avere un proprio spazio di esecuzione. In un runbook di PowerShell tutti gli elementi in uno script vengono eseguiti in un singolo spazio di esecuzione. Esistono anche [differenze di sintassi](https://technet.microsoft.com/magazine/dn151046.aspx) tra un runbook PowerShell nativo e un runbook Flusso di lavoro PowerShell.

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md)
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
* Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere il blog relativo al [supporto di script PowerShell nativi in Automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
