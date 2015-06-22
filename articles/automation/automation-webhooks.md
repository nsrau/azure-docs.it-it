<properties 
   pageTitle="Webhook di Automazione di Azure"
   description="Un webhook che consente a un client di avviare un runbook in Automazione di Azure da una chiamata HTTP. Questo articolo descrive come creare un webhook e come chiamarne uno per avviare un runbook."
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
   ms.date="05/13/2015"
   ms.author="bwren" />

# Webhook di Automazione di Azure

Un *webhook* consente di avviare un runbook specifico in Automazione di Azure tramite una singola richiesta HTTP. In questo modo, servizi esterni come Visual Studio Online, GitHub o applicazioni personalizzate possono avviare i runbook senza implementare una soluzione completa usando l'API di Automazione di Azure.

![Webhook](media/automation-webhooks/webhooks-overview.png)

È possibile confrontare i webhook con altre modalità di avvio di un runbook nell'articolo relativo all'[avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md).

## Informazioni dettagliate sui webhook

La tabella seguente descrive le proprietà che devono essere configurate per un webhook.

| Proprietà | Descrizione |
|:---|:---|
|Name | È possibile specificare un nome qualsiasi per un webhook dal momento che non viene esposto al client. Viene usato solo per consentire all'utente di identificare il runbook in Automazione di Azure. <br> Come procedura consigliata, è opportuno assegnare al webhook un nome correlato al client in cui verrà usato. |
|URL |L'URL del webhook è l'indirizzo univoco che viene chiamato da un client con HTTP POST per avviare il runbook collegato al webhook. Viene generato automaticamente al momento della creazione del webhook. Non è possibile specificare un URL personalizzato. <br> <br> L'URL contiene un token di sicurezza che consente al runbook di essere richiamato da un sistema di terze parti senza un'autenticazione aggiuntiva. Per questo motivo, deve essere considerato come una password. Per motivi di sicurezza, è possibile visualizzare l'URL solo nel portale di anteprima di Azure al momento della creazione del webhook. È consigliabile prendere nota dell'URL e conservarlo in un luogo sicuro per usi futuri. |
|Expiration date | Analogamente a un certificato, un webhook ha una data di scadenza oltre la quale non può più essere usato. La data di scadenza non può essere modificata dopo la creazione del webhook e il webhook non può essere abilitato di nuovo dopo la data di scadenza. In questo caso, è necessario creare un altro webhhok per sostituire quello corrente e aggiornare il client per l'uso del nuovo webhook. |
| Enabled | Un runbook viene abilitato per impostazione predefinita nel momento in cui viene creato. Se viene impostato su Disabled, nessun client sarà in grado di usarlo. È possibile impostare la proprietà **Enabled** quando si crea il webhook o in qualsiasi momento successivo. |


### Parametri
Un webhook può definire valori per parametri di runbook usati quando il runbook viene avviato dal webhook. Il webhook deve includere valori per tutti i parametri obbligatori del runbook e può includere valori per i parametri facoltativi. Se esistono più webhook collegati a un singolo runbook, ognuno di essi potrà usare valori di parametri diversi.

>[AZURE.NOTE]I valori di parametri impostati da un webhook attualmente non possono essere modificati dopo la creazione del webhook. In questo caso sarà necessario creare un altro webhook che usa valori di parametri diversi.

Quando avvia un runbook con un webhook, un client non può eseguire l'override dei valori di parametri definiti nel webhook. Per ricevere dati dal client, il runbook può accettare un singolo parametro denominato **$WebhookData** di tipo [object] che conterrà i dati inclusi dal client nella richiesta POST.

![Webhookdata](media/automation-webhooks/webhookdata.png)

L'oggetto **$WebhookData** disporrà delle proprietà seguenti:

| Proprietà | Descrizione |
|:--- |:---|
| WebhookName | Nome del webhook. |
| RequestHeader | Intestazione della richiesta POST in ingresso. |
| RequestBody | Corpo della richiesta POST in ingresso. |

Non è richiesta alcuna configurazione del webhook per supportare il parametro **$WebhookData** e il runbook non deve necessariamente accettarlo. Se il runbook non definisce il parametro, gli eventuali dettagli della richiesta inviata dal client verranno ignorati.

Se si specifica un valore per $WebhookData quando si crea il webhook, viene eseguito l'override di tale valore quando il webhook avvia il runbook con i dati della richiesta POST del client, anche se il client non include dati nel corpo della richiesta. Se si avvia un runbook con l'oggetto $WebhookData che usa un metodo diverso da un webhook, è possibile specificare un valore per $Webhookdata che verrà riconosciuto dal runbook. Questo valore deve essere un oggetto con le stesse proprietà di $Webhookdata in modo che il runbook possa usarlo correttamente.

