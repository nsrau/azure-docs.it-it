---
title: Il primo runbook del flusso di lavoro di PowerShell in Automazione di Azure
description: Esercitazione in cui viene illustrata la creazione, il test e la pubblicazione di un semplice runbook testuale con flusso di lavoro PowerShell.
keywords: PowerShell flusso di lavoro, esempi di flusso di lavoro PowerShell, flusso di lavoro PowerShell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: 8b103437ab30c05ddab88b7a8a723cd2b4b1d5f6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405897"
---
# <a name="my-first-powershell-workflow-runbook"></a>Il primo runbook del flusso di lavoro PowerShell

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Questa esercitazione illustra la creazione di un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) in Automazione di Azure. Iniziare con un semplice runbook che si esegue il test e la pubblicazione imparando a tenere traccia dello stato del processo del runbook. Modificare quindi il runbook per gestire effettivamente le risorse di Azure, illustrato avviando una macchina virtuale di Azure.Then modify the runbook to actually manage Azure resources, illustrated by starting an Azure virtual machine. Infine, rendere il runbook più affidabile con l'aggiunta di parametri del runbook.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. Poiché si arresta e si avvia questa macchina, non dovrebbe essere una macchina virtuale di produzione.

## <a name="step-1---create-new-runbook"></a>Passaggio 1: Creare nuovo runbook

Iniziare creando un runbook semplice che `Hello World`restituisce il testo .

1. Nel portale di Azure aprire l'account di automazione.

   La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. La maggior parte di queste risorse sono i moduli inclusi automaticamente in un nuovo account di automazione. È inoltre necessario disporre della risorsa Credenziali associata alla sottoscrizione.
 
