<properties 
   pageTitle="Concetti relativi ai runbook di Automazione di Azure"
   description="Descrive i concetti di base che è necessario conoscere per creare runbook in Automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Concetti relativi ai runbook di Automazione di Azure

I runbook in Automazione di Azure vengono implementati come flussi di lavoro di Windows PowerShell. Questa sezione fornisce una breve panoramica delle funzionalità critiche dei flussi di lavoro comuni ai runbook di Automazione. Dettagli completi sui flussi di lavoro sono disponibili in [Informazioni sul flusso di lavoro di Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).


## Flussi di lavoro di Windows PowerShell

Un flusso di lavoro è una sequenza di passaggi programmati connessi che eseguono operazioni a esecuzione prolungata o richiedono il coordinamento di più passaggi tra più dispositivi o nodi gestiti. I vantaggi di un flusso di lavoro rispetto a un normale script includono la possibilità di eseguire simultaneamente un'azione su più dispositivi e di eseguire il ripristino automatico dagli errori. Un flusso di lavoro di Windows PowerShell è uno script di Windows PowerShell che si basa su Windows Workflow Foundation. Pur essendo scritto con la sintassi di Windows PowerShell e avviato da Windows PowerShell, un flusso di lavoro viene elaborato da Windows Workflow Foundation.

### Struttura di base

Un flusso di lavoro di Windows PowerShell inizia con la parola chiave **Workflow** seguita dal corpo dello script racchiuso tra parentesi graffe. Il nome del flusso di lavoro segue la parola chiave **Workflow**, come mostrato nella sintassi seguente. Il nome del flusso di lavoro corrisponde al nome del runbook di Automazione.

    Workflow Test-Runbook
    {
       <Commands>
    }

Per aggiungere parametri al flusso di lavoro, usare la parola chiave **Param** come mostrato nella sintassi seguente. Il portale di gestione chiederà all'utente di specificare valori per questi parametri all'avvio del runbook. Questo esempio usa l'attributo facoltativo Parameter, che specifica se il parametro è o non è obbligatorio.

    Workflow Test-Runbook
    {
      Param
      (
       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>,

       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>
      )
      <Commands>
    }

### Denominazione

Il nome del flusso di lavoro deve essere conforme al formato Verbo-Nome usato come standard in Windows PowerShell. Per un elenco dei verbi approvati da usare, vedere l'articolo relativo ai [verbi approvati per i comandi di Windows PowerShell](http://msdn.microsoft.com/library/windows/desktop/ms714428(v=vs.85).aspx). Il nome del flusso di lavoro deve corrispondere al nome del runbook di Automazione. Se il runbook viene importato, il nome del file deve corrispondere al nome del flusso di lavoro e terminare con l'estensione ps1.

### Limitazioni

Per un elenco completo delle limitazioni e delle differenze di sintassi tra i flussi di lavoro di Windows PowerShell e Windows PowerShell, vedere l'articolo relativo alle [differenze sintattiche tra script e flussi di lavoro di script](http://technet.microsoft.com/library/jj574140.aspx).

## Attività

Un'attività è un'operazione specifica in un flusso di lavoro. Così come uno script è costituito da uno o più comandi, un flusso di lavoro è costituito da una o più attività eseguite in sequenza. Il flusso di lavoro di Windows PowerShell converte automaticamente molti dei cmdlet di Windows PowerShell in attività quando esegue un flusso di lavoro. Quando si specifica uno di questi cmdlet in un runbook, l'attività corrispondente viene in realtà eseguita da Windows Workflow Foundation. I cmdlet senza un'attività corrispondente vengono eseguiti automaticamente dal flusso di lavoro di Windows PowerShell in un'attività [InlineScript](#inlinescript). Esiste un set di cmdlet che sono esclusi e non possono essere usati in un flusso di lavoro, a meno che non vengano inclusi in modo esplicito in un blocco InlineScript. Per altri dettagli su questi concetti, vedere l'articolo relativo all'[uso di attività in flussi di lavoro di script](http://technet.microsoft.com/library/jj574194.aspx).

