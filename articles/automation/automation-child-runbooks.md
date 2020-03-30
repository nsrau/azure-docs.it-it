---
title: Runbook figlio in Automazione di Azure
description: Descrive i diversi metodi per avviare un runbook in automazione di Azure da un altro runbook e condividere informazioni tra di essi.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367364"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbook figlio in Automazione di Azure

È consigliabile in Automazione di Azure scrivere runbook modulari riutilizzabili con una funzione discreta chiamata da altri runbook. Un runbook padre chiama spesso uno o più runbook figlio per eseguire la funzionalità richiesta. Esistono due modi per chiamare un runbook figlio e esistono differenze distinte che è necessario comprendere per essere in grado di determinare ciò che è più adatto per gli scenari.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Richiamare un runbook figlio con esecuzione inline

Per richiamare un runbook inline da un altro runbook, usare il nome del runbook e fornire i valori per i relativi parametri, proprio come si utilizzerebbe un'attività o un cmdlet. Tutti i runbook nello stesso account di automazione sono disponibili a tutti gli altri per essere utilizzati come quanto segue. Il runbook padre attende il completamento del runbook figlio prima di passare alla riga successiva e qualsiasi output restituisce direttamente all'elemento padre.

Quando si richiama un runbook inline, esso viene eseguito nello stesso processo del runbook padre. Non vi è alcuna indicazione nella cronologia dei processi del runbook figlio. Eventuali eccezioni e gli output di flusso dal runbook figlio sono associati all'elemento padre. Questo comportamento comporta un numero inferiore di processi e ne semplifica il monitoraggio e la risoluzione dei problemi.

Quando viene pubblicato un runbook, tutti i runbook figlio chiamati devono già essere pubblicati. Il motivo è che Automazione di Azure crea un'associazione con qualsiasi runbook figlio quando compila un runbook. Se i runbook figlio non sono già stati pubblicati, il runbook padre sembra pubblicarlo correttamente, ma genera un'eccezione all'avvio. In questo caso, è possibile ripubblicare il runbook padre in modo che faccia riferimento in modo corretto ai runbook figlio. Non è necessario ripubblicare il runbook padre se viene modificato un runbook figlio perché l'associazione è già stata creata.

