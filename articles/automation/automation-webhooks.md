---
title: Avviare un runbook di Automazione di Azure con un webhook
description: Un webhook che consente a un client di avviare un Runbook in Automazione di Azure da una chiamata HTTP.  Questo articolo descrive come creare un webhook e come chiamarne uno per avviare un Runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6e0e0cdfd5bdda125ed38173df56e0fb7a84f71a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477936"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Avviare un runbook di Automazione di Azure con un webhook

Un *webhook* consente di avviare un Runbook specifico in Automazione di Azure tramite una singola richiesta HTTP. In questo modo i servizi esterni, ad esempio servizi di Azure DevOps, GitHub, log di monitoraggio di Azure o applicazioni personalizzate possono avviare i runbook senza implementare una soluzione completa usando l'API di automazione di Azure.  
![Panoramica dei webhook](media/automation-webhooks/webhook-overview-image.png)

È possibile confrontare i webhook con altre modalità di avvio di un Runbook nell'articolo relativo all' [avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md)

> [!NOTE]
> Usando un webhook per avviare un runbook di Python non è supportato.

## <a name="details-of-a-webhook"></a>Informazioni dettagliate sui webhook

La tabella seguente descrive le proprietà che devono essere configurate per un webhook.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| NOME |È possibile specificare un nome qualsiasi per un webhook dal momento che non viene esposto al client. Il nome viene usato solo per consentire all'utente di identificare il runbook in Automazione di Azure. <br> Come procedura consigliata è opportuno assegnare al webhook un nome correlato al client in cui verrà usato. |
| URL |L'URL del webhook è l'indirizzo univoco che viene chiamato da un client con HTTP POST per avviare il Runbook collegato al webhook. Viene generato automaticamente al momento della creazione del webhook. Non è possibile specificare un URL personalizzato. <br> <br> L'URL contiene un token di sicurezza che consente al runbook di essere richiamato da un sistema di terze parti senza un'autenticazione aggiuntiva. Per questo motivo, deve essere considerato come una password. Per motivi di sicurezza, è possibile visualizzare l'URL solo nel portale di Azure al momento della creazione del webhook. Prendere nota dell'URL e conservarlo in un luogo sicuro per usi futuri. |
| Expiration date |Analogamente a un certificato, un webhook ha una data di scadenza oltre la quale non può più essere usato. La data di scadenza può essere modificata dopo la creazione del webhook, a condizione che quest'ultimo non sia scaduto. |
| Enabled |Un webhook viene abilitato per impostazione predefinita nel momento in cui viene creato. Se viene disabilitato, nessun client sarà in grado di usarlo. È possibile impostare la proprietà **Enabled** quando si crea il webhook o in qualsiasi momento successivo. |

### <a name="parameters"></a>Parametri

Un webhook può definire valori per parametri di Runbook usati quando il Runbook viene avviato dal webhook. Il webhook deve includere valori per tutti i parametri obbligatori del Runbook e può includere valori per i parametri facoltativi. Un valore di parametro configurato per un webhook può essere modificato anche dopo aver creato il webhook. Se esistono più webhook collegati a un singolo Runbook, ognuno di essi potrà usare valori di parametri diversi.

Quando avvia un runbook con un webhook, un client non può eseguire l'override dei valori di parametri definiti nel webhook. Per ricevere dati dal client, il runbook può accettare un singolo parametro denominato **$WebhookData**. Questo parametro è di un tipo [object] che contiene i dati da includere nella richiesta POST del client.

![Proprietà Webhookdata](media/automation-webhooks/webhook-data-properties.png)

Di seguito sono elencate le proprietà dell'oggetto **$WebhookData**:

| Proprietà | Descrizione |
|:--- |:--- |
| WebhookName |Nome del webhook. |
| RequestHeader |Tabella hash contenente le intestazioni della richiesta POST in ingresso. |
| RequestBody |Corpo della richiesta POST in ingresso. Mantiene tutta la formattazione, ad esempio stringa, JSON, XML o dati codificati in un modulo. Il Runbook deve essere scritto in modo che funzioni con il formato di dati previsto. |

