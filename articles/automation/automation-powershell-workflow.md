<properties 
   pageTitle="Informazioni sul flusso di lavoro PowerShell"
   description="Questo articolo è concepito come una lezione rapida per autori che hanno familiarità con PowerShell per comprendere le differenze specifiche tra PowerShell e il flusso di lavoro PowerShell."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/12/2016"
   ms.author="bwren" />  

# Informazioni sul flusso di lavoro di Windows PowerShell

I Runbook in Automazione di Azure vengono implementati come flussi di lavoro di Windows PowerShell. Un flusso di lavoro di Windows PowerShell è simile a uno script Windows PowerShell, ma presenta alcune differenze significative che possono generare confusione in un nuovo utente. In questo articolo, destinato agli utenti che hanno già familiarità con PowerShell, vengono illustrati brevemente i concetti richiesti per convertire uno script di PowerShell nel relativo flusso di lavoro da poter utilizzare in un Runbook.

Un flusso di lavoro è una sequenza di passaggi programmati connessi che eseguono operazioni a esecuzione prolungata o richiedono il coordinamento di più passaggi tra più dispositivi o nodi gestiti. I vantaggi di un flusso di lavoro rispetto a un normale script includono la possibilità di eseguire simultaneamente un'azione su più dispositivi e di eseguire il ripristino automatico dagli errori. Un flusso di lavoro di Windows PowerShell è uno script di Windows PowerShell che si basa su Windows Workflow Foundation. Pur essendo scritto con la sintassi di Windows PowerShell e avviato da Windows PowerShell, un flusso di lavoro viene elaborato da Windows Workflow Foundation.

Per informazioni dettagliate sugli argomenti inclusi in questo articolo, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## Tipi di Runbook

Esistono tre tipi di Runbook in Automazione di Azure: Runbook del *flusso di lavoro PowerShell*, di *PowerShell* e *grafici*. È possibile definire il tipo di Runbook durante la creazione e non è possibile convertirlo in altro tipo al termine della creazione.

I Runbook del flusso di lavoro PowerShell e i Runbook di PowerShell sono destinati agli utenti che preferiscono utilizzare direttamente il codice di PowerShell mediante l'editor di testo in Automazione di Azure o un editor offline, ad esempio PowerShell ISE. Se si sta creando un Runbook del flusso di lavoro di PowerShell, è necessario comprendere le informazioni contenute in questo articolo.

I Runbook grafici consentono di creare un Runbook mediante gli stessi cmdlet e attività, ma utilizzando un'interfaccia grafica che nasconde la complessità del flusso di lavoro PowerShell sottostante. I concetti illustrati in questo articolo, ad esempio i checkpoint e l'esecuzione parallela vengono mantenuti per Runbook con interfaccia grafica, ma non sarà necessario preoccuparsi di una sintassi dettagliata.

## Struttura di base di un flusso di lavoro

Il primo passaggio per la conversione di uno script PowerShell in un flusso di lavoro consiste nell'includerlo con la parola chiave **Flusso di lavoro**. Un flusso di lavoro di Windows PowerShell inizia con la parola chiave **Flusso di lavoro** seguita dal corpo dello script racchiuso tra parentesi graffe. Il nome del flusso di lavoro segue la parola chiave **Workflow**, come mostrato nella sintassi seguente.

    Workflow Test-Workflow
    {
       <Commands>
    }

Il nome del flusso di lavoro deve corrispondere al nome del Runbook di Automazione. Se il Runbook viene importato, il nome del file deve corrispondere al nome del flusso di lavoro e terminare con l'estensione ps1.

Per aggiungere parametri al flusso di lavoro, utilizzare la parola chiave **Param** esattamente come si farebbe con uno script.

## Modifiche al codice

Il codice del flusso di lavoro di PowerShell è quasi identico al relativo codice di script, fatta eccezione per alcune modifiche significative. Nelle sezioni seguenti vengono descritte le modifiche che sarà necessario apportare a uno script di PowerShell per l'esecuzione in un flusso di lavoro.

### Attività

Un'attività è un'operazione specifica in un flusso di lavoro. Così come uno script è costituito da uno o più comandi, un flusso di lavoro è costituito da una o più attività eseguite in sequenza. Il flusso di lavoro di Windows PowerShell converte automaticamente molti dei cmdlet di Windows PowerShell in attività quando esegue un flusso di lavoro. Quando si specifica uno di questi cmdlet in un Runbook, l'attività corrispondente viene in realtà eseguita da Windows Workflow Foundation. I cmdlet senza un'attività corrispondente vengono eseguiti automaticamente dal flusso di lavoro di Windows PowerShell in un'attività [InlineScript](#inlinescript). Esiste un set di cmdlet che sono esclusi e non possono essere usati in un flusso di lavoro, a meno che non vengano inclusi in modo esplicito in un blocco InlineScript. Per altri dettagli su questi concetti, vedere l'articolo relativo all'[uso di attività in flussi di lavoro di script](http://technet.microsoft.com/library/jj574194.aspx).