1. Selezionare **Runbooks** in **Process Automation** per aprire l'elenco dei runbook.
1. Creare un nuovo runbook selezionando **Create a runbook**.
1. Denominare il runbook **MyFirstRunbook-Workflow**.
1. In questo caso, si creerà un [runbook del flusso](automation-runbook-types.md#powershell-workflow-runbooks)di lavoro di PowerShell . Selezionare **Flusso di lavoro di PowerShell** per Tipo di **runbook**.
1. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="step-2---add-code-to-the-runbook"></a>Passaggio 2: - aggiungere un codice al runbook

È possibile digitare il codice direttamente nel runbook oppure selezionare cmdlet, runbook e risorse dal controllo Libreria e aggiungerli al runbook con eventuali parametri correlati. Per questa esercitazione, si digita il codice direttamente nel runbook.

1. Il runbook è attualmente vuoto `Workflow` con solo la parola chiave obbligatoria, il nome del runbook e le parentesi graffe che racchiudeno l'intero flusso di lavoro.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Digitare `Write-Output "Hello World"` tra le parentesi graffe.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a>Passaggio 3: Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, è consigliabile testarlo per assicurarsi che funzioni correttamente. Il test di un runbook esegue la versione Bozza e consente di visualizzarne l'output in modo interattivo.

1. Selezionare il **riquadro Test** per aprire il riquadro Test.Select Test pane to open the Test pane.
1. Fare clic su **Avvia** per avviare il test, testando l'unica opzione abilitata.
1. Si noti che viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.

   Lo stato del processo inizia come In coda, a indicare che il processo è in attesa della disponibilità di un ruolo di lavoro del runbook nel cloud. Lo stato diventa Inizio quando un lavoratore rivendica il processo. Infine, lo stato diventa In esecuzione quando inizia l'esecuzione del runbook.

1. Al termine del processo del runbook, nel riquadro Test viene visualizzato l'output. In questo caso, `Hello World`viene visualizzato .

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il runbook che hai creato è ancora in modalità Bozza. È necessario pubblicarlo prima di poterlo eseguire nell'ambiente di produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.
1. Scorrere verso sinistra per visualizzare il runbook nella pagina **Runbooks** e notare che il campo **Stato creazione** creazione è impostato su **Pubblicato**.
1. Scorrere verso destra per visualizzare la pagina per **MyFirstRunbook-Workflow**.

   Le opzioni nella parte superiore consentono di avviare il runbook ora, pianificare un'ora di inizio futura o creare un [webhook](automation-webhooks.md) in modo che il runbook possa essere avviato tramite una chiamata HTTP.

1. Selezionare **Avvia** e poi **Sì** quando richiesto per avviare il runbook.

   ![Avvia runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Viene aperto un riquadro Processo per il processo runbook creato. In questo caso, lasciare aperto il riquadro in modo da poter controllare lo stato di avanzamento del processo.

1. Si noti che lo stato del processo viene visualizzato in **Riepilogo processo**. Questo stato corrisponde a quello visualizzato durante il test del runbook.

   ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Quando lo stato del runbook è Completato, fare clic su **Output**. Viene aperta la pagina Output, `Hello World` in cui è possibile visualizzare il messaggio.

   ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Chiudere la pagina Output.

1. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Si dovrebbe `Hello World` vedere solo nel flusso di output. Si noti che il riquadro Flussi può mostrare altri flussi per un processo del runbook, ad esempio flussi dettagliati e di errore, se il runbook scrive in essi.

   ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Chiudere il riquadro Flussi e il riquadro Processo per tornare alla pagina MyFirstRunbook.
1. Fare clic su **Processi** in **Risorse** per aprire la pagina Processi per questo runbook. Questa pagina elenca tutti i processi creati dal runbook. Dovrebbe essere elencato un solo processo, poiché il processo è stato eseguito una sola volta.

   ![Processi](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Fare clic sul nome del processo per aprire lo stesso riquadro Processo visualizzato all'avvio del runbook. Utilizzare questo riquadro per visualizzare i dettagli di qualsiasi processo creato per il runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. Non può farlo a meno che non si autentica utilizzando le credenziali per la sottoscrizione. L'autenticazione utilizza il cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0)

>[!NOTE]
>Per i runbook `Add-AzAccount` `Add-AzureRMAccount` di PowerShell `Connect-AzAccount`e sono alias per . È possibile utilizzare questi cmdlet [oppure aggiornare i moduli](automation-update-azure-modules.md) nell'account di automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli anche se è stato appena creato un nuovo account di automazione.

1. Passare alla pagina MyFirstRunbook-Workflow e aprire l'editor di testo facendo clic su **Modifica**.
2. Eliminare `Write-Output` la riga.
3. Posizionare il cursore su una riga vuota tra parentesi graffe.
4. Digitare o copiare e incollare il codice seguente, che gestisce l'autenticazione con l'account RunAs di automazione.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Fare clic sul **riquadro Test** per testare il runbook.
1. Fare clic su **Avvia** per avviare il test. Una volta completato, dovresti vedere un output simile al seguente, che visualizza le informazioni di base del tuo account. Questa azione conferma che la credenziale è valida.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passaggio 6 - aggiungere il codice per avviare una macchina virtuale

Ora che il runbook esegue l'autenticazione nella sottoscrizione di Azure, è possibile gestire le risorse. Aggiungiamo un comando per avviare una macchina virtuale. È possibile selezionare qualsiasi macchina virtuale nella sottoscrizione di Azure e per il momento si sta hardcoded tale nome nel runbook. Se si gestiscono risorse tra più sottoscrizioni, `AzContext` è necessario utilizzare il parametro con il cmdlet [Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Specificare il nome e il nome del gruppo di risorse della macchina virtuale da avviare immettendo una chiamata al cmdlet [Start-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) come illustrato di seguito. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Salvare il runbook e poi fare clic su **Riquadro di test** in modo da poterne eseguire il test.
1. Fare clic su **Avvia** per avviare il test. Al termine, verificare che la macchina virtuale sia stata avviata.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passaggio 7: Aggiungere un parametro di input al runbook

Il runbook avvia attualmente la macchina virtuale hardcoded nel runbook. Sarà più utile se è possibile specificare la macchina virtuale all'avvio del runbook. Aggiungiamo parametri di input al runbook per fornire tale funzionalità.

1. Aggiungere le `VMName` variabili `ResourceGroupName` per i parametri e al runbook e usare le variabili con il `Start-AzVM` cmdlet come illustrato di seguito.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Salvare il runbook e aprire il riquadro Test. È ora possibile fornire i valori per le due variabili di input usate nel test.
3. Chiudere il riquadro Test.
4. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
5. Arrestare la macchina virtuale avviata.
6. Fare clic su **Avvia** per avviare il runbook. 
7. Digitare i valori per **VMNAME** e **RESOURCEGROUPNAME** per la macchina virtuale che si intende avviare.

   ![Avviare il runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. Al termine del runbook, verificare che la macchina virtuale sia stata avviata.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere Documenti di [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
* Per iniziare a utilizzare i runbook grafici, vedere [Il mio primo runbook grafico](automation-first-runbook-graphical.md).
* Per iniziare a usare i runbook di PowerShell, vedere il mio primo runbook di [PowerShell.](automation-first-runbook-textual-powershell.md)
* Per altre informazioni sui tipi di runbook e sui relativi vantaggi e limitazioni, vedere [Tipi di runbook di Automazione di Azure.To](automation-runbook-types.md)learn more about runbook types and their advantages and limitations, see Azure Automation runbook types .
* Per altre informazioni sulle funzionalità di supporto degli script di PowerShell, vedere Supporto di [script di PowerShell nativi in Automazione di Azure.For](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)more information on PowerShell script support features, see Native PowerShell script support in Azure Automation .