Non è richiesta alcuna configurazione del webhook per supportare il parametro **$WebhookData** e il runbook non deve necessariamente accettarlo. Se il runbook non definisce il parametro, gli eventuali dettagli della richiesta inviata dal client verranno ignorati.

Se si specifica un valore per $WebhookData quando si crea il webhook, viene eseguito l'override di tale valore quando il webhook avvia il runbook con i dati della richiesta POST del client, anche se il client non include dati nel corpo della richiesta. Se si avvia un runbook con l'oggetto $WebhookData che usa un metodo diverso da un webhook, è possibile specificare un valore per $Webhookdata che verrà riconosciuto dal runbook. Questo valore deve essere un oggetto con le stesse [proprietà](#details-of-a-webhook) di $Webhookdata in modo che il Runbook possa usarlo correttamente come se stesse usando con il WebhookData effettivo passato da un webhook.

Ad esempio, se si avvia il seguente runbook dal portale di Azure e si vuole passare alcuni WebhookData di esempio per testarli, WebhookData deve essere passato come JSON nell'interfaccia utente poiché è un oggetto.

![Parametro WebhookData dall'interfaccia utente](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Per il runbook seguente, se si hanno le seguenti proprietà per il parametro WebhookData:

* WebhookName: *MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*

Passare quindi il seguente valore JSON nell'interfaccia utente per il parametro WebhookData. L'esempio seguente con i caratteri di ritorno a capo e di nuova riga corrisponde al formato passato da un webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Parametro WebhookData Start dall'interfaccia utente](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> I valori di tutti i parametri di input vengono registrati con il processo di Runbook. Qualsiasi input fornito dal client nella richiesta del webhook verrà quindi registrato e sarà disponibile per chiunque abbia accesso al processo di automazione.  Per questo motivo è consigliabile procedere con cautela quando si includono dati sensibili nelle chiamate di webhook.

## <a name="security"></a>Security

La sicurezza di un webhook si basa sulla privacy dell'URL che contiene un token di sicurezza che consente di richiamarlo. Automazione di Azure non esegue alcuna autenticazione per la richiesta, purché venga inviata all'URL corretto. Per questo motivo, non è consigliabile usare i webhook per i Runbook che eseguono funzioni sensibili senza usare una modalità alternativa di convalida della richiesta.

È possibile includere nel Runbook la logica per determinare che è stato chiamato da un webhook selezionando la proprietà **WebhookName** del parametro $WebhookData. Il runbook può eseguire altre convalide cercando informazioni specifiche nella proprietà **RequestHeader** o **RequestBody**.

Un'altra strategia consiste nel fare in modo che il Runbook esegua la convalida di una condizione esterna quando riceve una richiesta da un webhook. Si consideri ad esempio un runbook chiamato da GitHub ogni volta che si verifica un nuovo commit in un repository di GitHub. Il runbook può connettersi a GitHub per convalidare che sia stato eseguito un nuovo commit prima di continuare.

## <a name="creating-a-webhook"></a>Creazione di un webhook

Seguire questa procedura per creare un nuovo webhook collegato a un Runbook nel portale di Azure.

1. Nella pagina **Runbook** nel portale di Azure fare clic sul runbook che verrà avviato dal webhook per visualizzare la pagina dei dettagli. Verificare che lo **stato** del runbook sia **Pubblicato**.
2. Fare clic su **Webhook** nella parte superiore della pagina per aprire la pagina **Aggiungi webhook**.
3. Fare clic su **Creare un nuovo webhook** per aprire la pagina **Crea webhook**.
4. Specificare un valore per **Nome** e **Data scadenza** per il webhook e indicare se deve essere abilitato. Per altre informazioni su queste proprietà, vedere [Informazioni dettagliate sui webhook](#details-of-a-webhook) .
5. Fare clic sull'icona di copia e premere CTRL+C per copiare l'URL del webhook. Annotarlo in un luogo sicuro. **Dopo aver creato il webhook, non è possibile recuperare di nuovo l'URL.**

   ![URL webhook](media/automation-webhooks/copy-webhook-url.png)

1. Fare clic su **Parameters** per specificare i valori per i parametri del Runbook. Se il runbook contiene parametri obbligatori, non sarà possibile creare il webhook a meno che non vengano forniti i valori.
1. Fare clic su **Create** per creare il webhook.

## <a name="using-a-webhook"></a>Uso di un webhook

Per usare un webhook dopo averlo creato, l'applicazione client deve inviare una richiesta HTTP POST con l'URL del webhook. La sintassi del webhook ha il formato seguente:

```http
http://<Webhook Server>/token?=<Token Value>
```

Il client riceve uno dei codici restituiti seguenti dalla richiesta POST.

| Codice | Testo | Descrizione |
|:--- |:--- |:--- |
| 202 |Accepted |La richiesta è stata accettata e il Runbook è stato accodato. |
| 400 |Bad Request |La richiesta non è stata accettata per uno dei motivi seguenti: <ul> <li>Il webhook è scaduto.</li> <li>Il webhook è disabilitato.</li> <li>Il token nell'URL non è valido.</li>  </ul> |
| 404 |Non trovato |La richiesta non è stata accettata per uno dei motivi seguenti: <ul> <li>Il webhook non è stato trovato.</li> <li>Il runbook non è stato trovato.</li> <li>L'account non è stato trovato.</li>  </ul> |
| 500 |Internal Server Error |L'URL è valido, ma si è verificato un errore. Inviare di nuovo la richiesta. |

Se la richiesta ha esito positivo, la risposta del webhook conterrà l'ID processo in formato JSON come indicato di seguito. Conterrà un singolo ID processo, ma il formato JSON consente potenziali miglioramenti futuri.

```json
{"JobIds":["<JobId>"]}
```

Il client non è in grado di determinare quando viene completato il processo del runbook o lo stato di avanzamento dal webhook. Può determinare queste informazioni usando l'ID processo con un altro metodo, ad esempio [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) o l'[API di Automazione di Azure](/rest/api/automation/job).

## <a name="renew-webhook"></a>Rinnovare un webhook

Un webhook ha un periodo di validità di un anno a partire dalla data di creazione. Quando raggiunge il limite di validità di un anno, il webhook scade automaticamente. Dopo la scadenza, un webhook non può essere riattivato, ma deve essere rimosso e ricreato. Se un webhook non ha raggiunto la scadenza, può essere esteso.

Per estendere un webhook, passare al runbook che contiene il webhook. Selezionare **Webhook** sotto **Risorse**. Fare clic sul webhook che si vuole estendere in modo da visualizzare la pagina **Webhook**.  Scegliere una nuova data e ora di scadenza e fare clic su **Salva**.

## <a name="sample-runbook"></a>Runbook di esempio

Il runbook di esempio seguente accetta i dati del webhook e avvia le macchine virtuali specificate nel corpo della richiesta. Per testare il runbook, nell'account di Automazione in **Runbook** fare clic su **+ Aggiungi runbook**. Se non si conosce la procedura di creazione di un runbook, vedere [Creare un runbook di Automazione di Azure](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal." 
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Testare l'esempio.

L'esempio seguente usa Windows PowerShell per avviare un Runbook con un webhook. Qualsiasi linguaggio in grado di creare una richiesta HTTP può usare un webhook. Windows PowerShell viene usato qui come esempio.

Il Runbook prevede un elenco di macchine virtuali in formato JSON nel corpo della richiesta. Il runbook verifica anche che le intestazioni contengano un messaggio specifico per determinare se il chiamante webhook è valido.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

L'esempio seguente mostra il corpo della richiesta che è disponibile per il runbook nella proprietà **RequestBody** di **WebhookData**. Questo valore viene formattato come JSON perché questo era il formato incluso nel corpo della richiesta.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

L'immagine seguente mostra la richiesta inviata da Windows PowerShell e la risposta risultante. L'ID processo viene estratto dalla risposta e convertito in una stringa.

![Pulsante Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come usare Automazione di Azure per agire sugli avvisi di Azure, vedere [Usare un avviso per attivare un runbook di Automazione di Azure](automation-create-alert-triggered-runbook.md).

