<properties 
   pageTitle="Asset di tipo variabile in Automazione di Azure"
   description="Gli asset di tipo variabile sono valori disponibili per tutti i runbook in Automazione di Azure. Questo articolo illustra nel dettaglio le variabili e spiega come usarle nella creazione testuale e grafica."
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
   ms.date="05/21/2015"
   ms.author="bwren" />

# Asset di tipo variabile in Automazione di Azure

Gli asset di tipo variabile sono valori disponibili per tutti i runbook nell'account di automazione. Possono essere creati, modificati e recuperati dal portale di Azure, da Windows PowerShell e dall'interno di un runbook. Le variabili di automazione sono utili per gli scenari seguenti:

- Condivisione di un valore tra più runbook.

- Condivisione di un valore tra più processi dello stesso runbook

- Gestione di un valore dal portale o dalla riga di comando di Windows PowerShell usata dai runbook

Le variabili di automazione vengono salvate in maniera permanente in modo da continuare a essere disponibili anche in caso di errore del runbook. In tal modo, è anche possibile impostare in un runbook un valore che verrà quindi usato da un altro runbook oppure dallo stesso runbook alla successiva esecuzione.

Quando si crea una variabile, è possibile specificare che venga archiviata in modalità crittografata. Se una variabile viene crittografata, viene archiviata in modo sicuro in Automazione di Azure e non è possibile recuperarne il valore tramite il cmdlet [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx) incluso nel modulo Azure PowerShell. L'unico modo in cui è possibile recuperare un valore crittografato è dall'attività **Get-AutomationVariable** in un runbook.

>[AZURE.NOTE]Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure tramite una chiave univoca generata per ogni account di automazione. La chiave viene crittografata da un certificato master e archiviata in Automazione di Azure. Prima dell'archiviazione di un asset sicuro, la chiave per l'account di automazione viene decrittografata mediante il certificato master e viene quindi usata per crittografare l'asset.

## Tipi di variabile

