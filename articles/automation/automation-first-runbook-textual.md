---
title: Il primo runbook del flusso di lavoro di PowerShell in Automazione di Azure
description: Esercitazione in cui viene illustrata la creazione, il test e la pubblicazione di un semplice runbook testuale con flusso di lavoro PowerShell.
keywords: PowerShell flusso di lavoro, esempi di flusso di lavoro PowerShell, flusso di lavoro PowerShell
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 010c6b00161c7a0a004932528fa4f608aa7c5e23
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850672"
---
# <a name="my-first-powershell-workflow-runbook"></a>Il primo runbook del flusso di lavoro PowerShell

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Questa esercitazione illustra la creazione di un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) in Automazione di Azure. Si inizia con un runbook semplice che viene testato e pubblicato, quindi viene illustrato come tenere traccia dello stato del processo del runbook. Si modifica quindi il runbook per gestire effettivamente le risorse di Azure, avviando in questo caso una macchina virtuale di Azure. Si rende infine il runbook più affidabile aggiungendo i relativi parametri.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure.  Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. il computer viene arrestato e avviato, quindi non deve essere una macchina virtuale di produzione.

## <a name="step-1---create-new-runbook"></a>Passaggio 1: Creare nuovo runbook

Si inizia creando un runbook semplice che restituisce il testo *Hello World*.

1. Nel portale di Azure aprire l'account di automazione.

   La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di Automazione. Dovrebbe essere disponibile anche l'asset credenziali citato nei [prerequisiti](#prerequisites).

1. Fare clic su **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.
1. Creare un nuovo runbook facendo clic sul pulsante **+ Aggiungi runbook** e quindi su **Crea un nuovo runbook**.
1. Denominare il runbook *MyFirstRunbook-Workflow*.
1. In questo caso si sta creando un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks), quindi selezionare **Flusso di lavoro PowerShell** per **Tipo di runbook**.
1. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="step-2---add-code-to-the-runbook"></a>Passaggio 2: - aggiungere un codice al runbook

È possibile digitare il codice direttamente nel runbook, oppure è possibile selezionare i cmdlet, i runbook e le risorse dal controllo della libreria e aggiungerle al runbook con tutti i parametri correlati. Per questa procedura dettagliata si digiterà direttamente nel runbook.

1. Il runbook è attualmente vuoto con solo la parola chiave richiesta *workflow*, il nome del runbook e le parentesi graffe che racchiuderanno l'intero flusso di lavoro.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Digitare *Write-Output "Hello World".* tra parentesi graffe.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a>Passaggio 3: Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione **Bozza** e visualizzarne l'output in modo interattivo.

1. Fare clic su **Pannello di test** per aprire il pannello di test.
1. Fare clic su **Avvia** per avviare il test. Questa opzione deve essere l'unica opzione abilitata.
1. Viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato.

   Lo stato del processo verrà avviato come in *coda* per indicare che è in attesa della disponibilità di un Runbook Worker nel cloud. Lo stato passa ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.

1. Al termine del processo del runbook, viene visualizzato l'output. In questo caso dovrebbe essere visualizzato *Hello World*.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.
1. Se si scorre verso sinistra per visualizzare il runbook nella pagina **Runbook**, come **Stato di creazione** viene visualizzato **Pubblicato**.
1. Scorrere verso destra per visualizzare il pannello **MyFirstRunbook-Workflow**.
   Le opzioni nella parte superiore consentono di avviare il runbook, pianificarlo per avviarlo in qualsiasi momento in futuro o creare un [webhook](automation-webhooks.md) per poterlo avviare con una chiamata HTTP.
