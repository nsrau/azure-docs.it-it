<properties 
   pageTitle="Runbook figlio in automazione di Azure | Microsoft Azure"
   description="Descrive i diversi metodi per avviare un runbook in automazione di Azure da un altro runbook e condividere informazioni tra di essi."
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
   ms.date="09/17/2015"
   ms.author="bwren" />

# Runbook figlio in Automazione di Azure


È buona norma in automazione di Azure scrivere runbook riutilizzabili e modulari con una funzione discreta che può essere utilizzata da altri runbook. Un runbook padre chiama spesso uno o più runbook figlio per eseguire la funzionalità richiesta. Esistono due modi per chiamare un runbook figlio e ognuno presenta differenze che è necessario comprendere in modo che sia possibile determinare quale sarà migliore per i diversi scenari.

##  Richiamare un runbook figlio con esecuzione inline

Per richiamare un runbook inline da un altro runbook, utilizzare il nome del runbook e fornire valori per i relativi parametri, esattamente come si farebbe per un'attività o un cmdlet. Tutti i runbook nello stesso account di automazione sono disponibili a tutti gli altri per essere utilizzati come quanto segue. Il runbook padre attenderà il completamento del runbook figlio prima di passare alla riga successiva e gli output vengono restituiti direttamente all'elemento padre.

Quando si richiama un runbook inline, esso viene eseguito nello stesso processo del runbook padre. Nella cronologia del processo non verrà indicato il runbook figlio eseguito. Eventuali eccezioni e flussi di output dal runbook figlio verranno associati all'elemento padre. Ciò comporta un minor numero di processi e li rende più semplici per rilevare e risolvere poiché le eccezioni generate dal runbook figlio e i relativi output del flusso sono associati al processo padre.

Quando viene pubblicato un runbook, tutti i runbook figlio chiamati devono già essere pubblicati. Questo avviene perché l'automazione di Azure crea un'associazione con i runbook figlio quando viene compilato un runbook. In caso contrario, il runbook padre sembrerà pubblicato correttamente ma al suo avvio verrà generata un'eccezione. In questo caso, è possibile ripubblicare il runbook padre per fare riferimento in modo corretto ai runbook figlio. Non è necessario ripubblicare il runbook padre se alcuni runbook figlio sono stati modificati in quanto l'associazione sarà già stata creata.

I parametri di un runbook figlio chiamato inline possono essere costituiti da qualsiasi tipo di dati, inclusi gli oggetti complessi e non esiste alcuna [serializzazione JSON](automation-starting-a-runbook.md#runbook-parameters) come quando si avvia il runbook utilizzando il portale di gestione di Azure o con il cmdlet Start-AzureAutomationRunbook.

### Tipi di runbook

Non è possibile utilizzare un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o un [runbook grafico](automation-runbook-types.md#graphical-runbooks) come elemento figlio in un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) tramite l’esecuzione inline. Analogamente, non è possibile usare un runbook di PowerShell come elemento figlio con l'esecuzione inline in un runbook del flusso di lavoro PowerShell o in un runbook grafico. I runbook PowerShell possono utilizzare solo un altro PowerShell come figlio. I runbook grafici e del flusso di lavoro PowerShell si possono utilizzare tra loro come runbook figlio.

Quando si richiama un runbook figlio grafico o del flusso di lavoro PowerShell tramite l’esecuzione inline, è sufficiente utilizzare il nome del runbook. Quando si richiama un runbook figlio PowerShell, è necessario che il suo nome sia preceduto da *.\* per specificare che lo script si trova nella directory locale.

### Esempio

Nell'esempio seguente viene richiamato un runbook figlio di test che accetta tre parametri, un oggetto complesso, un numero intero e un valore booleano. L'output del runbook figlio viene assegnato a una variabile. In questo caso, il runbook figlio è un runbook del flusso di lavoro PowerShell

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	$output = Test-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Di seguito si trova lo stesso esempio con un runbook di PowerShell come elemento figlio.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	$output = .\Test-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true


##  Avvio di un runbook figlio utilizzando cmdlet

È possibile usare [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/dn690259.aspx) per avviare un Runbook come descritto in [Avviare un runbook con Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Quando si avvia un runbook figlio da un cmdlet, il runbook padre si sposterà alla riga successiva non appena viene creato un processo per il runbook figlio. Se è necessario recuperare l'output dal runbook, sarà necessario accedere al processo utilizzando [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx).

Verrà eseguito il processo da un runbook figlio avviato con un cmdlet in un processo separato dal runbook padre. Questo comporta più processi rispetto al richiamo del runbook inline e li rende più difficili da rilevare. L'elemento padre può avviare più runbook figlio senza attendere che ognuno di essi sia completato. Per questa stessa tipologia di esecuzione parallela che chiama l’inline del runbook figlio, il runbook padre dovrà usare la [parola chiave parallela](automation-powershell-workflow.md#parallel-processing).

I parametri per un runbook figlio avviato con un cmdlet vengono forniti come una tabella hash, come descritto in [Parametri di Runbook](automation-starting-a-runbook.md#runbook-parameters). Possono essere utilizzati solo tipi di dati semplici. Se il runbook dispone di un parametro con un tipo di dati complessi, deve essere chiamato inline.

### Esempio

Nell'esempio seguente viene avviato un runbook figlio con parametri e si attende il suo completamento. Una volta completato, il relativo output viene raccolto dal processo dal runbook padre.

	$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" –Parameters $params
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

[Start-ChildRunbook](http://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Automation-1ac858a9) è un runbook di supporto disponibile nella Raccolta TechNet per avviare un runbook da un cmdlet. Questo consente di attendere che il runbook figlio sia completato e di recuperare il relativo output. Oltre all'ambiente di automazione di Azure, questo runbook può essere utilizzato come riferimento per l'utilizzo di runbook e di processi mediante i cmdlet. Il runbook di supporto stesso deve essere chiamato inline perché richiede un parametro hashtable per accettare i valori dei parametri per il runbook figlio.


## Confronto di metodi per chiamare un runbook figlio

Nella tabella seguente vengono riepilogate le differenze tra i due metodi per chiamare un runbook da un altro runbook.

| | Inline| Cmdlet|
|:---|:---|:---|
|Job|I runbook figlio vengono eseguiti nello stesso processo dell’elemento padre.|Viene creato un processo separato per il runbook figlio.|
|Esecuzione|Il runbook padre attende il completamento del runbook figlio prima di continuare.|Il runbook padre continua immediatamente dopo l'avvio del runbook figlio.|
|Output|Il runbook padre può ottenere output direttamente dal runbook figlio.|Il runbook padre deve recuperare l'output dal processo del runbook figlio.|
|Parametri|I valori per i parametri di runbook figlio vengono specificati separatamente e possono utilizzare qualsiasi tipo di dati.|I valori per i parametri di runbook figlio devono essere combinati in una singola tabella di hash e possono includere solo tipi di dati semplici, matrice e oggetto che sfruttano la serializzazione JSON.|
|Account di automazione|Il runbook padre può utilizzare solo runbook figlio nello stesso account di automazione.|Il runbook padre può utilizzare runbook figlio da qualsiasi account di automazione dalla stessa sottoscrizione di Azure e anche da una sottoscrizione diversa se si dispone di una connessione ad essa.|
|Pubblicazione|Il runbook figlio deve essere pubblicato prima della pubblicazione del runbook padre.|Il runbook figlio deve essere pubblicato prima che il runbook padre venga avviato.|

## Articoli correlati

- [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)
- [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0204_2016-->