>[AZURE.NOTE]I valori di tutti i parametri di input vengono registrati con il processo di runbook. Qualsiasi input fornito dal client verrà quindi registrato con $WebhookData e sarà disponibile per chiunque abbia accesso al processo di automazione. Per questo motivo è consigliabile procedere con cautela quando si includono dati sensibili nelle chiamate di webhook.

## Sicurezza

La sicurezza di un webhook si basa sulla privacy dell'URL che contiene un token di sicurezza che consente di richiamarlo. Automazione di Azure non esegue alcuna autenticazione per la richiesta, purché venga inviata all'URL corretto. Per questo motivo, non è consigliabile usare i webhook per i runbook che eseguono funzioni sensibili senza usare una modalità alternativa di convalida della richiesta.

È possibile includere nel runbook la logica per determinare che è stato chiamato da un webhook selezionando la proprietà **WebhookName** del parametro $WebhookData. Il runbook può eseguire altre convalide cercando informazioni specifiche nella proprietà **RequestHeader** o **RequestBody**.

Un'altra strategia consiste nel fare in modo che il runbook esegua la convalida di una condizione esterna quando riceve una richiesta da un webhook. Si consideri ad esempio un runbook chiamato da GitHub ogni volta che si verifica un nuovo commit in un repository di GitHub. Il runbook può connettersi a GitHub per convalidare che si sia effettivamente verificato un nuovo commit prima di continuare.

## Creazione di un webhook

Seguire questa procedura per creare un nuovo webhook collegato a un runbook nel portale di anteprima di Azure.

1. Nel pannello **Runbook** nel portale di anteprima di Azure fare clic sul runbook che verrà avviato dal webhook per visualizzare il pannello dei dettagli. 
3. Fare clic su **Webhook** nella parte superiore del pannello per aprire il pannello **Add Webhook**. <br> ![Pulsante Webhooks](media/automation-webhooks/webhooks-button.png)
4. Fare clic su **Create new webhook** per aprire il pannello **Create webhook**.
5. Specificare un valore per **Name** ed **Expiration Date** per il webhook e indicare se deve essere abilitato. Per altre informazioni su queste proprietà, vedere [Informazioni dettagliate sui webhook](#details-of-a-webhook).
6. Fare clic sull'icona di copia e premere CTRL+C per copiare l'URL del webhook. Annotarlo in un luogo sicuro. **Dopo aver creato il webhook, non è possibile recuperare di nuovo l'URL.** <br> ![URL di webhook](media/automation-webhooks/copy-webhook-url.png)
1. Fare clic su **Create** per creare il webhook.
3. Fare clic su **Parameters** per specificare i valori per i parametri del runbook. <br>
1. Dopo aver configurato il webhook, fare clic su **OK**.


## Uso di un webhook

Per usare un webhook, l'applicazione client deve inviare una richiesta HTTP POST con l'URL del webhook. La sintassi del webhook sarà nel formato seguente.

	http://<Webhook Server>/token?=<Token Value>


Il client riceverà uno dei codici seguenti in risposta alla richiesta POST.

| Codice | Testo | Descrizione |
|:---|:----|:---|
| 202 | Accepted | La richiesta è stata accettata e il runbook è stato avviato. |
| 400 | Bad Request | La richiesta non è stata accettata per uno dei motivi seguenti. <ul> <li>Il webhook è scaduto.</li> <li>Il webhook è disabilitato.</li> <li>Il token nell'URL non è valido.</li> </ul>|
| 500 | Internal Server Error | L'URL è valido, ma si è verificato un errore. Inviare di nuovo la richiesta. |

Il client non può determinare quando viene completato il processo del runbook oppure ottenere lo stato di completamento da un webhook perché il webhook non restituisce un identificatore per il processo del runbook. È possibile convalidare solo che la richiesta è stata inviata correttamente.

### Esempio

L'esempio seguente avvia un runbook usando un webhook con Windows PowerShell. Questo esempio include dati nell'intestazione e nel corpo che possono essere usati dal runbook. Si noti che qualsiasi linguaggio in grado di creare una richiesta HTTP può usare un webhook.

	$uri = "https://oaaswebhookcurrent.cloudapp.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"header1"="headerval1";"header2"="headerval2"}
	$body = "some request body"

	Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body

Il runbook di esempio seguente accetta la richiesta precedente e recupera i dati dal webhook.

	workflow Sample-Webhook
	{
		param (	
				[object]$WebhookData
		)
	
		$WebhookName 	= 	$WebhookData.WebhookName
		$WebhookHeaders = 	$WebhookData.RequestHeader
		$WebhookBody 	= 	$WebhookData.RequestBody
	} 

	

## Articoli correlati

- [Avvio di un runbook](automation-starting-a-runbook.md)
- [Visualizzazione dello stato di un processo di runbook](automation-viewing-the-status-of-a-runbook-job.md)

<!---HONumber=58--> 