1. Si vuole solo avviare il runbook, quindi fare clic su **Avvia** e poi su **Sì** quando richiesto.

   ![Avvia runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Verrà aperto un riquadro per il processo del runbook creato. È possibile chiudere questo riquadro, ma in questo caso lo si lascerà aperto per poter controllare lo stato del processo.
1. Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook.

   ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Quando lo stato del runbook risulta *Completato*fare clic su **Output**. Viene aperto il riquadro Output dove si può vedere il testo *Hello World*.

   ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Chiudere il riquadro Output.
1. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. è necessario visualizzare solo *Hello World* nel flusso di output, ma questa vista può mostrare altri flussi per un processo Runbook, ad esempio verbose ed Error Se il Runbook scrive in essi.

   ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Chiudere la pagina dei flussi e la pagina del processo per tornare alla pagina MyFirstRunbook.
1. Fare clic su **processi** per aprire la pagina processi per questo runbook. In questa pagina sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.

   ![Processi](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. È possibile fare clic su questo processo per aprire la stessa pagina del processo visualizzata quando è stato avviato il Runbook. Questa azione consente di tornare indietro nel tempo e visualizzare i dettagli di tutti i processi creati per un runbook particolare.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. Tuttavia, a meno che non sia stata eseguita l'autenticazione con le credenziali a cui si fa riferimento nei [prerequisiti](#prerequisites). A questo scopo si userà il cmdlet **Connect-AzureRmAccount**.

1. Aprire l'editor di testo facendo clic su **Modifica** nel pannello MyFirstRunbook-Workflow.
2. Non è più necessaria la riga **Write-Output**, quindi andare avanti ed eliminarla.
3. Posizionare il cursore su una riga vuota tra parentesi graffe.
4. Digitare o copiare e incollare il codice seguente che gestirà l'autenticazione con l'account RunAs di Automazione:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** e **Login-AzureRmAccount** sono ora alias per **Connect-AzureRMAccount**. Se il cmdlet **Connect-AzureRMAccount**  non esiste, è possibile usare **Add-AzureRmAccount**  o **Login-AzureRmAccount** oppure [aggiornare i moduli](automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti.

> [!NOTE]
> Potrebbe essere necessario [aggiornare i moduli](automation-update-azure-modules.md), anche se è stato appena creato un nuovo account di automazione.

1. Fare clic su **Riquadro di test** in modo da testare il runbook.
1. Fare clic su **Avvia** per avviare il test. Al termine verrà visualizzato un output simile al seguente, con le informazioni di base sull'account. Questa azione conferma che le credenziali sono valide.

   ![Esegui autenticazione](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passaggio 6 - aggiungere il codice per avviare una macchina virtuale

Ora che il runbook esegue l'autenticazione per la sottoscrizione di Azure è possibile gestire le risorse. Si aggiunge un comando per avviare una macchina virtuale. È possibile selezionare qualsiasi macchina virtuale nella sottoscrizione di Azure e per il momento si sta codificando il nome in Runbook. Se si gestiscono risorse tra più sottoscrizioni, è necessario usare il parametro **-AzureRmContext** insieme a [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

1. Dopo *Connect-AzureRmAccount* digitare *Start-AzureRmVM -Name 'NomeVM' -ResourceGroupName 'NomeGruppoDiRisorse'* specificando il nome e il nome del gruppo di risorse della macchina virtuale da avviare.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzureRmContext $AzureContext
   }
   ```

1. Salvare il runbook e poi fare clic su **Riquadro di test** in modo da poterne eseguire il test.
1. Fare clic su **Avvia** per avviare il test. Dopo aver completato l'attività, controllare che la macchina virtuale sia stata avviata.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passaggio 7: Aggiungere un parametro di input al runbook

Ora il runbook avvia la macchina virtuale specificata nel runbook, ma sarebbe più utile se si potesse specificare la macchina virtuale quando si avvia il runbook. Per fornire questa funzionalità, si aggiungeranno dei parametri di input al runbook.

1. Aggiungere parametri per *VMName* e *ResourceGroupName* al runbook e usare queste variabili con il cmdlet **Start-AzureRmVM** come nell'esempio seguente.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Salvare il runbook e aprire il riquadro Test. È ora possibile fornire i valori per le due variabili di input usate nel test.
3. Chiudere il riquadro Test.
4. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
5. Arrestare la macchina virtuale avviata nel passaggio precedente.
6. Fare clic su **Avvia** per avviare il runbook. Digitare **VMName** e **ResourceGroupName** per la macchina virtuale da avviare.

   ![Avvia runbook](media/automation-first-runbook-textual/automation-pass-params.png)

7. Quando il runbook viene completato, controllare che la macchina virtuale sia stata avviata.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la [documentazione di PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview).
* Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md)
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md)
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
* Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere il blog relativo al [supporto di script PowerShell nativi in Automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
