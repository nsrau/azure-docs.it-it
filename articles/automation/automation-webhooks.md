---
title: Avviare un runbook di Automazione di Azure da un webhook
description: Questo articolo illustra come usare un webhook per avviare un runbook in Automazione di Azure da una chiamata HTTP.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: e64f437b65964b585311aeae25e5f3a92275754a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361677"
---
# <a name="start-a-runbook-from-a-webhook"></a>Avviare un runbook da un webhook

Un webhook consente a un servizio esterno di avviare un runbook specifico in Automazione di Azure tramite una singola richiesta HTTP. I servizi esterni includono Azure DevOps Services, GitHub, log di Monitoraggio di Azure e applicazioni personalizzate. Un servizio di questo tipo può usare un webhook per avviare un runbook senza implementare l'API di Automazione di Azure completa. È possibile confrontare i webhook con altri metodi di avvio di un runbook in [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md).

> [!NOTE]
> L'uso di un webhook per avviare un runbook Python non è supportato.

![Panoramica dei webhook](media/automation-webhooks/webhook-overview-image.png)

Per informazioni sui requisiti dei client per TLS 1,2 con webhook, vedere l' [imposizione di tls 1,2 per automazione di Azure](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="webhook-properties"></a>Proprietà dei webhook

La tabella seguente descrive le proprietà che devono essere configurate per un webhook.

| Proprietà | Description |
|:--- |:--- |
| Nome |Nome del webhook. È possibile specificare un nome qualsiasi, dal momento che non è esposto al client. Il nome viene usato solo per consentire all'utente di identificare il runbook in Automazione di Azure. Come procedura consigliata è opportuno assegnare al webhook un nome correlato al client in cui verrà usato. |
| URL |URL del webhook. Si tratta dell'indirizzo univoco che viene chiamato da un client con HTTP POST per avviare il runbook collegato al webhook. Viene generato automaticamente al momento della creazione del webhook. Non è possibile specificare un URL personalizzato. <br> <br> L'URL contiene un token di sicurezza che consente a un sistema di terze parti di chiamare il runbook senza alcuna autenticazione aggiuntiva. Per questo motivo, è consigliabile considerare l'URL come una password. Per motivi di sicurezza, è possibile visualizzare l'URL nel portale di Azure solo quando si crea il webhook. Prendere nota dell'URL e conservarlo in un luogo sicuro per usi futuri. |
| Expiration date | Data di scadenza del webhook, dopo la quale non può più essere usato. È possibile modificare la data di scadenza dopo la creazione del webhook, a condizione che quest'ultimo non sia scaduto. |
| Attivato | Impostazione che indica se il webhook è abilitato per impostazione predefinita quando viene creato. Se si imposta questa proprietà su Disattivato, nessun client può usare il webhook. È possibile impostare questa proprietà quando si crea il webhook o in qualsiasi altro momento dopo la creazione di questo. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametri usati quando il webhook avvia un runbook

Un webhook può definire valori per i parametri del runbook da usare all'avvio del runbook stesso. Il webhook deve includere valori per tutti i parametri obbligatori del runbook e può includere valori per i parametri facoltativi. Un valore di parametro configurato per un webhook può essere modificato anche dopo la creazione del webhook. Se esistono più webhook collegati a un singolo runbook, ognuno di essi potrà usare valori diversi per i parametri del runbook. Quando avvia un runbook con un webhook, un client non può eseguire l'override dei valori di parametri definiti nel webhook.

Per ricevere dati dal client, il runbook supporta un singolo parametro denominato `WebhookData`. Questo parametro definisce un oggetto contenente i dati inclusi dal client in una richiesta POST.

![Proprietà WebhookData](media/automation-webhooks/webhook-data-properties.png)

Il parametro `WebhookData` ha le proprietà seguenti:

| Proprietà | Descrizione |
|:--- |:--- |
| `WebhookName` | Nome del webhook. |
| `RequestHeader` | Tabella hash contenente le intestazioni della richiesta POST in ingresso. |
| `RequestBody` | Corpo della richiesta POST in ingresso. Il corpo mantiene la formattazione dei dati, ad esempio stringa, JSON, XML o con codifica per il modulo. Il Runbook deve essere scritto in modo che funzioni con il formato di dati previsto. |

Non è richiesta alcuna configurazione del webhook per supportare il parametro `WebhookData` e il runbook non deve necessariamente accettarlo. Se il runbook non definisce il parametro, gli eventuali dettagli della richiesta inviata dal client vengono ignorati.

