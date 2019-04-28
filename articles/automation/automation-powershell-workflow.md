---
title: Informazioni sul flusso di lavoro di PowerShell per Automazione di Azure
description: Questo articolo è concepito come una lezione rapida per autori che hanno familiarità con PowerShell per comprendere le differenze specifiche tra PowerShell e il flusso di lavoro di PowerShell e i concetti applicabili ai runbook di Automazione.
services: automation
ms.service: automation
ms.subservice: process-automation
author: WenJason
ms.author: v-jay
origin.date: 12/14/2018
ms.date: 04/01/2019
ms.topic: conceptual
manager: digimobile
ms.openlocfilehash: c5764c36a646b9639c0eb6463c39b9f014c4272d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738333"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Informazioni sui concetti chiave del flusso di lavoro di PowerShell per i runbook di Automazione

I Runbook in Automazione di Azure vengono implementati come flussi di lavoro di Windows PowerShell.  Un flusso di lavoro di Windows PowerShell è simile a uno script Windows PowerShell, ma presenta alcune differenze significative che possono generare confusione in un nuovo utente.  Anche se lo scopo di questo articolo è illustrare come scrivere runbook con il flusso di lavoro di PowerShell, è consigliabile scrivere runbook con PowerShell, a meno che non siano necessari checkpoint.  Esistono numerose differenze sintattiche nella creazione dei runbook del flusso di lavoro di PowerShell e queste differenze richiedono maggiore impegno nella scrittura di flussi di lavoro efficaci.

Un flusso di lavoro è una sequenza di passaggi programmati connessi che eseguono operazioni a esecuzione prolungata o richiedono il coordinamento di più passaggi tra più dispositivi o nodi gestiti. I vantaggi di un flusso di lavoro rispetto a un normale script includono la possibilità di eseguire simultaneamente un'azione su più dispositivi e di eseguire il ripristino automatico dagli errori. Un flusso di lavoro di Windows PowerShell è uno script di Windows PowerShell che usa Windows Workflow Foundation. Pur essendo scritto con la sintassi di Windows PowerShell e avviato da Windows PowerShell, un flusso di lavoro viene elaborato da Windows Workflow Foundation.

Per informazioni dettagliate sugli argomenti inclusi in questo articolo, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Struttura di base di un flusso di lavoro

Il primo passaggio per la conversione di uno script PowerShell in un flusso di lavoro consiste nell'includerlo con la parola chiave **Flusso di lavoro** .  Un flusso di lavoro di Windows PowerShell inizia con la parola chiave **Flusso di lavoro** seguita dal corpo dello script racchiuso tra parentesi graffe. Il nome del flusso di lavoro segue la parola chiave **Workflow**, come illustrato nella sintassi seguente:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Il nome del flusso di lavoro deve corrispondere al nome del Runbook di Automazione. Se il runbook viene importato, il nome del file deve corrispondere al nome del flusso di lavoro e terminare con l'estensione *ps1*.

Per aggiungere parametri al flusso di lavoro, utilizzare la parola chiave **Param** esattamente come si farebbe con uno script.

## <a name="code-changes"></a>Modifiche al codice

Il codice del flusso di lavoro di PowerShell è quasi identico al relativo codice di script, fatta eccezione per alcune modifiche significative.  Le sezioni seguenti descrivono le modifiche che è necessario apportare a uno script di PowerShell per l'esecuzione in un flusso di lavoro.

### <a name="activities"></a>attività

Un'attività è un'operazione specifica in un flusso di lavoro. Così come uno script è costituito da uno o più comandi, un flusso di lavoro è costituito da una o più attività eseguite in sequenza. Il flusso di lavoro di Windows PowerShell converte automaticamente molti dei cmdlet di Windows PowerShell in attività quando esegue un flusso di lavoro. Quando si specifica uno di questi cmdlet in un runbook, l'attività corrispondente viene eseguita da Windows Workflow Foundation. I cmdlet senza un'attività corrispondente vengono eseguiti automaticamente dal flusso di lavoro di Windows PowerShell in un'attività [InlineScript](#inlinescript) . Esiste un set di cmdlet che sono esclusi e non possono essere usati in un flusso di lavoro, a meno che non vengano inclusi in modo esplicito in un blocco InlineScript. Per altri dettagli su questi concetti, vedere l'articolo relativo all' [uso di attività in flussi di lavoro di script](https://technet.microsoft.com/library/jj574194.aspx).

