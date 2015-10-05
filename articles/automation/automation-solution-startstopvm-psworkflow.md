<properties 
   pageTitle="Avvio e arresto delle macchine virtuali con Automazione di Azure - Flusso di lavoro PowerShell | Microsoft Azure"
   description="Versione grafica della soluzione di Automazione di Azure che include runbook per l'avvio e l'arresto delle macchine virtuali classiche."
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

# Soluzione di Automazione di Azure - Avvio e arresto delle macchine virtuali

Questa soluzione di Automazione di Azure include runbook per l'avvio e l'arresto di macchine virtuali classiche. È possibile usare questa soluzione per le operazioni seguenti:

- Usare i runbook senza modifiche nell'ambiente in uso. 
- Modificare i runbook per eseguire funzionalità personalizzate.  
- Chiamare i runbook da un altro runbook come parte di una soluzione completa. 
- Usare i runbook come esercitazioni per acquisire familiarità con i concetti di creazione dei runbook. 

> [AZURE.SELECTOR]
- [Graphical](automation-solution-startstopvm-graphical.md)
- [PowerShell Workflow](automation-solution-startstopvm-psworkflow.md)

Questa è una versione della soluzione con runbook del flusso di lavoro PowerShell. È disponibile anche usando [runbook grafici](automation-solutions-startstopvm-graphical.md).

## Come ottenere la soluzione

Questa soluzione è costituita da due runbook del flusso di lavoro PowerShell che è possibile scaricare dai collegamenti seguenti. Per i collegamenti ai runbook grafici, vedere la [versione grafica](automation-solutions-startstopvm-graphical.md) di questa soluzione.

