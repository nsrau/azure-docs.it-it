---
title: Gestire runbook modulari in Automazione di Azure
description: Questo articolo descrive come creare un runbook chiamato da un altro runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: c15ed6e9409bee71a778986d8f38ae1ab126c180
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828645"
---
# <a name="create-modular-runbooks"></a>Creare runbook modulari

È buona norma in Automazione di Azure scrivere runbook riutilizzabili e modulari con una funzione discreta chiamata da altri runbook. Un runbook padre chiama spesso uno o più runbook figlio per eseguire la funzionalità richiesta. 

Esistono due metodi per chiamare un runbook figlio, con differenze precise che è necessario comprendere per determinare quale sia il migliore per i propri scenari. La tabella seguente riepiloga le differenze tra i due metodi per chiamare un runbook da un altro runbook.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Processo |I runbook figlio vengono eseguiti nello stesso processo dell’elemento padre. |Viene creato un processo separato per il runbook figlio. |
| Esecuzione |Il runbook padre attende il completamento del runbook figlio prima di continuare. |Il runbook padre continua subito dopo l'avvio del runbook figlio *o* attende il completamento del processo figlio. |
| Output |Il runbook padre può ottenere output direttamente dal runbook figlio. |Il runbook padre deve recuperare l'output dal processo del runbook figlio *o* può ottenere direttamente l'output dal runbook figlio. |
| Parametri |I valori per i parametri di runbook figlio vengono specificati separatamente e possono utilizzare qualsiasi tipo di dati. |I valori dei parametri dei runbook figlio devono essere raggruppati in un'unica tabella hash. Questa tabella hash può contenere solo tipi di dati semplici, matrice e oggetto che usano la serializzazione JSON. |
| Account di automazione |Il runbook padre può usare solo un runbook figlio nello stesso account di Automazione. |I runbook padre possono usare un runbook figlio da qualsiasi account di Automazione, dalla stessa sottoscrizione di Azure, e anche da una sottoscrizione diversa con cui si ha una connessione. |
| Pubblicazione |Il runbook figlio deve essere pubblicato prima della pubblicazione del runbook padre. |Il runbook figlio viene pubblicato prima che il runbook padre venga avviato. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Chiamare un runbook figlio usando l'esecuzione inline

Per chiamare un runbook inline da un altro runbook, usare il nome del runbook e specificare i valori per i relativi parametri, esattamente come si fa per un'attività o un cmdlet. Tutti i runbook nello stesso account di automazione sono disponibili a tutti gli altri per essere utilizzati come quanto segue. Il runbook padre attende il completamento del runbook figlio prima di passare alla riga successiva e riceve direttamente l'eventuale output.

Quando si richiama un runbook inline, esso viene eseguito nello stesso processo del runbook padre. Nella cronologia processo non è indicato il runbook figlio. Eventuali eccezioni e output del flusso dal runbook figlio vengono associati al runbook padre. Questo comportamento ha come risultato un minor numero di processi e semplifica la traccia e la risoluzione dei problemi di questi.

Quando viene pubblicato un runbook, tutti i runbook figlio chiamati devono già essere pubblicati. Il motivo è che quando compila un runbook Automazione di Azure crea un'associazione con i runbook figlio. Se i runbook figlio non sono stati ancora pubblicati, il runbook padre viene pubblicato apparentemente in modo corretto, ma genera un'eccezione quando viene avviato. In questo caso, è possibile ripubblicare il runbook padre in modo che faccia riferimento in modo corretto ai runbook figlio. Non è necessario ripubblicare il runbook padre se uno o più runbook figlio sono stati modificati, perché l'associazione è già stata creata.