Le attività dei flussi di lavoro condividono un set di parametri comuni per configurare il proprio funzionamento. Per informazioni dettagliate sui parametri comuni dei flussi di lavoro, vedere [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parametri posizionali

Non è possibile utilizzare i parametri posizionali con attività e cmdlet in un flusso di lavoro.  Tutto ciò significa che è necessario utilizzare nomi di parametro.

Si consideri, ad esempio, il codice seguente per rilevare tutti i servizi in esecuzione.

```powershell
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Se si tenta di eseguire lo stesso codice in un flusso di lavoro, viene visualizzato un messaggio simile al seguente: "Impossibile risolvere il set di parametri mediante i parametri denominati specificati".  Per risolvere il problema, specificare il nome del parametro come indicato di seguito.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Oggetti deserializzati

Gli oggetti nei flussi di lavoro vengono deserializzati.  Ciò significa che le relative proprietà sono ancora disponibili, ma non i relativi metodi.  Ad esempio, si consideri il codice PowerShell riportato di seguito che consente di arrestare un servizio utilizzando il metodo Stop dell'oggetto Service.

```powershell
$Service = Get-Service -Name MyService
$Service.Stop()
```

Se si tenta di eseguire questa operazione in un flusso di lavoro, viene visualizzato un errore indicante che la "chiamata al metodo non è supportata in un flusso di lavoro di Windows PowerShell".

È possibile eseguire il wrapping di queste due righe di codice in un blocco [InlineScript](#inlinescript). In questo caso, $Service sarà un oggetto Service all'interno del blocco.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Un'altra opzione consiste nell'utilizzare un altro cmdlet che esegue la stessa funzionalità del metodo, se disponibile.  Nel presente esempio il cmdlet Stop-Service fornisce la stessa funzionalità del metodo Stop e consente di usare quanto segue per un flusso di lavoro.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

L'attività **InlineScript** risulta utile quando è necessario eseguire uno o più comandi tradizionali come script piuttosto che flusso di lavoro PowerShell.  Mentre i comandi di un flusso di lavoro vengono inviati a Windows Workflow Foundation per l'elaborazione, i comandi in un blocco InlineScript vengono elaborati da Windows PowerShell.

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

Anche se possono rivelarsi di importanza critica in alcuni flussi di lavoro, le attività di InlineScript non supportano costrutti di flussi di lavoro e devono essere usate solo se necessario per i motivi seguenti:

* Non è possibile usare [checkpoint](#checkpoints) all'interno di un blocco InlineScript. Se si verifica un errore nel blocco, è necessario riprendere dall'inizio del blocco.
* Non è possibile usare l'[esecuzione parallela](#parallel-processing) all'interno di un blocco InlineScriptBlock.
* L'attività InlineScript influisce sulla scalabilità del Runbook perché vincola la sessione di Windows PowerShell per l'intera lunghezza del blocco InlineScript.

Per altre informazioni sull'uso di InlineScript, vedere [Esecuzione dei comandi di Windows PowerShell in un flusso di lavoro](https://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Elaborazione parallela

Uno dei vantaggi offerti dai flussi di lavoro di Windows PowerShell consiste nella possibilità di eseguire un set di comandi in parallelo anziché in sequenza, come accade invece con uno script tipico.

È possibile usare la parola chiave **Parallel** per creare un blocco di script con più comandi che vengono eseguiti contemporaneamente. Viene usata la sintassi indicata di seguito. In questo caso Activity1 e Activity2 iniziano nello stesso momento. Activity3 viene avviata solo dopo il completamento di Activity1 e Activity2.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Ad esempio, considerare i seguenti comandi PowerShell che consentono di copiare più file in una destinazione di rete.  Questi comandi vengono eseguiti in sequenza in modo che finisca la copia di un file prima che venga avviata la successiva.

```powershell
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

È possibile usare il costrutto **ForEach -Parallel** per elaborare i comandi per ogni elemento di una raccolta simultaneamente. Gli elementi della raccolta vengono elaborati in parallelo, mentre i comandi nel blocco di script vengono eseguiti in sequenza. Viene usata la sintassi indicata di seguito. In questo caso l'avvio di Activity1 è simultaneo a quello di tutti gli altri elementi della raccolta. Per ogni elemento, Activity2 inizia al termine di Activity1. Activity3 si avvia solo dopo il completamento di Activity1 e Activity2 per tutti gli elementi. Si usa il parametro `ThrottleLimit` per limitare il parallelismo. Un valore troppo elevato del parametro `ThrottleLimit` può causare problemi. Il valore ideale di `ThrottleLimit` dipende da numerosi fattori dell'ambiente in uso. È opportuno iniziare usando un valore basso e provare diversi valori crescenti fino a quando non si trova quello adatto per la situazione specifica.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

L'esempio seguente è simile all'esempio precedente in cui vengono copiati i file in parallelo.  In questo caso, viene visualizzato un messaggio per ogni file al termine della copia.  Solo dopo che stati tutti copiati completamente, viene visualizzato il messaggio di completamento finale.

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
> Si sconsiglia l'esecuzione di Runbook figlio in parallelo poiché è stato dimostrato che potrebbe causare risultati inaffidabili. L'output dal runbook figlio talvolta non viene visualizzato e le impostazioni di un runbook figlio possono influire sugli altri runbook figlio paralleli. È possibile che variabili come $VerbosePreference, $WarningPreference e altre non vengano propagate ai runbook figlio. Se poi il runbook figlio modifica questi valori, potrebbe non essere possibile ripristinarli correttamente dopo una chiamata.

## <a name="checkpoints"></a>Checkpoint

Un *checkpoint* è uno snapshot dello stato corrente del flusso di lavoro che include il valore corrente per le variabili e gli output generati fino al punto corrispondente. Se un flusso di lavoro termina con errori o viene sospeso, alla successiva esecuzione verrà avviato dall'ultimo checkpoint anziché dall'inizio del flusso di lavoro.  È possibile impostare un checkpoint in un flusso di lavoro con l'attività **Checkpoint-Workflow** . Automazione di Azure offre una funzionalità denominata [condivisione equa](automation-runbook-execution.md#fair-share), in cui viene scaricato i runbook in esecuzione per 3 ore per consentire altri runbook per l'esecuzione. Infine, il runbook scaricato verrà ricaricato e questo caso, l'esecuzione dall'ultimo checkpoint eseguito il runbook verrà ripresa. Per garantire che il runbook verrà infine completata, è necessario aggiungere checkpoint a intervalli di esecuzione per meno di 3 ore. Se durante ogni esecuzione viene aggiunto un nuovo checkpoint, e se il runbook Ottiene eliminato dopo 3 ore a causa di un errore, quindi il runbook verrà ripresa per un periodo illimitato.

Nel codice di esempio seguente si verifica un'eccezione dopo Activity2 con la conseguente sospensione del Runbook. Quando il flusso di lavoro viene nuovamente avviato, inizierà con l'esecuzione di Activity2, poiché questa attività si trovava immediatamente dopo l'ultimo checkpoint impostato.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

È consigliabile impostare checkpoint in un flusso di lavoro dopo attività che possono essere soggette a eccezioni e che non devono essere ripetute se riprende il flusso di lavoro. Si consideri ad esempio un flusso di lavoro con cui viene creata una macchina virtuale. È possibile impostare un checkpoint sia prima che dopo i comandi di creazione della macchina virtuale. Se la creazione ha esito negativo, i comandi verrebbero ripetuti se il flusso di lavoro viene riavviato. Se il flusso di lavoro ha esito negativo dopo il completamento della creazione, la macchina virtuale non verrà creata nuovamente quando verrà ripreso il flusso di lavoro.

Nell'esempio seguente vengono copiati più file in un percorso di rete e viene impostato un checkpoint dopo ogni file.  Se il percorso di rete viene perso, il flusso di lavoro termina con errori.  Quando viene avviato nuovamente, verrà ripreso dall'ultimo checkpoint, vale a dire che solo i file che sono già stati copiati vengono ignorati.

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

Poiché le credenziali nome utente non vengono rese permanenti dopo la chiamata dell'attività [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) o dopo l'ultimo checkpoint, è necessario impostare le credenziali su Null e quindi recuperarle di nuovo dall'archivio di asset dopo la chiamata di **Suspend-Workflow** o del checkpoint.  In caso contrario, è possibile che venga visualizzato un messaggio di errore simile al seguente: *Impossibile riprendere il processo del flusso di lavoro perché i dati di persistenza non sono stati salvati completamente o i dati di persistenza salvati sono danneggiati. È necessario riavviare il flusso di lavoro.*

Il codice seguente mostra come gestire questa situazione nei runbook del flusso di lavoro di PowerShell.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzureAutomationCredential -Name "MyCredential"
    $null = Connect-AzureRmAccount -Credential $Cred

    $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzureRmVm -VM $Vm -Location "ChinaNorth" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzureAutomationCredential -Name "MyCredential"
        $null = Connect-AzureRmAccount -EnvironmentName AzureChinaCloud -Credential $Cred
        }
}
```

> [!IMPORTANT]
> **Add-AzureRmAccount** è ora un alias per **Connect-AzureRMAccount**. Quando si esegue la ricerca tra gli elementi della libreria, se **Connect-AzureRMAccount** non viene visualizzato, è possibile usare **Add-AzureRmAccount** oppure aggiornare i moduli nell'account di Automazione.

Ciò non è necessario se si esegue l'autenticazione usando un account RunAs configurato con un'entità servizio.

Per altre informazioni sui checkpoint, vedere l'articolo relativo all' [aggiunta di checkpoint a un flusso di lavoro di script](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)