> [!NOTE]
> Quando si chiama un webhook, il client deve sempre archiviare tutti i valori dei parametri per l'eventualità che la chiamata abbia esito negativo. Se si verifica un'interruzione della rete o un problema di connessione, l'applicazione non è in grado di recuperare le chiamate al webhook non riuscite.

Se si specifica un valore per `WebhookData` al momento della creazione del webhook, viene eseguito l'override di tale valore quando il webhook avvia il runbook con i dati della richiesta POST del client. Questo si verifica anche se l'applicazione non include dati nel corpo della richiesta. 

Se si avvia un runbook che definisce `WebhookData` usando un meccanismo diverso da un webhook, è possibile specificare un valore per `WebhookData` riconoscibile dal runbook. Questo valore deve essere un oggetto con le stesse [proprietà](#webhook-properties) del parametro `WebhookData` in modo che il runbook possa usarlo in modo analogo a oggetti `WebhookData` effettivi passati da un webhook.

Se ad esempio si avvia il runbook seguente dal portale di Azure e si vogliono passare alcuni dati di webhook di esempio a scopo di test, i dati devono essere passati in JSON nell'interfaccia utente.

![Parametro WebhookData dall'interfaccia utente](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Per il prossimo esempio di runbook, si definiranno le proprietà seguenti per `WebhookData`:

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

A questo punto, nell'interfaccia utente viene passato l'oggetto JSON seguente per il parametro `WebhookData`. Questo esempio, con ritorni a capo e caratteri di nuova riga, corrisponde al formato passato da un webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Parametro WebhookData Start dall'interfaccia utente](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Automazione di Azure registra i valori di tutti i parametri di input con il processo del runbook. Qualsiasi input fornito dal client nella richiesta webhook viene quindi registrato ed è disponibile per chiunque abbia accesso al processo di automazione. Per questo motivo è consigliabile procedere con cautela quando si includono dati sensibili nelle chiamate di webhook.

## <a name="webhook-security"></a>Sicurezza dei webhook

La sicurezza di un webhook si basa sulla privacy dell'URL corrispondente, che contiene un token di sicurezza che consente di chiamare il webhook. Automazione di Azure non esegue alcuna autenticazione per una richiesta, a condizione che questa venga inviata all'URL corretto. Per questo motivo, i client devono usare webhook per runbook che eseguono operazioni altamente sensibili solo se usano una modalità alternativa di convalida della richiesta.

Prendere in considerazione le strategie seguenti:

* È possibile includere logica in un runbook per determinare se quest'ultimo viene chiamato da un webhook. Fare in modo che il runbook controlli la proprietà `WebhookName` del parametro `WebhookData`. Il runbook può eseguire ulteriori operazioni di convalida cercando informazioni specifiche nella proprietà `RequestHeader` e `RequestBody`.

* Fare in modo che il Runbook esegua una convalida di una condizione esterna quando riceve una richiesta di webhook. Si consideri ad esempio un runbook chiamato da GitHub ogni volta che viene eseguito un nuovo commit in un repository di GitHub. Il runbook può connettersi a GitHub per verificare che sia stato eseguito un nuovo commit prima di continuare.

* Automazione di Azure supporta i tag del servizio rete virtuale di Azure, in particolare [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza di rete](../virtual-network/security-overview.md#security-rules) o nel firewall di [Azure](../firewall/service-tags.md) e attivare i webhook dall'interno della rete virtuale. I tag di servizio possono essere usati al posto di indirizzi IP specifici quando si creano regole di sicurezza. Specificando il nome del tag del servizio **GuestAndHybridManagement** nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio di automazione. Questo tag di servizio non supporta l'abilitazione di un controllo più granulare limitando gli intervalli IP a un'area specifica.

## <a name="create-a-webhook"></a>Creare un webhook

Seguire questa procedura per creare un nuovo webhook collegato a un Runbook nel portale di Azure.

1. Nella pagina Runbook nel portale di Azure fare clic sul runbook avviato dal webhook per visualizzare i dettagli del runbook. Verificare che il campo **Stato** del runbook sia impostato su **Pubblicato**.
2. Fare clic su **Webhook** nella parte superiore della pagina per aprire la pagina Aggiungi webhook.
3. Fare clic su **Creare un nuovo webhook** per aprire la pagina Crea webhook.
4. Compilare i campi **Nome** e **Data di scadenza** per il webhook e specificare se deve essere abilitato. Per altre informazioni su queste proprietà, vedere [Proprietà dei webhook](#webhook-properties).
5. Fare clic sull'icona di copia e premere CTRL+C per copiare l'URL del webhook. Annotarlo in un luogo sicuro. 

    > [!NOTE]
    > Dopo la creazione del webhook, non è possibile recuperare di nuovo l'URL.

   ![URL webhook](media/automation-webhooks/copy-webhook-url.png)

1. Fare clic su **Parameters** per specificare i valori per i parametri del Runbook. Se il runbook contiene parametri obbligatori, è possibile creare il webhook solo se si specificano i valori di tali parametri.

2. Fare clic su **Create** per creare il webhook.

## <a name="use-a-webhook"></a>Usare un webhook

Per usare un webhook dopo che è stato creato, il client deve inviare una richiesta HTTP `POST` con l'URL del webhook. La sintassi è:

```http
http://<Webhook Server>/token?=<Token Value>
```

Il client riceve uno dei codici restituiti seguenti dalla richiesta `POST`.

| Codice | Text | Descrizione |
|:--- |:--- |:--- |
| 202 |Accepted |La richiesta è stata accettata e il Runbook è stato accodato. |
| 400 |Bad Request |La richiesta non è stata accettata per uno dei motivi seguenti: <ul> <li>Il webhook è scaduto.</li> <li>Il webhook è disabilitato.</li> <li>Il token nell'URL non è valido.</li>  </ul> |
| 404 |Non trovato |La richiesta non è stata accettata per uno dei motivi seguenti: <ul> <li>Il webhook non è stato trovato.</li> <li>Il runbook non è stato trovato.</li> <li>L'account non è stato trovato.</li>  </ul> |
| 500 |Internal Server Error |L'URL è valido, ma si è verificato un errore. Inviare di nuovo la richiesta. |

Se la richiesta ha esito positivo, la risposta del webhook conterrà l'ID del processo in formato JSON come illustrato di seguito. Contiene un singolo ID processo, ma il formato JSON consente potenziali miglioramenti futuri.

```json
{"JobIds":["<JobId>"]}
```

Il client non è in grado di determinare quando viene completato il processo del runbook o lo stato di avanzamento dal webhook. Può recuperare queste informazioni usando l'ID processo con un altro metodo, ad esempio [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) o l'[API di Automazione di Azure](/rest/api/automation/job).

## <a name="renew-a-webhook"></a>Rinnovare un webhook

Quando viene creato, un webhook ha un periodo di validità di dieci anni, dopo il quale scade automaticamente. Dopo che il webhook è scaduto, non è possibile riattivarlo. È solo possibile rimuoverlo e ricrearlo. 

È possibile estendere un webhook che non abbia raggiunto la data di scadenza. Per estendere un webhook:

1. Passare al runbook che contiene il webhook. 
2. Selezionare **Webhook** sotto **Risorse**. 
3. Fare clic sul webhook che si vuole estendere. 
4. Nella pagina Webhook scegliere una nuova data e una nuova ora di scadenza e fare clic su **Salva**.

## <a name="sample-runbook"></a>Runbook di esempio

Il runbook di esempio seguente accetta i dati del webhook e avvia le macchine virtuali specificate nel corpo della richiesta. Per testare il runbook, nell'account di Automazione, in **Runbook**, fare clic su **Crea un runbook**. Se non si conosce la procedura di creazione di un runbook, vedere [Creare un runbook di Automazione di Azure](automation-quickstart-create-runbook.md).

> [!NOTE]
> Per runbook PowerShell non grafici, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli, anche se è stato appena creato un nuovo account di Automazione.

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

    # Retrieve VMs from Webhook request body
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
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>Testare l'esempio

L'esempio seguente usa Windows PowerShell per avviare un Runbook con un webhook. Un webhook può essere usato da qualsiasi linguaggio in grado di effettuare una richiesta HTTP. Windows PowerShell viene usato qui come esempio.

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

L'esempio seguente illustra il corpo della richiesta disponibile per il runbook nella proprietà `RequestBody` di `WebhookData`. Questo valore viene formattato in JSON perché sia compatibile con il formato incluso nel corpo della richiesta.

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

* Per attivare un runbook da un avviso, vedere [Usare un avviso per attivare un runbook di Automazione di Azure](automation-create-alert-triggered-runbook.md).