| Runbook | Collegamento | Tipo | Descrizione |
|:---|:---|:---|:---|
| Start-AzureVMs | [Avviare le macchine virtuali classiche di Azure](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Flusso di lavoro PowerShell | Avvia tutte le macchine virtuali classiche in una sottoscrizione di Azure o tutte le macchine virtuali con un nome di servizio specifico. |
| Stop-AzureVMs | [Arrestare le macchine virtuali classiche di Azure](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Flusso di lavoro PowerShell | Arresta tutte le macchine virtuali in un account di automazione o tutte le macchine virtuali con un nome di servizio specifico. |


## Installazione della soluzione

### 1\. Installare i runbook

Dopo aver scaricato i runbook, è possibile importarli usando la procedura descritta nell'articolo relativo all'[importazione di un runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### 2\. Esaminare la descrizione e i requisiti
I runbook includono testo di supporto commentato contenente una descrizione e gli asset necessari. Le stesse informazioni sono disponibili anche in questo articolo.

### 3\. Configurare gli asset
I runbook richiedono gli asset seguenti, che devono essere creati e popolati con i valori appropriati.

| Tipo di risorsa | Nome dell’asset | Descrizione |
|:---|:---|:---|
| Credenziali | AzureCredential | Contiene le credenziali per un account che dispone delle autorizzazioni per avviare e arrestare le macchine virtuali nella sottoscrizione di Azure. In alternativa, è possibile specificare un altro asset di tipo credenziali nel parametro **Credential** dell'attività **Add-AzureAccount**. |
| Variabile | AzureSubscriptionId | Contiene l'ID sottoscrizione della sottoscrizione di Azure. |

## Uso della soluzione

### Parametri

Ogni runbook è associato ai parametri seguenti. È necessario fornire valori per tutti i parametri obbligatori ed è possibile facoltativamente specificare i valori per gli altri parametri a seconda delle esigenze.

| Parametro | Tipo | Obbligatorio | Descrizione |
|:---|:---|:---|
| ServiceName | stringa | No | Se viene specificato un valore, verranno avviate o arrestate tutte le macchine virtuali con lo stesso nome di servizio. Se invece non viene specificato alcun valore, verranno avviate o arrestate tutte le macchine virtuali classiche della sottoscrizione di Azure. |
| AzureSubscriptionIdAssetName | stringa | No | Contiene il nome dell'[asset di tipo variabile](#installing-the-solution) che include l'ID sottoscrizione della sottoscrizione di Azure. Se non si specifica un valore, verrà usato *AzureSubscriptionId*. |
| AzureCredentialAssetName | stringa | No | Contiene il nome dell'[asset di tipo credenziali](#installing-the-solution) che include le credenziali per il runbook da usare. Se non si specifica un valore, verrà usato *AzureCredential*. |

### Avvio dei runbook

È possibile usare uno dei metodi descritti in [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md) per avviare uno dei runbook di questa soluzione.

I comandi di esempio seguenti usano Windows PowerShell per eseguire **StartAzureVMs** per avviare tutte le macchine virtuali con nome di servizio *MyVMService*.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### Output

I runbook [restituiranno un messaggio](automation-runbook-output-and-messages.md) per ogni macchina virtuale indicando se l'istruzione di avvio o di arresto è stata inviata correttamente. È possibile cercare una stringa specifica nell'output per determinare il risultato per ogni runbook. Nella tabella seguente sono elencate le stringhe di output possibili.

| Runbook | Condizione | Message |
|:---|:---|:---|
| Start-AzureVMs | Macchina virtuale già in esecuzione | MyVM is already running |
| Start-AzureVMs | Richiesta di avvio per la macchina virtuale inviata correttamente | MyVM has been started |
| Start-AzureVMs | Richiesta di avvio per la macchina virtuale non riuscita | MyVM failed to start |
| Stop-AzureVMs | Macchina virtuale già in esecuzione | MyVM is already stopped |
| Stop-AzureVMs | Richiesta di avvio per la macchina virtuale inviata correttamente | MyVM has been started |
| Stop-AzureVMs | Richiesta di avvio per la macchina virtuale non riuscita | MyVM failed to start |

Il frammento di codice di un runbook seguente ad esempio tenta di avviare tutte le macchine virtuali con nome di servizio *MyServiceName*. Se una delle azioni di avvio ha esito negativo, è possibile eseguire le azioni di errore.

	$results = Start-AzureVMs -ServiceName "MyServiceName"
	foreach ($result in $results) {
		if ($result -like "* has been started" ) {
			# Action to take in case of success.
		}
		else {
			# Action to take in case of error.
		}
	}


## Scomposizione dettagliata

Di seguito viene riportata una scomposizione dettagliata dei runbook di questa soluzione. È possibile usare queste informazioni per personalizzare i runbook o solo come esercitazione.

### Parametri

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Il flusso di lavoro inizia ottenendo i valori per i [parametri di input](#using-the-solution). Se non sono specificati i nomi degli asset verranno usati i nomi predefiniti.

### Output

    # Returns strings with status messages
    [OutputType([String])]

Questa riga dichiara che l'output del runbook sarà una stringa. Non è obbligatoria, ma è una procedura consigliata se il runbook viene usato come [runbook figlio](automation-child-runbooks.md), affinché un runbook padre conosca il tipo di output previsto.

### Autenticazione

	# Connect to Azure and select the subscription to work against
	$Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
	$null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
	$SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

Le righe successive impostano le [credenziali](automation-configuring.md#configuring-authentication-to-azure-resources) e la sottoscrizione di Azure che verranno usate per il resto del runbook. Viene usato prima **Get-AutomationPSCredential** per ottenere l'asset contenente le credenziali con l'accesso per l'avvio e l'arresto delle macchine virtuali nella sottoscrizione di Azure. **Add-AzureAccount** usa quindi questo asset per impostare le credenziali. L'output viene assegnato a una variabile fittizia in modo da non essere incluso nell'output del runbook.

L'asset di tipo variabile con l'ID sottoscrizione viene quindi recuperato con **Get-AutomationVariable** e il set di sottoscrizioni con **Select-AzureSubscription**.

### Ottenere le macchine virtuali

	# If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
	{ 
		$VMs = Get-AzureVM -ServiceName $ServiceName
	}
    else 
	{ 
		$VMs = Get-AzureVM
	}

**Get-AzureVM** viene usato per recuperare le macchine virtuali che verranno usate dal runbook. Se viene specificato un valore nella variabile di input **ServiceName**, verranno recuperate solo le macchine virtuali con lo stesso nome di servizio. Se invece la variabile **ServiceName** è vuota, verranno recuperate tutte le macchine virtuali.

### Avviare/arrestare le macchine virtuali e inviare l'output

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
		if ($VM.PowerState -eq "Started")
		{
			# The VM is already started, so send notice
			Write-Output ($VM.InstanceName + " is already running")
		}
		else
		{
			# The VM needs to be started
        	$StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

	        if ($StartRtn.OperationStatus -ne 'Succeeded')
	        {
				# The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
	        }
			else
			{
				# The VM started, so send notice
				Write-Output ($VM.InstanceName + " has been started")
			}
		}
    }

Le righe successive eseguono i passaggi per ogni macchina virtuale. Viene prima verificata l'impostazione di **PowerState** della macchina virtuale per controllare se è già in esecuzione o arrestata, a seconda del runbook. Se lo stato è già quello di destinazione, viene restituito un messaggio per l'output e il runbook termina. In caso contrario, viene usato **Start-AzureVM** o **Stop-AzureVM** per tentare di avviare o arrestare la macchina virtuale con il risultato della richiesta archiviato in una variabile. Verrà quindi restituito all'output un messaggio che specifica se la richiesta di avvio o di arresto è stata inviata correttamente.


## Articoli correlati

- [Runbook figlio in Automazione di Azure](automation-child-runbooks.md) 
- [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md)

<!---HONumber=Sept15_HO4-->