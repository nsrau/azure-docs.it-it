---
title: Creare un runbook del flusso di lavoro PowerShell in Automazione di Azure
description: Questo articolo illustra come creare, testare e pubblicare un semplice runbook di flusso di lavoro PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: dd9aee01edf45f89feb6a6010c8d958511bc3904
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185399"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Esercitazione: Creare un runbook del flusso di lavoro PowerShell

Questa esercitazione illustra la creazione di un [runbook del flusso di lavoro PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks) in Automazione di Azure. I runbook del flusso di lavoro PowerShell sono runbook di testo basati sul flusso di lavoro Windows PowerShell. È possibile creare e modificare direttamente il codice del runbook usando l'editor di testo nel portale di Azure. 

> [!div class="checklist"]
> * Creare un runbook del flusso di lavoro PowerShell semplice
> * Testare e pubblicare il runbook
> * Eseguire il processo del runbook e monitorarne lo stato
> * Aggiornare il runbook per avviare una macchina virtuale di Azure con i parametri del runbook

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../index.yml) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. Poiché la macchina virtuale viene arrestata e avviata, non deve essere una macchina virtuale di produzione.

## <a name="step-1---create-new-runbook"></a>Passaggio 1: Creare nuovo runbook

Iniziare creando un runbook semplice che restituisce il testo `Hello World`.

1. Nel portale di Azure aprire l'account di automazione.

   La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di Automazione. È anche necessario disporre dell'asset delle credenziali associato alla sottoscrizione.
 
2. Selezionare **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.

3. Per creare un nuovo runbook, selezionare **Crea un runbook**.

4. Denominare il runbook **MyFirstRunbook-Workflow**.

5. In questo caso, verrà creato un [Runbook del flusso di lavoro PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks). In **Tipo di runbook** selezionare **Flusso di lavoro PowerShell**.

6. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="step-2---add-code-to-the-runbook"></a>Passaggio 2: - aggiungere un codice al runbook

È possibile digitare il codice direttamente nel runbook oppure è possibile selezionare i cmdlet, i runbook e le risorse dal controllo Libreria e aggiungerli al runbook con tutti i parametri correlati. Per questa esercitazione si digiterà il codice direttamente nel runbook.

1. Il runbook è attualmente vuoto e contiene solo la parola chiave `Workflow` obbligatoria, il nome del runbook e le parentesi graffe che racchiuderanno l'intero flusso di lavoro.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Digitare `Write-Output "Hello World"` tra le parentesi graffe.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a>Passaggio 3: Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione Bozza e visualizzarne l'output in modo interattivo.

1. Fare clic su **Riquadro di test** per aprire il riquadro di test.

2. Fare clic su **Avvia** per avviare il test, verificando l'unica opzione abilitata.