I parametri di un runbook figlio chiamato inline possono essere di qualsiasi tipo di dati, inclusi gli oggetti complessi. Non esiste alcuna [serializzazione JSON,](start-runbooks.md#runbook-parameters)come quando si avvia il runbook utilizzando il portale di Azure o con il cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Tipi di runbook

Quali tipi di runbook possono chiamarsi a vicenda?

* Un runbook di PowerShell e un runbook grafico possono chiamarsi inline, poiché entrambi sono basati su PowerShell.A [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) and a [graphical runbook](automation-runbook-types.md#graphical-runbooks) can call each other inline, as both are PowerShell-based.
* Un runbook del flusso di lavoro di PowerShell e un runbook del flusso di lavoro di PowerShell grafico possono chiamarsi inline, poiché entrambi sono basati sul flusso di lavoro di PowerShell.A [PowerShell Workflow runbook](automation-runbook-types.md#powershell-workflow-runbooks) and a graphical PowerShell Workflow runbook can call each other inline, as both are PowerShell Workflow-based.
* I tipi di PowerShell e i tipi di flusso di lavoro `Start-AzAutomationRunbook`di PowerShell non possono chiamarsi inline e devono utilizzare .

Quando è importante l'ordine di pubblicazione?

L'ordine di pubblicazione dei runbook è importante solo per il flusso di lavoro di PowerShell e i runbook grafici del flusso di lavoro di PowerShell.The publish order of runbooks only matters for PowerShell Workflow and graphical PowerShell Workflow runbooks.

Quando il runbook chiama un runbook figlio grafico o PowerShell Workflow usando l'esecuzione inline, usa il nome del runbook. Il nome deve `.\\` iniziare con per specificare che lo script si trova nella directory locale.

### <a name="example"></a>Esempio

Nell'esempio seguente viene avviato un runbook figlio di test che accetta un oggetto complesso, un valore intero e un valore booleano. L'output del runbook figlio viene assegnato a una variabile. In questo caso, il runbook figlio è un runbook del flusso di lavoro PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Ecco lo stesso esempio usando un runbook di PowerShell come figlio.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Avvio di un runbook figlio utilizzando un cmdlet

> [!IMPORTANT]
> Se il runbook richiama un runbook figlio con il `Start-AzAutomationRunbook` cmdlet con il `Wait` parametro e il runbook figlio produce un risultato dell'oggetto, l'operazione potrebbe riscontrare un errore. Per risolvere l'errore, vedere [Runbooks figlio con output dell'oggetto](troubleshoot/runbooks.md#child-runbook-object) per informazioni su come implementare la logica per eseguire il polling dei risultati utilizzando il cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

È possibile `Start-AzAutomationRunbook` utilizzare per avviare un runbook come descritto in [Per avviare un runbook con Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Sono disponibili due modalità di utilizzo di questo cmdlet. In una modalità, il cmdlet restituisce l'ID processo quando viene creato il processo per il runbook figlio. Nell'altra modalità abilitata dallo script specificando il parametro *Wait,* il cmdlet attende il completamento del processo figlio e restituisce l'output del runbook figlio.

Il processo di un runbook figlio avviato con un cmdlet viene eseguito separatamente dal processo runbook padre. Questo comportamento comporta più processi rispetto all'avvio del runbook inline e rende i processi più difficili da tenere traccia. L'elemento padre può avviare più runbook figlio in modo asincrono senza attendere il completamento di ognuno. Per questa esecuzione parallela che chiama i runbook figlio inline, il runbook padre deve usare la [parola chiave parallel](automation-powershell-workflow.md#parallel-processing).

L'output del runbook figlio non torna al runbook padre in modo affidabile a causa della temporizzazione. Inoltre, variabili quali `$VerbosePreference` `$WarningPreference`, e altre potrebbero non essere propagate ai runbook figlio. Per evitare questi problemi, è possibile avviare `Start-AzAutomationRunbook` i `Wait` runbook figlio come processi di automazione separati utilizzando con il parametro . Questa tecnica blocca il runbook padre fino al completamento del runbook figlio.

Se non si desidera che il runbook padre venga bloccato durante l'attesa, è possibile avviare il runbook figlio senza `Start-AzAutomationRunbook` il `Wait` parametro. In questo caso, il runbook deve usare [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) per attendere il completamento del processo. Deve inoltre usare [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) e [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) per recuperare i risultati.

I parametri per un runbook figlio avviato con un cmdlet vengono forniti come tabella hash, come descritto in [Parametri Runbook](start-runbooks.md#runbook-parameters). Possono essere utilizzati solo tipi di dati semplici. Se il runbook dispone di un parametro con un tipo di dati complessi, deve essere chiamato inline.

Il contesto della sottoscrizione potrebbe andare perso quando si avviano i runbook figlio come processi separati. Affinché il runbook figlio esegua i cmdlet del modulo Az su una sottoscrizione di Azure specifica, il bambino deve eseguire l'autenticazione a questa sottoscrizione indipendentemente dal runbook padre.

Se i processi all'interno dello stesso account di automazione funzionano con più di una sottoscrizione, la selezione di una sottoscrizione in un processo può modificare il contesto di sottoscrizione attualmente selezionato per altri processi. Per evitare questa `Disable-AzContextAutosave –Scope Process` situazione, utilizzare all'inizio di ogni runbook. Questa azione salva solo il contesto dell'esecuzione del runbook.

### <a name="example"></a>Esempio

Nell'esempio seguente viene avviato un runbook figlio con `Start-AzAutomationRunbook` parametri e `Wait` quindi viene attendere il completamento utilizzando il cmdlet con il parametro . Al termine, l'esempio raccoglie l'output del cmdlet dal runbook figlio. Per `Start-AzAutomationRunbook`usare , lo script deve eseguire l'autenticazione nella sottoscrizione di Azure.To use , the script must authenticate to your Azure subscription.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Confronto di metodi per chiamare un runbook figlio

Nella tabella seguente sono riepilogate le differenze tra i due modi per chiamare un runbook da un altro runbook.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Processo |I runbook figlio vengono eseguiti nello stesso processo dell’elemento padre. |Viene creato un processo separato per il runbook figlio. |
| Esecuzione |Il runbook padre attende il completamento del runbook figlio prima di continuare. |Il runbook padre continua subito dopo l'avvio del runbook figlio *o* attende il completamento del processo figlio. |
| Output |Il runbook padre può ottenere output direttamente dal runbook figlio. |Il runbook padre deve recuperare l'output dal processo del runbook figlio *o* può ottenere direttamente l'output dal runbook figlio. |
| Parametri |I valori per i parametri di runbook figlio vengono specificati separatamente e possono utilizzare qualsiasi tipo di dati. |I valori dei parametri dei runbook figlio devono essere raggruppati in un'unica tabella hash. Questa tabella hash può contenere solo tipi di dati semplici, matrice e oggetto che usano la serializzazione JSON. |
| Account di automazione |Il runbook padre può usare il runbook figlio solo nello stesso account di automazione. |I runbook padre possono usare un runbook figlio da qualsiasi account di automazione, dalla stessa sottoscrizione di Azure e anche da una sottoscrizione diversa a cui si ha una connessione. |
| Pubblicazione |Il runbook figlio deve essere pubblicato prima della pubblicazione del runbook padre. |Il runbook figlio viene pubblicato in qualsiasi momento prima dell'avvio del runbook padre. |

## <a name="next-steps"></a>Passaggi successivi

* [Avvio di un runbook in Automazione di AzureStarting a runbook in Azure Automation](start-runbooks.md)
* [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md)