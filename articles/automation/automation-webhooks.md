<properties 
   pageTitle="Webhook di Automazione di Azure | Microsoft Azure"
   description="Un webhook che consente a un client di avviare un Runbook in Automazione di Azure da una chiamata HTTP. Questo articolo descrive come creare un webhook e come chiamarne uno per avviare un Runbook."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="bwren;sngun"/>

# Webhook di Automazione di Azure

Un *webhook* consente di avviare un Runbook specifico in Automazione di Azure tramite una singola richiesta HTTP. In questo modo, i servizi esterni come Visual Studio Team Services, GitHub o le applicazioni personalizzate possono avviare i runbook senza implementare una soluzione completa usando l'API di Automazione di Azure. ![Webhook](media/automation-webhooks/webhooks-overview.png)

È possibile confrontare i webhook con altre modalità di avvio di un Runbook nell'articolo relativo all'[avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md).

## Informazioni dettagliate sui webhook

La tabella seguente descrive le proprietà che devono essere configurate per un webhook.

| Proprietà | Descrizione |
|:---|:---|
|Name | È possibile specificare un nome qualsiasi per un webhook dal momento che non viene esposto al client. Viene usato solo per consentire all'utente di identificare il Runbook in Automazione di Azure. <br> Come procedura consigliata, è opportuno assegnare al webhook un nome correlato al client in cui verrà usato. |
|URL |L'URL del webhook è l'indirizzo univoco che viene chiamato da un client con HTTP POST per avviare il Runbook collegato al webhook. Viene generato automaticamente al momento della creazione del webhook. Non è possibile specificare un URL personalizzato. <br> <br> L'URL contiene un token di sicurezza che consente al Runbook di essere richiamato da un sistema di terze parti senza un'autenticazione aggiuntiva. Per questo motivo, deve essere considerato come una password. Per motivi di sicurezza, è possibile visualizzare l'URL solo nel portale di anteprima di Azure al momento della creazione del webhook. È consigliabile prendere nota dell'URL e conservarlo in un luogo sicuro per usi futuri. |
|Expiration date | Analogamente a un certificato, un webhook ha una data di scadenza oltre la quale non può più essere usato. La data di scadenza non può essere modificata dopo la creazione del webhook e il webhook non può essere abilitato di nuovo dopo la data di scadenza. In questo caso, è necessario creare un altro webhhok per sostituire quello corrente e aggiornare il client per l'uso del nuovo webhook. |
| Enabled | Un webhook viene abilitato per impostazione predefinita nel momento in cui viene creato. Se viene impostato su Disabled, nessun client sarà in grado di usarlo. È possibile impostare la proprietà **Enabled** quando si crea il webhook o in qualsiasi momento successivo. |


### Parametri
Un webhook può definire valori per parametri di Runbook usati quando il Runbook viene avviato dal webhook. Il webhook deve includere valori per tutti i parametri obbligatori del Runbook e può includere valori per i parametri facoltativi. Un valore di parametro configurato per un webhook può essere modificato anche dopo aver creato il webhoook. Se esistono più webhook collegati a un singolo Runbook, ognuno di essi potrà usare valori di parametri diversi.

Quando avvia un Runbook con un webhook, un client non può eseguire l'override dei valori di parametri definiti nel webhook. Per ricevere dati dal client, il Runbook può accettare un singolo parametro denominato **$WebhookData** di tipo [object] che conterrà i dati inclusi dal client nella richiesta POST.

![Webhookdata](media/automation-webhooks/webhookdata.png)

L'oggetto **$WebhookData** disporrà delle proprietà seguenti:

| Proprietà | Descrizione |
|:--- |:---|
| WebhookName | Nome del webhook. |
| RequestHeader | Tabella hash contenente le intestazioni della richiesta POST in ingresso. |
| RequestBody | Corpo della richiesta POST in ingresso. Manterrà tutta la formattazione, ad esempio stringa, JSON, XML o dati codificati in un form. Il Runbook deve essere scritto in modo che funzioni con il formato di dati previsto.|


Non è richiesta alcuna configurazione del webhook per supportare il parametro **$WebhookData** e il Runbook non deve necessariamente accettarlo. Se il Runbook non definisce il parametro, gli eventuali dettagli della richiesta inviata dal client verranno ignorati.

