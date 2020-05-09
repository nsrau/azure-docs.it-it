---
title: Runbook figlio in Automazione di Azure
description: Descrive i diversi metodi per avviare un runbook in automazione di Azure da un altro runbook e condividere informazioni tra di essi.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 21dc14362fed2abf80c2c5ecf57f688541c9c639
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994801"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbook figlio in Automazione di Azure

In automazione di Azure è consigliabile scrivere manuali operativi modulari riutilizzabili con una funzione discreta chiamata da altri manuali operativi. Un runbook padre chiama spesso uno o più runbook figlio per eseguire la funzionalità richiesta. Esistono due modi per chiamare un Runbook figlio e ci sono differenze distinte che è necessario comprendere per poter determinare quale sia la soluzione migliore per gli scenari.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Richiamare un runbook figlio con esecuzione inline

Per richiamare un Runbook inline da un altro Runbook, usare il nome del Runbook e fornire i valori per i relativi parametri, esattamente come si farebbe con un'attività o un cmdlet. Tutti i runbook nello stesso account di automazione sono disponibili a tutti gli altri per essere utilizzati come quanto segue. Il Runbook padre attende il completamento del Runbook figlio prima di procedere alla riga successiva e qualsiasi output viene restituito direttamente all'elemento padre.

Quando si richiama un runbook inline, esso viene eseguito nello stesso processo del runbook padre. Non è presente alcuna indicazione nella cronologia processo del Runbook figlio. Eventuali eccezioni e tutti gli output del flusso dal Runbook figlio sono associati all'elemento padre. Questo comportamento comporta un minor numero di processi e ne semplifica la traccia e la risoluzione dei problemi.

Quando viene pubblicato un runbook, tutti i runbook figlio chiamati devono già essere pubblicati. Il motivo è che automazione di Azure compila un'associazione con qualsiasi manuali operativi figlio durante la compilazione di un Runbook. Se il manuali operativi figlio non è ancora stato pubblicato, il Runbook padre sembra pubblicare correttamente, ma genera un'eccezione quando viene avviato. In questo caso, è possibile ripubblicare il runbook padre in modo che faccia riferimento in modo corretto ai runbook figlio. Non è necessario ripubblicare il Runbook padre se un Runbook figlio viene modificato perché l'associazione è già stata creata.

