---
title: Runbook figlio in Automazione di Azure
description: Descrive i diversi metodi per avviare un runbook in automazione di Azure da un altro runbook e condividere informazioni tra di essi.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84f17b76f03c01d0b1441a50b9bcbddc1dfe2ef3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61081579"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbook figlio in Automazione di Azure

È buona norma in Automazione di Azure scrivere runbook riutilizzabili e modulari con una funzione discreta usata da altri runbook. Un runbook padre chiama spesso uno o più runbook figlio per eseguire la funzionalità richiesta. Esistono due modi per chiamare un runbook figlio e ognuno presenta differenze che è necessario comprendere in modo che sia possibile determinare il modo migliore per i diversi scenari.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Richiamare un runbook figlio con esecuzione inline

Per richiamare un runbook inline da un altro runbook, utilizzare il nome del runbook e fornire valori per i relativi parametri, esattamente come si farebbe per un'attività o un cmdlet.  Tutti i runbook nello stesso account di automazione sono disponibili a tutti gli altri per essere utilizzati come quanto segue. Il runbook padre attenderà il completamento del runbook figlio prima di passare alla riga successiva e gli output vengono restituiti direttamente all'elemento padre.

Quando si richiama un runbook inline, esso viene eseguito nello stesso processo del runbook padre. Nella cronologia processo non è indicato il runbook figlio eseguito. Eventuali eccezioni e flussi di output dal runbook figlio vengono associati al runbook padre. In questo modo si ottiene un minor numero di processi che sono quindi più semplici da monitorare e risolvere, poiché le eccezioni generate dal runbook figlio e i relativi output del flusso sono associati al processo padre.

Quando viene pubblicato un runbook, tutti i runbook figlio chiamati devono già essere pubblicati. Questo avviene perché l'automazione di Azure crea un'associazione con i runbook figlio quando viene compilato un runbook. In caso contrario, il runbook padre sembrerà pubblicato correttamente ma al suo avvio verrà generata un'eccezione. In questo caso, è possibile ripubblicare il runbook padre in modo che faccia riferimento in modo corretto ai runbook figlio. Non è necessario ripubblicare il runbook padre se uno o più runbook figlio sono stati modificati, in quanto l'associazione è già stata creata.

I parametri di un runbook figlio chiamati inline possono essere di qualsiasi tipo di dati, inclusi gli oggetti complessi. Non c'è alcuna [serializzazione JSON](start-runbooks.md#runbook-parameters), come invece avviene quando si avvia il runbook usando il portale di Azure o il cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Tipi di runbook

Tipi che possono richiamarsi a vicenda:

