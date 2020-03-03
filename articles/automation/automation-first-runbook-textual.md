---
title: Il primo runbook del flusso di lavoro di PowerShell in Automazione di Azure
description: Esercitazione in cui viene illustrata la creazione, il test e la pubblicazione di un semplice runbook testuale con flusso di lavoro PowerShell.
keywords: PowerShell flusso di lavoro, esempi di flusso di lavoro PowerShell, flusso di lavoro PowerShell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: 4c816ade5ae6b416ba716433f385b75e1b32eb92
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226513"
---
# <a name="my-first-powershell-workflow-runbook"></a>Il primo runbook del flusso di lavoro PowerShell

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Questa esercitazione illustra la creazione di un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) in Automazione di Azure. Iniziare con un Runbook semplice che viene testato e pubblicato imparando a tenere traccia dello stato del processo del Runbook. Modificare quindi il Runbook per gestire effettivamente le risorse di Azure, illustrando l'avvio di una macchina virtuale di Azure. Infine, rendere più affidabili i Runbook aggiungendo parametri Runbook.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. Poiché si arresta e si avvia questo computer, non dovrebbe essere una macchina virtuale di produzione.

## <a name="step-1---create-new-runbook"></a>Passaggio 1: Creare nuovo runbook

Iniziare creando un runbook semplice che restituisce il testo **Hello World**.

1. Nel portale di Azure aprire l'account di automazione.

   La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. La maggior parte di tali asset sono i moduli inclusi automaticamente in un nuovo account di automazione. È anche necessario che l'asset delle credenziali sia associato alla sottoscrizione.
 
