---
title: Informazioni sul flusso di lavoro di PowerShell per Automazione di Azure
description: Questo articolo illustra le differenze tra il flusso di lavoro di PowerShell e PowerShell e i concetti applicabili ai runbook di Automazione.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: f175e495af8e925c0d5a6c61669a5e2f44f73ae7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186002"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Informazioni sul flusso di lavoro di PowerShell per Automazione di Azure

I runbook in Automazione di Azure vengono implementati come flussi di lavoro di Windows PowerShell, script di Windows PowerShell che usano Windows Workflow Foundation. Un flusso di lavoro è una sequenza di passaggi programmati e connessi che consentono di eseguire attività di lunga durata o richiedono il coordinamento di più passaggi tra più dispositivi o nodi gestiti. 

Pur essendo scritto con la sintassi di Windows PowerShell e avviato da Windows PowerShell, un flusso di lavoro viene elaborato da Windows Workflow Foundation. I vantaggi di un flusso di lavoro rispetto a un normale script includono l'esecuzione simultanea di un'azione su più dispositivi e del recupero automatico dagli errori. 

> [!NOTE]
> Uno script di flusso di lavoro di PowerShell è molto simile a uno script Windows PowerShell, ma presenta alcune differenze significative che possono generare confusione in un nuovo utente. È pertanto consigliabile scrivere i runbook usando il flusso di lavoro di PowerShell solo se è necessario usare [checkpoint](#use-checkpoints-in-a-workflow). 

Per informazioni dettagliate sugli argomenti inclusi in questo articolo, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11)).

## <a name="use-workflow-keyword"></a>Usare la parola chiave Workflow

Il primo passaggio per la conversione di uno script PowerShell in un flusso di lavoro consiste nell'includerlo con la parola chiave `Workflow`. Un flusso di lavoro inizia con la parola chiave `Workflow` seguita dal corpo dello script racchiuso tra parentesi graffe. Il nome del flusso di lavoro segue la parola chiave `Workflow`, come illustrato nella sintassi seguente:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Il nome del flusso di lavoro deve corrispondere al nome del Runbook di Automazione. Se il runbook viene importato, il nome del file deve corrispondere al nome del flusso di lavoro e terminare con l'estensione **ps1**.

Per aggiungere parametri al flusso di lavoro, usare la parola chiave `Param` esattamente come si farebbe con uno script.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Differenze tra il codice del flusso di lavoro PowerShell e il codice dello script PowerShell

Il codice del flusso di lavoro di PowerShell è quasi identico al relativo codice di script, fatta eccezione per alcune modifiche significative. Le sezioni seguenti descrivono le modifiche che è necessario apportare a uno script di PowerShell per l'esecuzione in un flusso di lavoro.

### <a name="activities"></a>attività

Un'attività è un'attività specifica in un flusso di lavoro eseguita in una sequenza. Il flusso di lavoro di Windows PowerShell converte automaticamente molti dei cmdlet di Windows PowerShell in attività quando esegue un flusso di lavoro. Quando si specifica uno di questi cmdlet in un runbook, l'attività corrispondente viene eseguita da Windows Workflow Foundation. 