3. Si noti che viene creato un [processo del runbook](../automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.

   Lo stato iniziale del processo è In coda, per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passa ad Avvio quando un ruolo di lavoro richiede il processo. Infine, lo stato diventa In esecuzione quando viene avviata l'esecuzione effettiva del runbook.

4. Al termine del processo del runbook, viene visualizzato l'output nel riquadro di test. In questo caso, verrà visualizzato l'output `Hello World`.

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.

2. Scorrere verso sinistra per visualizzare il runbook nella pagina **Runbook** e notare che il campo **Stato creazione** è impostato su **Pubblicato**.

3. Scorrere verso destra per visualizzare la pagina **MyFirstRunbook-Workflow**.

   Le opzioni nella parte superiore consentono di avviare il runbook adesso, pianificarlo per un'ora di inizo in futuro o creare un [webhook](../automation-webhooks.md) per poterlo avviare con una chiamata HTTP.

4. Selezionare **Avvia** e poi **Sì** quando richiesto per avviare il runbook.

   ![Avvia runbook](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Verrà aperto un riquadro per il processo del runbook creato. In questo caso, lasciare aperto il riquadro per poter controllare lo stato del processo.

6. Si noti che lo stato del processo viene visualizzato in **Riepilogo processi**. Questo stato corrisponde agli stati visualizzati durante il test del runbook.

   ![Riepilogo dei processi](../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png)

7. Quando lo stato del runbook risulta Completato, fare clic su **Output**. Verrà aperta la pagina Output in cui è possibile visualizzare il messaggio `Hello World`.

   ![Riepilogo dei processi](../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png)

8. Chiudere la pagina Output.

9. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output sarà visibile solo `Hello World`. Si noti che nel riquadro Flussi potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Dettagliato ed Errore, se il runbook esegue la scrittura in questi flussi.

   ![Riepilogo dei processi](../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png)

10. Chiudere il riquadro Flussi e il riquadro Processi per tornare alla pagina MyFirstRunbook.

11. In **Risorse** fare clic su **Processi** per aprire la pagina Processi per questo runbook. In questa pagina sono elencati tutti i processi creati dal runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.

   ![Processi](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Fare clic sul nome del processo per aprire lo stesso riquadro Processo visualizzato quando è stato avviato il runbook. Utilizzare questo riquadro per visualizzare i dettagli di qualsiasi processo creato per il runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. Ma potrà procedere solo eseguendo l'autenticazione con le credenziali per la sottoscrizione. L'autenticazione usa il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0).

>[!NOTE]
>Per i runbook PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per `Connect-AzAccount`. È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](../automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli, anche se è stato appena creato un nuovo account di Automazione.

1. Passare alla pagina MyFirstRunbook-Workflow e fare clic su **Modifica** per aprire l'editor di testo.

2. Eliminare la riga `Write-Output`.

3. Posizionare il cursore su una riga vuota tra parentesi graffe.

4. Digitare o copiare e incollare il codice seguente che gestisce l'autenticazione con l'account RunAs di Automazione.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Fare clic su **Riquadro di test** in modo da testare il runbook.

6. Fare clic su **Avvia** per avviare il test. Al termine verrà visualizzato un output simile al seguente, con le informazioni di base dell'account. Questa azione conferma che le credenziali sono valide.

   ![Authenticate](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passaggio 6 - aggiungere il codice per avviare una macchina virtuale

Ora che il runbook esegue l'autenticazione per la sottoscrizione di Azure è possibile gestire le risorse. Verrà aggiunto un comando per avviare una macchina virtuale. È possibile selezionare una macchina virtuale qualsiasi nella sottoscrizione di Azure. Per ora il nome sarà hardcoded nel runbook. Se si gestiscono risorse in più sottoscrizioni, è necessario usare il parametro `AzContext` con il cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. Specificare il nome del gruppo di risorse e il nome della macchina virtuale da avviare immettendo una chiamata al cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0), come illustrato di seguito. 

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

2. Salvare il runbook e poi fare clic su **Riquadro di test** in modo da poterne eseguire il test.

3. Fare clic su **Avvia** per avviare il test. Al termine, assicurarsi che la macchina virtuale sia stata avviata.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passaggio 7: Aggiungere un parametro di input al runbook

Il runbook avvia effettivamente la macchina virtuale hardcoded nel runbook. Sarà più utile se è possibile specificare la macchina virtuale all'avvio del runbook. Per fornire questa funzionalità, occorre aggiungere parametri di input al runbook.

1. Aggiungere le variabili per i parametri `VMName` e `ResourceGroupName` al runbook e usare le variabili con il cmdlet `Start-AzVM` come illustrato di seguito.

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

5. Arrestare la macchina virtuale avviata in precedenza.

6. Fare clic su **Avvia** per avviare il runbook. 

7. Specificare i valori per **VMNAME** e **RESOURCEGROUPNAME** per la macchina virtuale da avviare.

   ![Avviare il runbook](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Al termine del runbook, verificare che la macchina virtuale sia stata avviata.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su PowerShell, incluse le informazioni di riferimento sul linguaggio e i moduli di apprendimento, vedere la [documentazione di PowerShell](/powershell/scripting/overview).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
* Per iniziare a usare runbook grafici, vedere [Creare un runbook grafico](automation-tutorial-runbook-graphical.md).
* Per iniziare a usare i runbook PowerShell, vedere [Creare un runbook PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](../automation-runbook-types.md).
* Per altre informazioni sulle funzionalità di supporto degli script PowerShell, vedere il blog relativo al [supporto di script PowerShell nativi in Automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