1. Selezionare **manuali operativi** in **automazione processi** per aprire l'elenco di manuali operativi.
1. Creare un nuovo Runbook selezionando **Crea una Runbook**.
1. Denominare il runbook **MyFirstRunbook-Workflow**.
1. In questo caso verrà creato un [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Quindi selezionare **flusso di lavoro PowerShell** per **tipo di Runbook**.
1. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="step-2---add-code-to-the-runbook"></a>Passaggio 2: - aggiungere un codice al runbook

È possibile digitare il codice direttamente in Runbook oppure è possibile selezionare i cmdlet, manuali operativi e gli asset dal controllo libreria e aggiungerli a runbook con tutti i parametri correlati. Per questa esercitazione, digitare il codice direttamente nel runbook.

1. Il Runbook è attualmente vuoto con solo la parola chiave required **Workflow** , il nome del Runbook e le parentesi graffe che racchiuderanno l'intero flusso di lavoro.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Digitare `Write-Output "Hello World"` tra parentesi graffe.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a>Passaggio 3: Testare il runbook

Prima di pubblicare il Runbook per renderlo disponibile nell'ambiente di produzione, è necessario testarlo per verificare che funzioni correttamente. Il test di un Runbook esegue la versione bozza e consente di visualizzarne l'output in modo interattivo.

1. Selezionare **riquadro Test** per aprire il riquadro test.
1. Fare clic su **Avvia** per avviare il test, verificando l'unica opzione abilitata.
1. Si noti che viene creato un [processo del Runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.

   Lo stato del processo viene avviato come **accodato**, a indicare che il processo è in attesa che un Runbook Worker nel cloud diventi disponibile. Lo stato passa a **avvio** quando un thread di lavoro dichiara il processo. Infine, lo stato viene **eseguito** quando il Runbook inizia effettivamente a essere eseguito.

1. Al termine del processo del Runbook, nel riquadro test viene visualizzato l'output. In questo caso viene visualizzato **Hello World**.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il Runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.
1. Scorrere verso sinistra per visualizzare il Runbook nella pagina **manuali operativi** e notare che il campo **stato di creazione** è impostato su **pubblicato**.
1. Scorrere verso destra per visualizzare la pagina per **MyFirstRunbook-Workflow**.

   Le opzioni nella parte superiore consentono di avviare il Runbook ora, pianificare un'ora di avvio futura o creare un [webhook](automation-webhooks.md) in modo che Runbook possa essere avviato tramite una chiamata http.

1. Selezionare **Avvia** e poi **Sì** quando richiesto per avviare il runbook.

   ![Avvia runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Viene aperto un riquadro del processo per il processo Runbook creato. In questo caso, lasciare aperto il riquadro per poter controllare lo stato del processo.

1. Si noti che lo stato del processo è visualizzato in **Riepilogo processi**. Questo stato corrisponde agli stati visualizzati durante il test di Runbook.

   ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Quando lo stato del runbook risulta **Completato**fare clic su **Output**. Viene aperta la pagina output, in cui è possibile visualizzare il messaggio di **Hello World** .

   ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Chiudere la pagina Output.

1. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output dovrebbe essere visibile solo **Hello World** . Si noti che nel riquadro flussi possono essere visualizzati altri flussi per un processo Runbook, ad esempio flussi dettagliati e di errore, se il Runbook scrive in essi.

   ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Chiudere il riquadro dei flussi e il riquadro dei processi per tornare alla pagina **MyFirstRunbook** .
1. Fare clic su **processi** in **risorse** per aprire la pagina processi per questo runbook. Questa pagina elenca tutti i processi creati dal Runbook. Dovrebbe essere visualizzato un solo processo, perché il processo è stato eseguito una sola volta.

   ![Processi](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Fare clic sul nome del processo per aprire lo stesso riquadro del processo visualizzato quando è stato avviato il Runbook. Utilizzare questo riquadro per visualizzare i dettagli di qualsiasi processo creato per il Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. Questa operazione può essere eseguita solo se l'autenticazione viene eseguita utilizzando le credenziali per la sottoscrizione. L'autenticazione usa il cmdlet **Connect-AzAccount** .

>[!NOTE]
>Per PowerShell manuali operativi, **Add-AzAccount** e **Add-AzureRMAccount** sono alias per **Connect-AzAccount**. È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](automation-update-azure-modules.md) nell'account di automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli anche se è stato appena creato un nuovo account di automazione.

1. Passare alla pagina **MyFirstRunbook-Workflow** e aprire l'editor di testo facendo clic su **modifica**.
2. Eliminare la riga di `Write-Output`.
3. Posizionare il cursore su una riga vuota tra parentesi graffe.
4. Digitare o copiare e incollare il codice seguente che gestisce l'autenticazione con l'account RunAs di automazione.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Fare clic su **Riquadro di test** in modo da testare il runbook.
1. Fare clic su **Avvia** per avviare il test. Al termine, verrà visualizzato un output simile al seguente, in cui vengono visualizzate le informazioni di base del proprio account. Questa azione conferma che le credenziali sono valide.

   ![Autenticazione](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passaggio 6 - aggiungere il codice per avviare una macchina virtuale

Ora che il Runbook esegue l'autenticazione alla sottoscrizione di Azure, è possibile gestire le risorse. È ora possibile aggiungere un comando per avviare una macchina virtuale. È possibile selezionare qualsiasi macchina virtuale nella sottoscrizione di Azure e per il momento si sta codificando il nome in Runbook. Se si gestiscono risorse tra più sottoscrizioni, è necessario usare il parametro *AzContext* con il cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext) .

1. Specificare il nome e il nome del gruppo di risorse della macchina virtuale per iniziare inserendo una chiamata al cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) , come illustrato di seguito. 

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

Il Runbook avvia attualmente la macchina virtuale che è stata codificata in Runbook. Sarà più utile se è possibile specificare la macchina virtuale all'avvio del Runbook. Aggiungere i parametri di input al Runbook per fornire tale funzionalità.

1. Aggiungere i valori per *VMName* e *ResourceGroupName* al Runbook e usare le variabili associate con il cmdlet **Start-AzVM** , come illustrato di seguito.

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
7. Digitare i valori per **VMName** e **RESOURCEGROUPNAME** per la macchina virtuale che si sta per avviare.

   ![Avviare il runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. Al termine del Runbook, verificare che la macchina virtuale sia stata avviata.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Per iniziare a usare manuali operativi grafici, vedere [il primo Runbook grafico](automation-first-runbook-graphical.md).
* Per iniziare a usare i runbook PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Per altre informazioni sui tipi di Runbook e i relativi vantaggi e limitazioni, vedere [tipi di Runbook di automazione di Azure](automation-runbook-types.md).
* Per altre informazioni sulle funzionalità di supporto degli script PowerShell, vedere [supporto degli script PowerShell nativi in automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