Quando si crea una variabile con il portale di Azure, è necessario selezionare un tipo di dati dall'elenco a discesa, in modo che nel portale sia visualizzato il controllo appropriato per immettere il valore della variabile. La variabile non è limitata a questo tipo di dati, ma è necessario impostarla usando Windows PowerShell se si intende specificare un valore di tipo diverso. Se si specifica **Non definito**, il valore della variabile verrà impostato su **$null** e sarà necessario impostare il valore con il cmdlet [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) o l'attività **Set-AutomationVariable**. Non è possibile creare o modificare il valore per un tipo di variabile complesso nel portale, ma è possibile indicare un valore di qualsiasi tipo usando Windows PowerShell. I tipi complessi verranno restituiti come [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

È possibile archiviare più valori in una singola variabile creando una matrice o una tabella hash e salvandola nella variabile.

## Cmdlet e attività flusso di lavoro

I cmdlet della tabella seguente vengono usati per creare e gestire variabili di automazione con Windows PowerShell. Sono inclusi nel [modulo Azure PowerShell](powershell-install-configure.md), disponibile per l'uso nei runbook di automazione.

|Cmdlet|Descrizione|
|:---|:---|
|[Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx)|Recupera il valore di una variabile esistente.|
|[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx)|Crea una nuova variabile e ne imposta il valore.|
|[Remove-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913775.aspx)|Rimuove una variabile esistente.|
|[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx)|Imposta il valore di una variabile esistente.|

Le attività flusso di lavoro incluse nella tabella seguente vengono usate per accedere alle variabili di automazione in un runbook. Sono disponibili per l'uso solo in un runbook e non vengono fornite come parte del modulo Azure PowerShell.

|Attività flusso di lavoro|Descrizione|
|:---|:---|
|Get-AutomationVariable|Recupera il valore di una variabile esistente.|
|Set-AutomationVariable|Imposta il valore di una variabile esistente.|

>[AZURE.NOTE]È consigliabile evitare di usare le variabili nel parametro –Name di **Get-AutomationVariable** in un runbook perché ciò può complicare l'individuazione delle dipendenze tra i runbook e le variabili di automazione durante la fase di progettazione.

## Creazione di una nuova variabile di automazione

### Per creare una nuova variabile con il portale di Azure

1. Dall'account di automazione fare clic su **Asset** nella parte superiore della finestra.
1. Nella parte inferiore della finestra fare clic su **Aggiungi impostazione**.
1. Fare clic su **Aggiungi variabile**.
1. Completare la procedura guidata e selezionare la casella di controllo per salvare la nuova variabile.


### Per creare una nuova variabile con il portale di anteprima di Azure

1. Dall'account di automazione fare clic sulla parte **Asset** per aprire il pannello **Asset**.
1. Fare clic sulla parte **Variabili** per aprire il pannello **Variabili**.
1. Fare clic su **Aggiungi variabile** nella parte superiore del pannello.
1. Completare il modulo e fare clic su **Crea** per salvare la nuova variabile.


### Per creare una nuova variabile con Windows PowerShell

Il cmdlet [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) crea una nuova variabile e ne imposta il valore iniziale. È possibile recuperare il valore usando [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx). Se il valore è di tipo semplice, verrà restituito lo stesso tipo. Se è di tipo complesso, verrà restituito un **PSCustomObject**.

I comandi di esempio seguenti mostrano come creare una variabile di tipo stringa e restituirne il valore.


	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' –Encrypted $false –Value 'My String'
	$string = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

I comandi di esempio seguenti mostrano come creare una variabile di tipo complesso e restituirne le relative proprietà. In questo caso, viene usato un oggetto macchina virtuale recuperato da **Get-AzureVM**.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
	
	$vmValue = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
	$vmName = $ vmValue.Name
	$vmIpAddress = $ vmValue.IpAddress



## Uso di una variabile in un runbook

Usare l'attività **Set-AutomationVariable** per impostare il valore di una variabile di automazione in un runbook e **Get-AutomationVariable** per recuperarlo. Non è consigliabile usare i cmdlet **Set-AzureAutomationVariable** o **Get-AzureAutomationVariable** in un runbook perché sono meno efficienti rispetto alle attività flusso di lavoro. Non è inoltre possibile recuperare il valore delle variabili sicure con **Get-AzureAutomationVariable**. L'unico modo per creare una nuova variabile dall'interno di un runbook consiste nell'usare il cmdlet [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx).


### Esempi di runbook testuali

#### Impostazione e recupero di un valore semplice da una variabile

I comandi di esempio seguenti mostrano come impostare e recuperare una variabile in un runbook testuale. In questo esempio si presuppone che le variabili di tipo integer denominate *NumberOfIterations* e *NumberOfRunnings* e una variabile di tipo stringa denominata *SampleMessage* siano già state create.

	$NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
	$NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
	$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
	
	Write-Output "Runbook has been run $NumberOfRunnings times."
	
	for ($i = 1; $i -le $NumberOfIterations; $i++) {
	   Write-Output "$i`: $SampleMessage"
	}
	Set-AutomationVariable –Name NumberOfRunnings –Value (NumberOfRunngs += 1)


#### Impostazione e recupero di un oggetto complesso in una variabile

Il codice di esempio seguente mostra come aggiornare una variabile con un valore complesso in un runbook testuale. In questo esempio una macchina virtuale di Azure viene recuperata con **Get-AzureVM** e salvata in una variabile di automazione esistente. Come illustrato in [Tipi di variabile](#variable-types), viene archiviata come PSCustomObject.

	$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
	Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


Nel codice seguente il valore viene recuperato dalla variabile e usato per avviare la macchina virtuale.

	$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
	if ($vmObject.PowerState -eq 'Stopped') {
	   Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
	}


#### Impostazione e recupero di una raccolta in una variabile

Il codice di esempio seguente mostra come usare una variabile con una raccolta di valori complessi in un runbook testuale. In questo esempio più macchine virtuali di Azure vengono recuperate con **Get-AzureVM** e salvate in una variabile di automazione esistente. Come illustrato in [Tipi di variabile](#variable-types), viene archiviata una raccolta di PSCustomObject.

	$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

Nel codice seguente la raccolta viene recuperata dalla variabile e usata per avviare ogni macchina virtuale.

	$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
	ForEach ($vmValue in $vmValues)
	{
	   if ($vmValue.PowerState -eq 'Stopped') {
	      Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
	   }
	}

### Esempi di runbook grafici

In un runbook grafico è possibile aggiungere **Get-AutomationVariable** o **Set-AutomationVariable** facendo clic con il pulsante destro del mouse sulla variabile nel riquadro Libreria dell'editor grafico e scegliendo l'attività desiderata.

![Aggiungere una variabile al canvas](media/automation-variables/variable-add-canvas.png)

#### Impostazione dei valori in una variabile

La figura seguente illustra attività di esempio per aggiornare una variabile con un valore semplice in un runbook grafico. In questo esempio un'unica macchina virtuale di Azure viene recuperata con **Get-AzureVM** e il nome computer viene salvato in una variabile di automazione esistente di tipo stringa. Non è importante se il [collegamento è una pipeline o una sequenza](automation-graphical-authoring-intro.md#links-and-workflow) poiché è previsto solo un singolo oggetto nell'output.

![Impostare una variabile semplice](media/automation-variables/set-simple-variable.png)

La figura seguente illustra le attività usate per aggiornare una variabile con un valore complesso in un runbook grafico. L'unica differenza rispetto all'esempio precedente consiste nel fatto di non specificare un percorso del campo per l'output nell'attività **Set-AutomationVariable** in modo che venga archiviato l'oggetto e non soltanto una delle relative proprietà. Come illustrato in [Tipi di variabile](#variable-types), viene archiviato un PSCustomObject.

![Impostare una variabile complessa](media/automation-variables/set-complex-variable.png)

L'immagine seguente illustra una funzionalità simile all'esempio precedente, con più macchine virtuali salvate nella variabile. In questo caso deve essere usato un[ ](automation-graphical-authoring-intro.md#links-and-workflow)collegamento di tipo sequenza in modo che l'attività **Set-AutomationVariable** riceva l'intero set di macchine virtuali come un'unica raccolta. Se è stato usato un[ ](automation-graphical-authoring-intro.md#links-and-workflow)collegamento di tipo pipeline, l'attività **Set-AutomationVariable** verrà eseguita separatamente per ogni oggetto, pertanto verrà salvata solo l'ultima macchina virtuale della raccolta. Come illustrato in [Tipi di variabile](#variable-types), viene archiviata una raccolta di PSCustomObject.

![Impostare una variabile per una raccolta complessa](media/automation-variables/set-complex-variable-collection.png)

#### Recupero di valori da una variabile

La figura seguente illustra attività di esempio che recuperano e usano una variabile in un runbook grafico. La prima attività recupera le macchine virtuali salvate nella variabile nell'esempio precedente. Il collegamento deve essere una [pipeline](automation-graphical-authoring-intro.md#links-and-workflow) in modo che l'attività **Start-AzureVM** venga eseguita una volta per ogni oggetto inviato dall'attività **Get-AutomationVariable**. Il funzionamento sarà lo stesso, indipendentemente dal fatto che nella variabile siano archiviati uno o più oggetti. L'attività **Start-AzureVM** usa le proprietà del PSCustomObject che rappresenta ogni macchina virtuale.

![Ottenere una variabile complessa](media/automation-variables/get-complex-variable.png)

La figura seguente illustra come filtrare gli oggetti archiviati in una variabile in un runbook grafico. Al collegamento dell'esempio precedente viene aggiunta una [condizione](automation-graphical-authoring-intro.md#links-and-workflow) per filtrare esclusivamente le macchine virtuali arrestate al momento dell'impostazione della variabile.

![Ottenere una variabile complessa filtrata](media/automation-variables/get-complex-variable-filter.png)


## Articoli correlati

- [Collegamenti nella creazione grafica](automation-graphical-authoring-intro.md#links-and-workflow)

<!---HONumber=58-->