Le attività dei flussi di lavoro condividono un set di parametri comuni per configurare il proprio funzionamento. Per informazioni dettagliate sui parametri comuni dei flussi di lavoro, vedere [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

## Moduli di integrazione

Un *modulo di integrazione* è un pacchetto che contiene un modulo di Windows PowerShell e che può essere importato in Automazione di Azure. I cmdlet contenuti in moduli di integrazione importati in Automazione di Azure sono automaticamente disponibili per tutti i runbook dello stesso account di Automazione. Poiché si basa su Windows PowerShell 4.0, Automazione di Azure supporta il caricamento automatico di moduli, pertanto i cmdlet dei moduli installati possono essere usati senza essere importati nello script con [Import-Module](http://technet.microsoft.com/library/hh849725.aspx).

## Esecuzione parallela

Uno dei vantaggi offerti dai flussi di lavoro di Windows PowerShell consiste nella possibilità di eseguire un set di comandi in parallelo anziché in sequenza, come accade invece con uno script tipico. Questo si rivela particolarmente utile per i runbook, che in questo modo possono eseguire più azioni che richiedono tempo significativo per il completamento. Un runbook ad esempio può effettuare il provisioning di un set di macchine virtuali. Anziché eseguire ogni processo di provisioning in sequenza uno dopo l'altro, è possibile eseguire le azioni simultaneamente migliorando l'efficienza generale. Il runbook proseguirà solo dopo il completamento di tutte le azioni.

È possibile usare la parola chiave **Parallel** per creare un blocco di script con più comandi che verranno eseguiti contemporaneamente. In questo scenario viene usata la sintassi seguente. In questo caso l'esecuzione di Activity1 e Activity2 sarà simultanea. Activity3 si avvierà solo dopo il completamento di Activity1 e Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

È possibile usare il costrutto **ForEach -Parallel** per elaborare i comandi per ogni elemento di una raccolta simultaneamente. Gli elementi della raccolta vengono elaborati in parallelo, mentre i comandi nel blocco di script vengono eseguiti in sequenza. In questo scenario viene usata la sintassi seguente. In questo caso l'avvio di Activity1 sarà simultaneo a quello di tutti gli altri elementi della raccolta. Per ogni elemento, Activity2 si avvierà al completamento di Activity1. Activity3 si avvierà solo dopo il completamento di Activity1 e Activity2 per tutti gli elementi.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

La parola chiave **Sequence** viene usata per eseguire comandi in sequenza in un blocco di script **Parallel**. Il blocco di script **Sequence** viene eseguito in parallelo con altri comandi, ma i comandi nel blocco vengono eseguiti in sequenza. In questo scenario viene usata la sintassi seguente. In questo caso Activity1, Activity2 e Activity3 si avvieranno contemporaneamente. Activity4 si avvierà solo al completamento di Activity3. Activity5 si avvierà solo al completamento di tutte le altre attività.

    Parallel
    {
      <Activity1>
      <Activity2>

      Sequence 
      { 
        <Activity3>  
        <Activity4>
      }

    }
    <Activity5>

## Checkpoint

Un *checkpoint* è uno snapshot dello stato corrente del flusso di lavoro che include il valore corrente per le variabili e gli output generati fino al punto corrispondente. L'ultimo checkpoint da completare in un runbook viene salvato nel database di Automazione in modo che il flusso di lavoro possa riprendere se si verificano problemi, come ad esempio un guasto del computer durante il runtime. Senza un checkpoint, il flusso di lavoro ripartirebbe dall'inizio. I dati del checkpoint vengono rimossi al completamento del processo del runbook.

È possibile impostare un checkpoint in un flusso di lavoro con l'attività **Checkpoint-Workflow**. Quando si include questa attività in un runbook, viene creato automaticamente un checkpoint. Se il runbook viene sospeso da un'eccezione, quando viene ripreso il processo si riparte dal punto dell'ultimo checkpoint impostato.

Nel codice di esempio seguente si verifica un'eccezione dopo Activity2 con la conseguente sospensione del runbook. Quando riprende, il processo inizia eseguendo Activity2, poiché si trova immediatamente dopo l'ultimo checkpoint impostato.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

È consigliabile impostare checkpoint in un runbook dopo attività che possono essere soggette a eccezioni e che non devono essere ripetute se riprende il runbook. Si consideri ad esempio un runbook che crea una macchina virtuale. È possibile impostare un checkpoint sia prima che dopo i comandi di creazione della macchina virtuale. Se la creazione non riesce, i comandi vengono ripetuti quando riparte il runbook. Se invece la creazione riesce ma il runbook ha esito negativo più avanti, la macchina virtuale non viene ricreata quando riparte il runbook.

Per altre informazioni sui checkpoint, vedere l'articolo relativo all'[aggiunta di checkpoint a un flusso di lavoro di script](http://technet.microsoft.com/library/jj574114.aspx).

## Sospensione di un flusso di lavoro

È possibile forzare la sospensione di un runbook con l'attività **Suspend-Workflow**. Questa attività imposterà un checkpoint e determinerà la sospensione immediata del flusso di lavoro. La sospensione di un flusso di lavoro è utile per i runbook che possono richiedere l'esecuzione di un passaggio manuale prima che venga eseguito un altro set di attività.

Per altre informazioni sulla sospensione di un flusso di lavoro, vedere l'articolo relativo alla [sospensione di un flusso di lavoro](http://technet.microsoft.com/library/jj574175.aspx).

## InlineScript

L'attività **InlineScript** esegue un blocco di comandi in uno script di PowerShell tradizionale anziché un flusso di lavoro di PowerShell e restituisce l'output al flusso di lavoro. Mentre i comandi di un flusso di lavoro vengono inviati a Windows Workflow Foundation per l'elaborazione, i comandi in un blocco InlineScript vengono elaborati da Windows PowerShell. L'attività usa i parametri comuni dei flussi di lavoro standard incluso **PSCredential**, che consente di specificare che il blocco di codice deve essere eseguito con credenziali alternative.

InlineScript usa la sintassi seguente.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Anche se possono rivelarsi di importanza critica in alcuni runbook, le attività di InlineScript non supportano costrutti di flussi di lavoro e devono essere usate solo se necessario per i motivi seguenti:

- Non è possibile usare checkpoint in un blocco InlineScript. Se si verifica un errore nel blocco, è necessario riprendere dall'inizio del blocco.
- InlineScript ha effetti sulla scalabilità del runbook perché vincola la sessione di Windows PowerShell per l'intera lunghezza del blocco InlineScript.
- Attività come Get-AutomationVariable e Get-AutomationPSCredential non sono disponibili in un blocco InlineScript.  

Se non è necessario usare InlineScript, è consigliabile ridurne al minimo l'ambito. Se ad esempio un runbook esegue l'iterazione di una raccolta applicando la stessa operazione a ogni elemento, il loop deve verificarsi all'esterno di InlineScript. In questo modo si otterranno i vantaggi seguenti:

- È possibile creare un [checkpoint](#checkpoints) nel flusso di lavoro dopo ogni iterazione. Se il processo viene sospeso oppure viene interrotto e ripreso, il loop potrà riprendere.
- È possibile usare **ForEach –Parallel** per gestire gli elementi della raccolta simultaneamente.

Se non si usa InlineScript in un runbook, tenere presenti i suggerimenti seguenti:

- È comunque possibile passare valori nello script con il modificatore di ambito **$Using**. Ad esempio, una variabile denominata $abc impostata all'esterno di InlineScript diventerebbe $using:abc all'interno di InlineScript.

- Per restituire l'output da InlineScript, assegnare l'output a una variabile ed eseguire l'output dei dati da restituire nel flusso di output. Il seguente esempio assegna la stringa "hi" a una variabile denominata $output.

	<pre><code>$output = InlineScript { Write-Output "hi" }</code></pre>

- Evitare di definire flussi di lavoro nell'ambito di InlineScript. Anche se alcuni flussi di lavoro sembrano funzionare correttamente, questo non è uno scenario testato. È possibile pertanto che vengano visualizzati messaggi di errore poco chiari o che si ottenga un comportamento imprevisto.

Per altri dettagli sull'uso di InlineScript, vedere [Esecuzione dei comandi di Windows PowerShell in un flusso di lavoro](http://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## Articoli correlati

- [Creazione o importazione di un runbook](http://technet.microsoft.com/library/dn919921.aspx)

<!---HONumber=58-->