Se si specifica un valore per $WebhookData quando si crea il webhook, viene eseguito l'override di tale valore quando il webhook avvia il Runbook con i dati della richiesta POST del client, anche se il client non include dati nel corpo della richiesta. Se si avvia un Runbook con l'oggetto $WebhookData che usa un metodo diverso da un webhook, è possibile specificare un valore per $Webhookdata che verrà riconosciuto dal Runbook. Questo valore deve essere un oggetto con le stesse [proprietà](#details-of-a-webhook) di $Webhookdata in modo che il Runbook possa usarlo correttamente come se stesse usando con il WebhookData effettivo passato da un webhook.

Ad esempio, se si inizia il seguente runbook dal portale di Azure e si desidera passare alcuni esempi WebhookData per testarli, poiché WebhookData è un oggetto, deve essere passato come JSON nell'interfaccia utente.

![Parametro WebhookData dall'interfaccia utente](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Per il runbook riportato sopra, se si hanno le seguenti proprietà per il parametro WebhookData:

1. WebhookName: *MyWebhook*
2. RequestHeader: *From=Test User*
3. RequestBody: *[“VM1”, “VM2”]*

Passare quindi il seguente valore JSON nell'interfaccia utente per il parametro WebhookData:

* {"WebhookName":"MyWebhook", "RequestHeader":{"From":"Test User"}, "RequestBody":"["VM1","VM2"]"}

![Parametro WebhookData Start dall'interfaccia utente](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)


>[AZURE.NOTE]I valori di tutti i parametri di input vengono registrati con il processo di Runbook. Qualsiasi input fornito dal client nella richiesta del webhook verrà quindi registrato e sarà disponibile per chiunque abbia accesso al processo di automazione. Per questo motivo è consigliabile procedere con cautela quando si includono dati sensibili nelle chiamate di webhook.

## Sicurezza

La sicurezza di un webhook si basa sulla privacy dell'URL che contiene un token di sicurezza che consente di richiamarlo. Automazione di Azure non esegue alcuna autenticazione per la richiesta, purché venga inviata all'URL corretto. Per questo motivo, non è consigliabile usare i webhook per i Runbook che eseguono funzioni sensibili senza usare una modalità alternativa di convalida della richiesta.

È possibile includere nel Runbook la logica per determinare che è stato chiamato da un webhook selezionando la proprietà **WebhookName** del parametro $WebhookData. Il Runbook può eseguire altre convalide cercando informazioni specifiche nella proprietà **RequestHeader** o **RequestBody**.

Un'altra strategia consiste nel fare in modo che il Runbook esegua la convalida di una condizione esterna quando riceve una richiesta da un webhook. Si consideri ad esempio un Runbook chiamato da GitHub ogni volta che si verifica un nuovo commit in un repository di GitHub. Il Runbook può connettersi a GitHub per convalidare che si sia effettivamente verificato un nuovo commit prima di continuare.

## Creazione di un webhook

Seguire questa procedura per creare un nuovo webhook collegato a un Runbook nel portale di anteprima di Azure.

1. Nel pannello **Runbook** nel portale di anteprima di Azure fare clic sul Runbook che verrà avviato dal webhook per visualizzare il pannello dei dettagli. 
3. Fare clic su **Webhook** nella parte superiore del pannello per aprire il pannello **Add Webhook**. <br> ![Pulsante Webhooks](media/automation-webhooks/webhooks-button.png)
4. Fare clic su **Create new webhook** per aprire il pannello **Create webhook**.
5. Specificare un valore per **Name** ed **Expiration Date** per il webhook e indicare se deve essere abilitato. Per altre informazioni su queste proprietà, vedere [Informazioni dettagliate sui webhook](#details-of-a-webhook).
6. Fare clic sull'icona di copia e premere CTRL+C per copiare l'URL del webhook. Annotarlo in un luogo sicuro. **Dopo aver creato il webhook, non è possibile recuperare di nuovo l'URL.** <br> ![URL di webhook](media/automation-webhooks/copy-webhook-url.png)
3. Fare clic su **Parameters** per specificare i valori per i parametri del Runbook. Se il Runbook ha parametri obbligatori, non sarà possibile creare il webhook a meno che non vengano messi a disposizione i valori.
1. Fare clic su **Create** per creare il webhook.


## Uso di un webhook

Per usare un webhook dopo averlo creato, l'applicazione client deve inviare una richiesta HTTP POST con l'URL del webhook. La sintassi del webhook sarà nel formato seguente.

	http://<Webhook Server>/token?=<Token Value>

Il client riceverà uno dei codici restituiti seguenti dalla richiesta POST.

| Codice | Testo | Descrizione |
|:---|:----|:---|
| 202 | Accepted | La richiesta è stata accettata e il Runbook è stato accodato. |
| 400 | Bad Request | La richiesta non è stata accettata per uno dei motivi seguenti. <ul> <li>Il webhook è scaduto.</li> <li>Il webhook è disabilitato.</li> <li>Il token nell'URL non è valido.</li> </ul>|
| 404 | Non trovato | La richiesta non è stata accettata per uno dei motivi seguenti. <ul><li>Il webhook non è stato trovato.</li> <li>Il runbook non è stato trovato.</li> <li>L'account non è stato trovato.</li></ul> |
| 500 | Internal Server Error | L'URL è valido, ma si è verificato un errore. Inviare di nuovo la richiesta. |

Presupponendo che la richiesta riesca, la risposta del webhook conterrà l'ID processo in formato JSON come indicato di seguito. Conterrà un singolo ID processo, ma il formato JSON consente potenziali miglioramenti futuri.

	{"JobIds":["<JobId>"]}  

Il client non è in grado di determinare quando viene completato il processo del Runbook o lo stato di avanzamento dal webhook. Può determinare queste informazioni usando l'ID processo con un altro metodo, ad esempio [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) o l'[API di Automazione di Azure](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### Esempio

L'esempio seguente usa Windows PowerShell per avviare un Runbook con un webhook. Si noti che qualsiasi linguaggio in grado di creare una richiesta HTTP può usare un webhook. Windows PowerShell viene usato qui solo come esempio.

Il Runbook prevede un elenco di macchine virtuali in formato JSON nel corpo della richiesta. Nell'intestazione della richiesta vengono incluse anche informazioni sull'utente che avvia il Runbook e la data e l'ora di avvio.

	$uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @(
    			@{ Name="vm01";ServiceName="vm01"},
    			@{ Name="vm02";ServiceName="vm02"}
    		)
	$body = ConvertTo-Json -InputObject $vms 

	$response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
	$jobid = ConvertFrom-Json $response 


L'immagine seguente illustra le informazioni di intestazione (usando una traccia di [ Fiddler](http://www.telerik.com/fiddler)) dalla richiesta. Sono incluse le intestazioni standard di una richiesta HTTP, oltre alla data personalizzata e le intestazioni DA aggiunte. Ognuno di questi valori è disponibile per il Runbook nella proprietà **RequestHeaders** di **WebhookData**.

![Pulsante Webhooks](media/automation-webhooks/webhook-request-headers.png)

L'immagine seguente mostra il corpo della richiesta (usando una traccia di [Fiddler](http://www.telerik.com/fiddler)) disponibile per il Runbook nella proprietà **RequestBody** di **WebhookData**. Viene formattata come JSON perché questo era il formato incluso nel corpo della richiesta.

![Pulsante Webhooks](media/automation-webhooks/webhook-request-body.png)

L'immagine seguente mostra la richiesta inviata da Windows PowerShell e la risposta risultante. L'ID processo viene estratto dalla risposta e convertito in una stringa.

![Pulsante Webhooks](media/automation-webhooks/webhook-request-response.png)

Il Runbook di esempio seguente accetta la richiesta di esempio precedente e avvia le macchine virtuali specificate nel corpo della richiesta.

	workflow Test-StartVirtualMachinesFromWebhook
	{
		param (	
			[object]$WebhookData
		)

		# If runbook was called from Webhook, WebhookData will not be null.
		if ($WebhookData -ne $null) {	
			
			# Collect properties of WebhookData
			$WebhookName 	= 	$WebhookData.WebhookName
			$WebhookHeaders = 	$WebhookData.RequestHeader
			$WebhookBody 	= 	$WebhookData.RequestBody
			
			# Collect individual headers. VMList converted from JSON.
			$From = $WebhookHeaders.From
			$VMList = ConvertFrom-Json -InputObject $WebhookBody
			Write-Output "Runbook started from webhook $WebhookName by $From."
			
			# Authenticate to Azure resources
			$Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
			Add-AzureAccount -Credential $Cred
			
            # Start each virtual machine
			foreach ($VM in $VMList)
			{
				$VMName = $VM.Name
				Write-Output "Starting $VMName"
				Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
			}
		}
		else {
			Write-Error "Runbook mean to be started only from webhook." 
		} 
	}


## Avvio di runbook in risposta agli avvisi di Azure

I runbook abilitati per i webhook possono essere usati in risposta agli [avvisi di Azure](../azure-portal/insights-receive-alert-notifications.md). È possibile monitorare le risorse in Azure grazie alla raccolta di statistiche relative, ad esempio, a prestazioni, disponibilità e uso con l'aiuto degli avvisi di Azure. È possibile ricevere avvisi basati su metriche o eventi di monitoraggio per i servizi di Azure, attualmente gli account di automazione supportano solo le metriche. Quando il valore di una metrica specifica supera la soglia assegnata o se l'evento configurato viene attivato quando una notifica viene inviata all'amministratore o ai coamministratori del servizio per risolvere l'avviso, per altre informazioni sulle metriche e sugli eventi, vedere [Avvisi di Azure](../azure-portal/insights-receive-alert-notifications.md).

Oltre a usare gli avvisi di Azure come sistema di notifica, è anche possibile avviare i runbook in risposta agli avvisi. Automazione di Azure offre la possibilità di eseguire runbook abilitati per i webhook con gli avvisi di Azure. Quando una metrica supera il valore di soglia configurato, la regola dell'avviso viene abilitata e attiva il webhook di automazione che a sua volta esegue il runbook.

![Webhook](media/automation-webhooks/webhook-alert.jpg)

### Contesto dell'avviso

Se si considera una risorsa di Azure, ad esempio una macchina virtuale, l'uso della CPU del computer rappresenta una delle metriche fondamentali relative alle prestazioni. Se l'uso della CPU è pari al 100% o più di una certa quantità per un lungo periodo di tempo, si potrebbe voler riavviare la macchina virtuale per risolvere il problema. Questo problema può essere risolto tramite la configurazione di una regola di avviso per la macchina virtuale. Tale regola userà la percentuale di CPU come metrica. La percentuale di CPU viene usata solo come esempio. È tuttavia possibile configurare numerose altre metriche per le risorse di Azure e riavviare la macchina virtuale per risolvere il problema. È comunque possibile configurare il runbook per eseguire altre operazioni.

Quando la regola di avviso viene abilitata e attiva il runbook abilitato per i webhook, la regola invia il contesto dell'avviso al runbook. Il [contesto dell'avviso](../azure-portal/insights-receive-alert-notifications.md) contiene dettagli, tra cui i valori di **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** e **Timestamp** richiesti dal runbook per identificare la risorsa in cui verrà eseguita l'azione. Il contesto dell'avviso è incorporato nel corpo del contesto dell'oggetto **WebhookData** inviato al runbook, a cui è possibile accedere tramite la proprietà **Webhook.RequestBody**.


### Esempio

Creare una macchina virtuale di Azure nella sottoscrizione corrente e associare un [avviso per monitorare la metrica relativa alla percentuale della CPU](../azure-portal/insights-receive-alert-notifications.md). Durante la creazione dell'avviso assicurarsi di popolare il campo del webhook con l'URL del webhook generato durante la creazione del webhook stesso.

Il seguente runbook di esempio viene attivato quando la regola dell'avviso diventa attiva e raccoglie i parametri del contesto dell'avviso. Tali parametri consentono al runbook di identificare la risorsa in cui verrà eseguita l'azione.

	workflow Invoke-RunbookUsingAlerts
	{
	    param (  	
	        [object]$WebhookData 
	    ) 

	    # If runbook was called from Webhook, WebhookData will not be null.
	    if ($WebhookData -ne $null) {   
	        # Collect properties of WebhookData. 
	        $WebhookName    =   $WebhookData.WebhookName 
	        $WebhookBody    =   $WebhookData.RequestBody 
	        $WebhookHeaders =   $WebhookData.RequestHeader 

	        # Outputs information on the webhook name that called This 
	        Write-Output "This runbook was started from webhook $WebhookName." 

	        
			# Obtain the WebhookBody containing the AlertContext 
			$WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody) 
	        Write-Output "`nWEBHOOK BODY" 
	        Write-Output "=============" 
	        Write-Output $WebhookBody 

	        # Obtain the AlertContext     
	        $AlertContext = [object]$WebhookBody.context

	        # Some selected AlertContext information 
	        Write-Output "`nALERT CONTEXT DATA" 
	        Write-Output "===================" 
	        Write-Output $AlertContext.name 
	        Write-Output $AlertContext.subscriptionId 
	        Write-Output $AlertContext.resourceGroupName 
	        Write-Output $AlertContext.resourceName 
	        Write-Output $AlertContext.resourceType 
	        Write-Output $AlertContext.resourceId 
	        Write-Output $AlertContext.timestamp 

	    	# Act on the AlertContext data, in our case restarting the VM. 
	    	# Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine. 
	        $cred = Get-AutomationPSCredential -Name "MyAzureCredential" 
	        Add-AzureAccount -Credential $cred 
	        Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN" 
	      
	        #Check the status property of the VM
	        Write-Output "Status of VM before taking action"
	        Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
	        Write-Output "Restarting VM"

	        # Restart the VM by passing VM name and Service name which are same in this case
	        Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName 
	        Write-Output "Status of VM after alert is active and takes action"
	        Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
	    } 
	    else  
	    { 
	        Write-Error "This runbook is meant to only be started from a webhook."  
	    }  
	}

 

## Passaggi successivi

- Per informazioni dettagliate sulle diverse modalità disponibili per l'avvio dei runbook, vedere [Avvio di un runbook](automation-starting-a-runbook.md)
- Per informazioni sulla visualizzazione dello stato di un processo del runbook, vedere [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md)
- [Uso di Automazione di Azure per eseguire azioni sugli avvisi di Azure](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/)

<!---HONumber=AcomDC_0121_2016-->