I parametri di un Runbook figlio chiamato inline possono essere di qualsiasi tipo di dati, inclusi gli oggetti complessi. Non esiste alcuna [serializzazione JSON](start-runbooks.md#runbook-parameters), come avviene quando si avvia il Runbook usando il portale di Azure o con il cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) .

### <a name="runbook-types"></a>Tipi di runbook

Quali tipi di Runbook possono chiamarsi reciprocamente?

* Un [Runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e un [Runbook grafico](automation-runbook-types.md#graphical-runbooks) possono chiamare reciprocamente inline, perché entrambi sono basati su PowerShell.
* Un [Runbook del](automation-runbook-types.md#powershell-workflow-runbooks) flusso di lavoro PowerShell e un Runbook del flusso di lavoro PowerShell grafico possono chiamare reciprocamente inline, perché entrambi sono basati sul flusso di lavoro di PowerShell.
* I tipi di PowerShell e i tipi di flusso di lavoro PowerShell non possono chiamare reciprocamente `Start-AzAutomationRunbook`inline e devono usare.

Quando l'ordine di pubblicazione è importante?

L'ordine di pubblicazione di manuali operativi è importante solo per il flusso di lavoro PowerShell e manuali operativi di flusso di lavoro PowerShell grafico.

Quando il Runbook chiama un Runbook figlio grafico o del flusso di lavoro PowerShell usando l'esecuzione inline, usa il nome del Runbook. Il nome deve iniziare con `.\\` per specificare che lo script si trova nella directory locale.

### <a name="example"></a>Esempio

Nell'esempio seguente viene avviato un Runbook figlio di test che accetta un oggetto complesso, un valore integer e un valore booleano. L'output del runbook figlio viene assegnato a una variabile. In questo caso, il runbook figlio è un runbook del flusso di lavoro PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Di seguito è riportato lo stesso esempio che usa un Runbook di PowerShell come figlio.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Avvio di un Runbook figlio con un cmdlet

> [!IMPORTANT]
> Se il Runbook richiama un Runbook figlio con il `Start-AzAutomationRunbook` cmdlet con il `Wait` parametro e il Runbook figlio produce un risultato dell'oggetto, è possibile che si verifichi un errore nell'operazione. Per risolvere l'errore, vedere [manuali operativi figlio con l'output dell'oggetto](troubleshoot/runbooks.md#child-runbook-object) per informazioni su come implementare la logica per eseguire il polling dei risultati usando il cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

È possibile usare `Start-AzAutomationRunbook` per avviare un Runbook come descritto in [per avviare un runbook con Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Sono disponibili due modalità di utilizzo di questo cmdlet. In una modalità, il cmdlet restituisce l'ID processo quando viene creato il processo per il Runbook figlio. In altre modalità, che lo script Abilita specificando il parametro *wait* , il cmdlet attende il completamento del processo figlio e restituisce l'output dal Runbook figlio.

Il processo da un Runbook figlio avviato con un cmdlet viene eseguito separatamente dal processo del Runbook padre. Questo comportamento comporta più processi rispetto all'avvio di Runbook inline e rende più difficile tenere traccia dei processi. L'elemento padre può avviare più di un Runbook figlio in modo asincrono senza attendere il completamento di ognuno di essi. Per questa esecuzione parallela che chiama il manuali operativi figlio inline, il Runbook padre deve usare la [parola chiave parallela](automation-powershell-workflow.md#parallel-processing).

L'output del Runbook figlio non restituisce in modo affidabile il Runbook padre a causa di un intervallo di tempo. Inoltre, le variabili, ad `$VerbosePreference`esempio `$WarningPreference`, e altre, potrebbero non essere propagate al manuali operativi figlio. Per evitare questi problemi, è possibile avviare il manuali operativi figlio come processi di automazione distinti `Start-AzAutomationRunbook` usando con `Wait` il parametro. Questa tecnica blocca il Runbook padre fino al completamento del Runbook figlio.

Se non si desidera che il Runbook padre venga bloccato in attesa, è possibile avviare il Runbook figlio utilizzando `Start-AzAutomationRunbook` senza il `Wait` parametro. In questo caso, il Runbook deve usare [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) per attendere il completamento del processo. Per recuperare i risultati, è inoltre necessario utilizzare [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) e [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

I parametri per un Runbook figlio avviato con un cmdlet vengono forniti come Hashtable, come descritto in [parametri di Runbook](start-runbooks.md#runbook-parameters). Possono essere utilizzati solo tipi di dati semplici. Se il runbook dispone di un parametro con un tipo di dati complessi, deve essere chiamato inline.

Il contesto della sottoscrizione potrebbe andare perso quando si avviano i runbook figlio come processi separati. Affinché il Runbook figlio esegua i cmdlet AZ Module per una sottoscrizione di Azure specifica, l'elemento figlio deve eseguire l'autenticazione a questa sottoscrizione indipendentemente dal Runbook padre.

Se i processi all'interno dello stesso account di automazione funzionano con più di una sottoscrizione, la selezione di una sottoscrizione in un processo può modificare il contesto di sottoscrizione attualmente selezionato per altri processi. Per evitare questa situazione, usare `Disable-AzContextAutosave –Scope Process` all'inizio di ogni Runbook. Questa azione salva solo il contesto dell'esecuzione del runbook.

### <a name="example"></a>Esempio

Nell'esempio seguente viene avviato un Runbook figlio con parametri e quindi ne viene atteso il completamento utilizzando `Start-AzAutomationRunbook` il cmdlet con `Wait` il parametro. Al termine, l'esempio raccoglie l'output del cmdlet dal Runbook figlio. Per usare `Start-AzAutomationRunbook`, lo script deve eseguire l'autenticazione alla sottoscrizione di Azure.

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

Nella tabella seguente sono riepilogate le differenze tra i due modi per chiamare un Runbook da un altro Runbook.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Processo |I runbook figlio vengono eseguiti nello stesso processo dell’elemento padre. |Viene creato un processo separato per il runbook figlio. |
| Esecuzione |Il runbook padre attende il completamento del runbook figlio prima di continuare. |Il runbook padre continua subito dopo l'avvio del runbook figlio *o* attende il completamento del processo figlio. |
| Output |Il runbook padre può ottenere output direttamente dal runbook figlio. |Il runbook padre deve recuperare l'output dal processo del runbook figlio *o* può ottenere direttamente l'output dal runbook figlio. |
| Parametri |I valori per i parametri di runbook figlio vengono specificati separatamente e possono utilizzare qualsiasi tipo di dati. |I valori dei parametri dei runbook figlio devono essere raggruppati in un'unica tabella hash. Questa tabella hash può contenere solo tipi di dati semplici, matrice e oggetto che usano la serializzazione JSON. |
| Account di automazione |Il Runbook padre può usare solo Runbook figlio nello stesso account di automazione. |Il manuali operativi padre può usare un Runbook figlio da qualsiasi account di automazione, dalla stessa sottoscrizione di Azure e anche da una sottoscrizione diversa a cui si ha una connessione. |
| Pubblicazione |Il runbook figlio deve essere pubblicato prima della pubblicazione del runbook padre. |Il Runbook figlio viene pubblicato in qualsiasi momento prima dell'avvio del Runbook padre. |

## <a name="next-steps"></a>Passaggi successivi

* [Avvio di un Runbook in automazione di Azure](start-runbooks.md)
* [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md)