Se un cmdlet non presenta un'attività corrispondente, il flusso di lavoro di Windows PowerShell lo esegue automaticamente in un'attività [InlineScript](#use-inlinescript). Alcuni cmdlet sono esclusi e non possono essere usati in un flusso di lavoro, a meno che non vengano inclusi in modo esplicito in un blocco InlineScript. Per altre informazioni, vedere [Uso delle attività nei flussi di lavoro di script](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

Le attività dei flussi di lavoro condividono un set di parametri comuni per configurare il proprio funzionamento. Vedere [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Parametri posizionali

Non è possibile utilizzare i parametri posizionali con attività e cmdlet in un flusso di lavoro. Pertanto, è necessario utilizzare nomi di parametro. Si consideri il codice seguente per rilevare tutti i servizi in esecuzione:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Se si tenta di eseguire questo codice in un flusso di lavoro, viene visualizzato un messaggio simile a `Parameter set cannot be resolved using the specified named parameters.` Per correggere questo problema, specificare il nome del parametro, come nell'esempio seguente:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Oggetti deserializzati

Gli oggetti nei flussi di lavoro vengono deserializzati, ovvero le proprietà sono ancora disponibili, ma non i relativi metodi.  Ad esempio, si consideri il codice PowerShell riportato di seguito che consente di arrestare un servizio utilizzando il metodo `Stop` dell'oggetto `Service`.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Se si tenta di eseguire questa operazione in un flusso di lavoro, viene visualizzato un errore che informa `Method invocation is not supported in a Windows PowerShell Workflow.`

Un'opzione consiste nell'eseguire il wrapping di queste due righe di codice in un blocco [InlineScript](#use-inlinescript). In questo caso, `Service` rappresenta un oggetto servizio all'interno del blocco.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Un'altra opzione consiste nell'utilizzare un altro cmdlet con la stessa funzionalità del metodo, se disponibile. Nel presente esempio il cmdlet `Stop-Service` fornisce la stessa funzionalità del metodo `Stop` e consente di usare il codice seguente per un flusso di lavoro.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Usare InlineScript

L'attività `InlineScript` risulta utile quando è necessario eseguire uno o più comandi come script PowerShell tradizionale anziché come flusso di lavoro PowerShell.  Mentre i comandi di un flusso di lavoro vengono inviati a Windows Workflow Foundation per l'elaborazione, i comandi in un blocco InlineScript vengono elaborati da Windows PowerShell.

InlineScript usa la sintassi indicata di seguito.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

È possibile restituire l'output di un InlineScript assegnando l'output a una variabile. Nell'esempio seguente viene arrestato un servizio e viene quindi restituito il nome del servizio.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

È possibile passare i valori in un blocco InlineScript, ma è necessario utilizzare il modificatore di ambito **$Using** .  L'esempio seguente è identico all'esempio precedente, ad eccezione del fatto che il nome del servizio viene fornito da una variabile.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Sebbene le attività InlineScript possano essere critiche in determinati flussi di lavoro, non supportano i costrutti del flusso di lavoro. È consigliabile usarle solo se necessario per i motivi seguenti:

* Non è possibile usare [checkpoint](#use-checkpoints-in-a-workflow) all'interno di un blocco InlineScript. Se si verifica un errore nel blocco, deve riprendere dall'inizio del blocco.
* Non è possibile usare l'[esecuzione parallela](#use-parallel-processing) all'interno di un blocco InlineScriptBlock.
* L'attività InlineScript influisce sulla scalabilità del Runbook perché vincola la sessione di Windows PowerShell per l'intera lunghezza del blocco InlineScript.

Per altre informazioni sull'uso di InlineScript, vedere [Esecuzione dei comandi di Windows PowerShell in un flusso di lavoro](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) e [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Usare l'elaborazione parallela

Uno dei vantaggi offerti dai flussi di lavoro di Windows PowerShell consiste nella possibilità di eseguire un set di comandi in parallelo anziché in sequenza, come accade invece con uno script tipico.

È possibile usare la parola chiave `Parallel` per creare un blocco di script con più comandi che vengono eseguiti contemporaneamente. Viene usata la sintassi indicata di seguito. In questo caso Activity1 e Activity2 iniziano nello stesso momento. Activity3 viene avviata solo dopo il completamento di Activity1 e Activity2.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Ad esempio, considerare i seguenti comandi PowerShell che consentono di copiare più file in una destinazione di rete. Questi comandi vengono eseguiti in sequenza in modo che finisca la copia di un file prima che venga avviata la successiva.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Nel flusso di lavoro seguente vengono eseguiti questi stessi comandi in parallelo in modo che inizi la copia di tutti nello stesso momento.  Solo dopo che la copia è terminata, viene visualizzato il messaggio di completamento.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

È possibile usare il costrutto `ForEach -Parallel` per elaborare i comandi per ogni elemento di una raccolta simultaneamente. Gli elementi della raccolta vengono elaborati in parallelo, mentre i comandi nel blocco di script vengono eseguiti in sequenza. Il processo usa la sintassi indicata di seguito. In questo caso l'avvio di Activity1 è simultaneo a quello di tutti gli altri elementi della raccolta. Per ogni elemento, Activity2 inizia al termine di Activity1. Activity3 si avvia solo dopo il completamento di Activity1 e Activity2 per tutti gli elementi. Si usa il parametro `ThrottleLimit` per limitare il parallelismo. Un valore troppo elevato del parametro `ThrottleLimit` può causare problemi. Il valore ideale di `ThrottleLimit` dipende da numerosi fattori dell'ambiente in uso. Iniziare usando un valore basso e provare diversi valori crescenti fino a quando non si trova quello adatto per la situazione specifica.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

L'esempio seguente è simile all'esempio precedente in cui vengono copiati i file in parallelo.  In questo caso, viene visualizzato un messaggio per ogni file al termine della copia.  Solo dopo che la copia è terminata, viene visualizzato il messaggio di completamento finale.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Si sconsiglia l'esecuzione di Runbook figlio in parallelo poiché è stato dimostrato che potrebbe causare risultati inaffidabili. L'output dal runbook figlio talvolta non viene visualizzato e le impostazioni di un runbook figlio possono influire sugli altri runbook figlio paralleli. Variabili quali `VerbosePreference`, `WarningPreference` e altre ancora potrebbero non propagarsi ai runbook figlio. Se poi il runbook figlio modifica questi valori, potrebbe non essere possibile ripristinarli correttamente dopo una chiamata.

## <a name="use-checkpoints-in-a-workflow"></a>Usare i checkpoint in un flusso di lavoro

Un checkpoint è uno snapshot dello stato corrente del flusso di lavoro che include i valori correnti per le variabili e gli output generati fino al punto corrispondente. Se un flusso di lavoro termina con errori o viene sospeso, inizia dall'ultimo checkpoint alla successiva esecuzione, anziché dall'inizio. 

È possibile impostare un checkpoint in un flusso di lavoro con l'attività `Checkpoint-Workflow`. Automazione di Azure include la cosiddetta [condivisione equa](automation-runbook-execution.md#fair-share), una funzionalità per cui qualsiasi runbook eseguito per tre ore viene scaricato per consentire l'esecuzione di altri runbook. In ultima istanza, il runbook scaricato viene ricaricato. In questa fase riprende l'esecuzione dall'ultimo checkpoint rilevato nel runbook.

Per garantire che il runbook venga completato, è necessario aggiungere checkpoint a intervalli di esecuzione inferiori a tre ore. Se durante ogni esecuzione viene aggiunto un nuovo checkpoint e il runbook viene eliminato dopo tre ore a causa di un errore, il runbook viene ripreso indefinitamente.

Nell'esempio seguente si verifica un'eccezione dopo Activity2 con la conseguente sospensione del flusso di lavoro. Quando il flusso di lavoro viene nuovamente avviato, inizierà con l'esecuzione di Activity2, poiché questa attività si trovava immediatamente dopo l'ultimo checkpoint impostato.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Impostare checkpoint in un flusso di lavoro dopo attività che possono essere soggette a eccezioni e che non devono essere ripetute se riprende il flusso di lavoro. Si consideri ad esempio un flusso di lavoro con cui viene creata una macchina virtuale. È possibile impostare un checkpoint sia prima che dopo i comandi di creazione della macchina virtuale. Se la creazione ha esito negativo, i comandi vengono ripetuti se il flusso di lavoro viene riavviato. Se il flusso di lavoro ha esito negativo dopo il completamento della creazione, la macchina virtuale non viene creata nuovamente quando riprenderà il flusso di lavoro.

Nell'esempio seguente vengono copiati più file in un percorso di rete e viene impostato un checkpoint dopo ogni file.  Se il percorso di rete viene perso, il flusso di lavoro termina con errori.  Quando viene riavviato, riprende dall'ultimo checkpoint. Solo i file che sono già stati copiati vengono ignorati.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Poiché le credenziali nome utente non vengono rese permanenti dopo la chiamata dell'attività [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) o dopo l'ultimo checkpoint, è necessario impostare le credenziali su Null e quindi recuperarle di nuovo dall'archivio di asset dopo la chiamata di `Suspend-Workflow` o del checkpoint.  In caso contrario, è possibile che venga visualizzato il messaggio di errore seguente: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Il codice seguente mostra come gestire questa situazione nei runbook del flusso di lavoro di PowerShell.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Per runbook PowerShell non grafici, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli, anche se è stato appena creato un nuovo account di Automazione. L'uso di questi cmdlet non è necessario se si esegue l'autenticazione usando un account RunAs configurato con un'entità servizio.

Per altre informazioni sui checkpoint, vedere l'articolo relativo all' [aggiunta di checkpoint a un flusso di lavoro di script](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui runbook del flusso di lavoro PowerShell, vedere [Esercitazione: Creare un runbook del flusso di lavoro PowerShell](learn/automation-tutorial-runbook-textual.md).