Le attività dei flussi di lavoro condividono un set di parametri comuni per configurare il proprio funzionamento. Per informazioni dettagliate sui parametri comuni dei flussi di lavoro, vedere [about\_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### Parametri posizionali

Non è possibile utilizzare i parametri posizionali con attività e cmdlet in un flusso di lavoro. Tutto ciò significa che è necessario utilizzare nomi di parametro.

Si consideri, ad esempio, il codice seguente per rilevare tutti i servizi in esecuzione.

	 Get-Service | Where-Object {$_.Status -eq "Running"}

Se si tenta di eseguire lo stesso codice in un flusso di lavoro, verrà visualizzato un messaggio simile al seguente: ""Impossibile risolvere il set di parametri mediante i parametri denominati specificati". Per risolvere il problema, è sufficiente fornire il nome del parametro come indicato di seguito.

	Workflow Get-RunningServices
	{
		Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
	}

### Oggetti deserializzati

Gli oggetti nei flussi di lavoro vengono deserializzati. Ciò significa che le relative proprietà sono ancora disponibili, ma non i relativi metodi. Ad esempio, si consideri il codice PowerShell riportato di seguito che consente di arrestare un servizio utilizzando il metodo Stop dell'oggetto Service.

	$Service = Get-Service -Name MyService
	$Service.Stop()

Se si tenta di eseguire questa operazione in un flusso di lavoro, si otterrà un errore indicante che la "chiamata al metodo non è supportata in un flusso di lavoro di Windows PowerShell".

È possibile eseguire il wrapping di queste due righe di codice in un blocco [InlineScript](#InlineScript), nel qual caso $Service sarebbe un oggetto Service all'interno del blocco.

	Workflow Stop-Service
	{
		InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
		}
	} 

Un'altra opzione consiste nell'utilizzare un altro cmdlet che esegue la stessa funzionalità del metodo, se disponibile. Nel caso del presente esempio, il cmdlet Stop-Service fornisce la stessa funzionalità del metodo Stop e consente di utilizzare quanto segue per un flusso di lavoro.

	Workflow Stop-MyService
	{
		$Service = Get-Service -Name MyService
		Stop-Service -Name $Service.Name
	}


## InlineScript

L'attività **InlineScript** risulta utile quando è necessario eseguire uno o più comandi tradizionali come script piuttosto che flusso di lavoro PowerShell. Mentre i comandi di un flusso di lavoro vengono inviati a Windows Workflow Foundation per l'elaborazione, i comandi in un blocco InlineScript vengono elaborati da Windows PowerShell.

InlineScript usa la sintassi seguente.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

È possibile restituire l'output di un InlineScript assegnando l'output a una variabile. Nell'esempio seguente viene arrestato un servizio e viene quindi restituito il nome del servizio.

	Workflow Stop-MyService
	{
		$Output = InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


È possibile passare i valori in un blocco InlineScript, ma è necessario utilizzare il modificatore di ambito **$Using**. L'esempio seguente è identico all'esempio precedente, ad eccezione del fatto che il nome del servizio viene fornito da una variabile.

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


Anche se possono rivelarsi di importanza critica in alcuni flussi di lavoro, le attività di InlineScript non supportano costrutti di flussi di lavoro e devono essere usate solo se necessario per i motivi seguenti:

- Non è possibile utilizzare [Checkpoint](#Checkpoints) all'interno di un blocco InlineScript. Se si verifica un errore nel blocco, è necessario riprendere dall'inizio del blocco.
- Non è possibile utilizzare [l'esecuzione parallela](#parallel-execution) all'interno di un InlineScriptBlock.
- L'attività InlineScript influisce sulla scalabilità del Runbook perché vincola la sessione di Windows PowerShell per l'intera lunghezza del blocco InlineScript.

Per altri dettagli sull'uso di InlineScript, vedere [Esecuzione dei comandi di Windows PowerShell in un flusso di lavoro](http://technet.microsoft.com/library/jj574197.aspx) e [about\_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## Elaborazione parallela

Uno dei vantaggi offerti dai flussi di lavoro di Windows PowerShell consiste nella possibilità di eseguire un set di comandi in parallelo anziché in sequenza, come accade invece con uno script tipico.

È possibile usare la parola chiave **Parallel** per creare un blocco di script con più comandi che verranno eseguiti contemporaneamente. In questo scenario viene usata la sintassi seguente. In questo caso l'esecuzione di Activity1 e Activity2 sarà simultanea. Activity3 si avvierà solo dopo il completamento di Activity1 e Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Ad esempio, considerare i seguenti comandi PowerShell che consentono di copiare più file in una destinazione di rete. Questi comandi vengono eseguiti in sequenza in modo che finisca la copia di un file prima che venga avviata la successiva.

	$Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
	$Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
	$Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

Nel flusso di lavoro seguente vengono eseguiti questi stessi comandi in parallelo in modo che inizi la copia di tutti nello stesso momento. Solo dopo che stati tutti copiati completamente, viene visualizzato il messaggio di completamento.

	Workflow Copy-Files
	{
		Parallel 
		{
			$Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
		}

		Write-Output "Files copied."
	}


È possibile usare il costrutto **ForEach -Parallel** per elaborare i comandi per ogni elemento di una raccolta simultaneamente. Gli elementi della raccolta vengono elaborati in parallelo, mentre i comandi nel blocco di script vengono eseguiti in sequenza. In questo scenario viene usata la sintassi seguente. In questo caso l'avvio di Activity1 sarà simultaneo a quello di tutti gli altri elementi della raccolta. Per ogni elemento, Activity2 si avvierà al completamento di Activity1. Activity3 si avvierà solo dopo il completamento di Activity1 e Activity2 per tutti gli elementi.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

L'esempio seguente è simile all'esempio precedente in cui vengono copiati i file in parallelo. In questo caso, viene visualizzato un messaggio per ogni file al termine della copia. Solo dopo che stati tutti copiati completamente, viene visualizzato il messaggio di completamento finale.

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach -Parallel ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
		}
		
		Write-Output "All files copied."
	}

> [AZURE.NOTE]  Si sconsiglia l'esecuzione di Runbook figlio in parallelo poiché è stato dimostrato che potrebbe causare risultati inaffidabili. L'output dal Runbook figlio talvolta non verrà visualizzato e le impostazioni di un Runbook figlio possono influire sugli altri Runbook figlio paralleli.


## Checkpoint

Un *checkpoint* è uno snapshot dello stato corrente del flusso di lavoro che include il valore corrente per le variabili e gli output generati fino al punto corrispondente. Se un flusso di lavoro termina con errori o viene sospeso, alla successiva esecuzione verrà avviato dall'ultimo checkpoint anziché dall'inizio del flusso di lavoro. È possibile impostare un checkpoint in un flusso di lavoro con l'attività **Checkpoint-Workflow**.

Nel codice di esempio seguente si verifica un'eccezione dopo Activity2 con la conseguente sospensione del Runbook. Quando il flusso di lavoro viene nuovamente avviato, inizierà con l'esecuzione di Activity2, poiché questa attività si trovava immediatamente dopo l'ultimo checkpoint impostato.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

È consigliabile impostare checkpoint in un flusso di lavoro dopo attività che possono essere soggette a eccezioni e che non devono essere ripetute se riprende il flusso di lavoro. Si consideri ad esempio un flusso di lavoro con cui viene creata una macchina virtuale. È possibile impostare un checkpoint sia prima che dopo i comandi di creazione della macchina virtuale. Se la creazione ha esito negativo, i comandi verrebbero ripetuti se il flusso di lavoro viene riavviato. Se il flusso di lavoro ha esito negativo dopo il completamento della creazione, la macchina virtuale non verrà creata nuovamente quando verrà ripreso il flusso di lavoro.

Nell'esempio seguente vengono copiati più file in un percorso di rete e viene impostato un checkpoint dopo ogni file. Se il percorso di rete viene perso, il flusso di lavoro terminerà con errori. Quando viene avviato nuovamente, verrà ripreso dall'ultimo checkpoint, vale a dire che solo i file che sono già stati copiati verranno ignorati.

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
			Checkpoint-Workflow
		}
		
		Write-Output "All files copied."
	}

Poiché le credenziali UserName non vengono rese permanenti dopo la chiamata all'attività [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) o dopo l'ultimo checkpoint, è necessario impostare le credenziali su Null e quindi recuperarle di nuovo dall'archivio di asset dopo la chiamata a **Suspend-Workflow** o al checkpoint. In caso contrario, è possibile che venga visualizzato il messaggio di errore seguente: *Impossibile riprendere il processo del flusso di lavoro perché i dati di persistenza non sono stati salvati completamente o i dati di persistenza salvati sono danneggiati. È necessario riavviare il flusso di lavoro.*

Il codice seguente mostra come gestire questa situazione nei runbook del flusso di lavoro di PowerShell.

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


Ciò non è necessario se si esegue l'autenticazione usando un account RunAs configurato con un'entità servizio.

Per altre informazioni sui checkpoint, vedere l'articolo relativo all'[aggiunta di checkpoint a un flusso di lavoro di script](http://technet.microsoft.com/library/jj574114.aspx).


## Passaggi successivi

- Per iniziare a usare i runbook del flusso di lavoro di PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)

<!---HONumber=AcomDC_0914_2016-->