I dati dei parametri di un runbook figlio chiamato inline possono essere di qualsiasi tipo, inclusi oggetti complessi. Non viene eseguita alcuna [serializzazione JSON](start-runbooks.md#work-with-runbook-parameters), come invece avviene quando si avvia il runbook usando il portale di Azure o il cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

### <a name="runbook-types"></a>Tipi di runbook

Quali tipi di runbook possono chiamarsi a vicenda?

* Un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) e un [runbook grafico](automation-runbook-types.md#graphical-runbooks) possono chiamarsi reciprocamente inline, dato che entrambi si basano su PowerShell.
* Un [runbook del flusso di lavoro di PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e un runbook del flusso di lavoro di PowerShell grafico possono chiamarsi reciprocamente inline, dato che entrambi si basano sul flusso di lavoro di PowerShell.
* I tipi PowerShell e i tipi flusso di lavoro di PowerShell non possono chiamarsi reciprocamente inline e devono usare `Start-AzAutomationRunbook`.

Quando è importante l'ordine di pubblicazione?

L'ordine di pubblicazione dei runbook è rilevante solo per i runbook del flusso di lavoro di PowerShell e i runbook del flusso di lavoro di PowerShell grafici.

Quando un runbook chiama un runbook figlio grafico o del flusso di lavoro di PowerShell tramite esecuzione inline, usa il nome del runbook. Il nome deve iniziare con `.\\` per specificare che lo script si trova nella directory locale.

### <a name="example"></a>Esempio

L'esempio seguente avvia un runbook figlio di test che accetta un oggetto complesso, un valore intero e un valore booleano. L'output del runbook figlio viene assegnato a una variabile. In questo caso, il runbook figlio è un runbook del flusso di lavoro PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Ecco lo stesso esempio con un runbook PowerShell come elemento figlio.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Avviare un runbook figlio usando un cmdlet

> [!IMPORTANT]
> Se il runbook chiama un runbook figlio con il cmdlet `Start-AzAutomationRunbook` e il parametro `Wait` e il runbook figlio genera un risultato oggetto, l'operazione può generare un errore. Per correggere l'errore, vedere [Runbook con output oggetto](troubleshoot/runbooks.md#child-runbook-object) per informazioni su come implementare la logica di polling dei risultati usando il cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord).

È possibile usare `Start-AzAutomationRunbook` per avviare un runbook come descritto in [Per avviare un runbook con Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Sono disponibili due modalità di utilizzo di questo cmdlet. In una modalità, il cmdlet restituisce l'ID del processo quando viene creato il processo per il runbook figlio. Nell'altra modalità, abilitata dallo script tramite l'indicazione del parametro *Wait*, il cmdlet attende il completamento del processo figlio e restituisce l'output del runbook figlio.

Il processo di un runbook figlio avviato con un cmdlet viene eseguito separatamente dal processo del runbook padre. Questo comportamento produce più processi rispetto all'avvio del runbook inline e rende più difficile il monitoraggio di tali processi. Il runbook padre può avviare più runbook figlio in modo asincrono senza attendere il completamento di ognuno. Per questa esecuzione parallela che chiama i runbook figlio inline, il runbook padre deve usare la [parola chiave parallela](automation-powershell-workflow.md#use-parallel-processing).

L'output dei runbook figlio non viene restituito al runbook padre in modo affidabile a causa della temporizzazione. Le variabili, poi, ad esempio `$VerbosePreference`, `$WarningPreference` e altre, potrebbero non essere propagate ai runbook figlio. Per evitare questi problemi, è possibile avviare i runbook figlio come processi di Automazione separati usando `Start-AzAutomationRunbook` con il parametro `Wait`. Questa tecnica blocca il runbook padre fino al completamento del runbook figlio.

Se non si vuole che il runbook padre rimanga bloccato in attesa, è possibile avviare il runbook figlio usando `Start-AzAutomationRunbook` senza il parametro `Wait`. In questo caso, il runbook deve usare [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) per attendere il completamento del processo. Deve anche usare [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) e [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) per recuperare i risultati.

I parametri per un runbook figlio avviato con un cmdlet vengono resi disponibili sotto forma di tabella hash, come descritto in [Parametri di runbook](start-runbooks.md#work-with-runbook-parameters). Possono essere utilizzati solo tipi di dati semplici. Se il runbook dispone di un parametro con un tipo di dati complessi, deve essere chiamato inline.

Il contesto della sottoscrizione potrebbe andare perso quando si avviano i runbook figlio come processi separati. Perché il runbook figlio possa eseguire i cmdlet del modulo Az per una sottoscrizione di Azure specifica, il runbook figlio deve eseguire l'autenticazione in tale sottoscrizione indipendentemente dal runbook padre.

Se i processi nello stesso account di Automazione usano più sottoscrizioni, la selezione di una sottoscrizione in un processo può cambiare il contesto della sottoscrizione attualmente selezionata anche per altri processi. Per evitare questa situazione, usare `Disable-AzContextAutosave –Scope Process` all'inizio di ogni runbook. Questa azione salva solo il contesto dell'esecuzione del runbook.

### <a name="example"></a>Esempio

L'esempio seguente avvia un runbook figlio con parametri e quindi attende il suo completamento usando il cmdlet `Start-AzAutomationRunbook` con il parametro `Wait`. Al termine, l'esempio raccoglie l'output del cmdlet dal runbook figlio. Per usare `Start-AzAutomationRunbook` lo script deve eseguire l'autenticazione alla sottoscrizione di Azure.

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

## <a name="next-steps"></a>Passaggi successivi

* Per avviare il runbook, vedere [Avviare un runbook in Automazione di Azure](start-runbooks.md).
* Per il monitoraggio del funzionamento dei runbook, vedere [Output e messaggi dei runbook in Automazione di Azure](automation-runbook-output-and-messages.md).