* Un [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e i [runbook grafici](automation-runbook-types.md#graphical-runbooks) possono chiamarsi reciprocamente inline (entrambi i tipi sono basati su PowerShell).
* Un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e i runbook grafici del flusso di lavoro PowerShell possono richiamarsi a vicenda inline (entrambi i tipi si basano su PowerShell).
* I tipi PowerShell e i tipi di flusso di lavoro PowerShell non possono richiamarsi a vicenda online e devono utilizzare Start-AzureRmAutomationRunbook.

Quando è importante l’ordine di pubblicazione:

* L'ordine di pubblicazione dei runbook è rilevante solo per i runbook del flusso di lavoro PowerShell e i runbook grafici del flusso di lavoro PowerShell.

Quando si chiama un runbook figlio grafico o del flusso di lavoro PowerShell tramite l’esecuzione inline, si usa il nome del runbook.  Quando si chiama un runbook figlio di PowerShell, è necessario anteporre al nome *.\\* per specificare che lo script si trova nella directory locale.

### <a name="example"></a>Esempio

Nell'esempio seguente viene avviato un runbook figlio di test che accetta tre parametri, ossia un oggetto complesso, un numero intero e un valore booleano. L'output del runbook figlio viene assegnato a una variabile.  In questo caso, il runbook figlio è un runbook del flusso di lavoro PowerShell.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Di seguito si trova lo stesso esempio con un runbook di PowerShell come elemento figlio.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Avvio di un runbook figlio utilizzando cmdlet

> [!IMPORTANT]
> Se si richiama un runbook figlio con il cmdlet `Start-AzureRmAutomationRunbook` e il parametro `-Wait` e il risultato del runbook figlio è un oggetto, potrebbero verificarsi errori. Per correggere l'errore, vedere [Il runbook figlio restituisce un errore quando il flusso di output contiene oggetti diversi da semplici tipi di dati](troubleshoot/runbooks.md#child-runbook-object) per informazioni su come implementare la logica di polling dei risultati e usare [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

È possibile usare il cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) per avviare un runbook come descritto nella sezione relativa all'[avvio di un runbook con Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Sono disponibili due modalità di utilizzo di questo cmdlet.  In una modalità, il cmdlet restituisce l'ID del processo quando viene creato il processo figlio per il runbook figlio.  Nell'altra modalità, abilitata specificando il parametro **-wait**, il cmdlet attende il termine del processo figlio e restituisce l'output dal runbook figlio.

Il processo di un runbook figlio avviato con un cmdlet viene eseguito in un processo separato dal runbook padre. Questo comportamento produce più processi rispetto all'avvio del runbook inline e li rende più difficili da monitorare. Il runbook padre può avviare più runbook figlio in modo asincrono senza attendere il completamento di ognuno. Per questa stessa tipologia di esecuzione parallela che chiama l’inline del runbook figlio, il runbook padre dovrà usare la [parola chiave parallela](automation-powershell-workflow.md#parallel-processing).

L'output dei runbook figlio non viene restituito al runbook padre in modo affidabile a causa della temporizzazione. È anche possibile che alcune variabili come $VerbosePreference, $WarningPreference e altre variabili non vengano propagate ai runbook figlio. Per evitare questi problemi, è possibile avviare i runbook figlio come processi di automazione separati usando il cmdlet `Start-AzureRmAutomationRunbook` con l'opzione `-Wait`. In questo modo viene bloccato il runbook padre fino al completamento del runbook figlio.

Se non si vuole che il runbook padre rimanga bloccato in attesa, è possibile avviare il runbook figlio usando il cmdlet `Start-AzureRmAutomationRunbook` senza l'opzione `-Wait`. Sarà quindi necessario usare `Get-AzureRmAutomationJob` per attendere il completamento del processo e `Get-AzureRmAutomationJobOutput` e `Get-AzureRmAutomationJobOutputRecord` per recuperare i risultati.

I parametri per un runbook figlio avviato con un cmdlet vengono forniti come una tabella hash, come descritto in [Parametri di Runbook](start-runbooks.md#runbook-parameters). Possono essere utilizzati solo tipi di dati semplici. Se il runbook dispone di un parametro con un tipo di dati complessi, deve essere chiamato inline.

Il contesto della sottoscrizione potrebbe andare perso quando si avviano i runbook figlio come processi separati. Affinché il runbook figlio possa eseguire i cmdlet di Azure Resource Manager a livello di una sottoscrizione di Azure specifica, il runbook figlio deve autenticarsi in tale sottoscrizione indipendentemente dal runbook padre.

Se i processi nello stesso account di Automazione di Azure usano più sottoscrizioni, la selezione di una sottoscrizione in un processo può cambiare il contesto della sottoscrizione attualmente selezionata anche per altri processi. Per evitare questo problema, usare `Disable-AzureRmContextAutosave –Scope Processsave` all'inizio di ogni runbook. Questa azione salva solo il contesto dell'esecuzione del runbook.

### <a name="example"></a>Esempio

Nell'esempio seguente viene avviato un runbook figlio con parametri e si attende il suo completamento con il parametro Start-AzureRmAutomationRunbook -wait. Una volta completato, l'output viene raccolto dal runbook figlio. Per usare `Start-AzureRmAutomationRunbook` è necessario eseguire l'autenticazione alla sottoscrizione di Azure.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Confronto di metodi per chiamare un runbook figlio

Nella tabella seguente vengono riepilogate le differenze tra i due metodi per chiamare un runbook da un altro runbook.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Processo |I runbook figlio vengono eseguiti nello stesso processo dell’elemento padre. |Viene creato un processo separato per il runbook figlio. |
| Esecuzione |Il runbook padre attende il completamento del runbook figlio prima di continuare. |Il runbook padre continua subito dopo l'avvio del runbook figlio *o* attende il completamento del processo figlio. |
| Output |Il runbook padre può ottenere output direttamente dal runbook figlio. |Il runbook padre deve recuperare l'output dal processo del runbook figlio *o* può ottenere direttamente l'output dal runbook figlio. |
| Parametri |I valori per i parametri di runbook figlio vengono specificati separatamente e possono utilizzare qualsiasi tipo di dati. |I valori dei parametri dei runbook figlio devono essere raggruppati in un'unica tabella hash. Questa tabella hash può contenere solo tipi di dati semplici, matrice e oggetto che usano la serializzazione JSON. |
| Account di automazione |Il runbook padre può utilizzare solo runbook figlio nello stesso account di automazione. |I runbook padre possono usare un runbook figlio di qualsiasi account di automazione della stessa sottoscrizione di Azure e anche di una sottoscrizione diversa a cui si ha una connessione. |
| Pubblicazione |Il runbook figlio deve essere pubblicato prima della pubblicazione del runbook padre. |Il runbook figlio deve essere pubblicato prima che il runbook padre venga avviato. |

## <a name="next-steps"></a>Passaggi successivi

* [Avvio di un runbook in Automazione di Azure](start-runbooks.md)
* [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md)

