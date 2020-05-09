---
title: Avviare un runbook di Automazione di Azure con un webhook
description: Un webhook che consente a un client di avviare un Runbook in Automazione di Azure da una chiamata HTTP.  Questo articolo descrive come creare un webhook e come chiamarne uno per avviare un Runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: cbe43b298c57d266f0b031b5192f25fe3df07c05
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582440"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Avviare un runbook di Automazione di Azure con un webhook

Un webhook consente a un servizio esterno di avviare un Runbook specifico in automazione di Azure tramite una singola richiesta HTTP. I servizi esterni includono Azure DevOps Services, GitHub, log di monitoraggio di Azure e applicazioni personalizzate. Un servizio di questo tipo può usare un webhook per avviare un Runbook senza implementare una soluzione completa usando l'API di automazione di Azure. È possibile confrontare i webhook con altri metodi di avvio di un Runbook nell' [avvio di un Runbook in automazione di Azure](automation-starting-a-runbook.md).

> [!NOTE]
> L'uso di un webhook per avviare un runbook Python non è supportato.

![Panoramica dei webhook](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="webhook-properties"></a>Proprietà webhook

La tabella seguente descrive le proprietà che devono essere configurate per un webhook.

| Proprietà | Descrizione |
|:--- |:--- |
| Nome |Nome del webhook. È possibile specificare qualsiasi nome desiderato, perché non è esposto al client. Il nome viene usato solo per consentire all'utente di identificare il runbook in Automazione di Azure. Come procedura consigliata è opportuno assegnare al webhook un nome correlato al client in cui verrà usato. |
| URL |URL del webhook. Si tratta dell'indirizzo univoco chiamato da un client con un HTTP POST per avviare il Runbook collegato al webhook. Viene generato automaticamente al momento della creazione del webhook. Non è possibile specificare un URL personalizzato. <br> <br> L'URL contiene un token di sicurezza che consente a un sistema di terze parti di richiamare Runbook senza ulteriore autenticazione. Per questo motivo, è consigliabile considerare l'URL come una password. Per motivi di sicurezza, è possibile visualizzare l'URL solo nel portale di Azure durante la creazione del webhook. Prendere nota dell'URL e conservarlo in un luogo sicuro per usi futuri. |
| Expiration date | Data di scadenza del webhook, dopo la quale non può più essere utilizzata. È possibile modificare la data di scadenza dopo la creazione del webhook, purché il webhook non sia scaduto. |
| Attivato | Impostazione che indica se il webhook è abilitato per impostazione predefinita al momento della creazione. Se si imposta questa proprietà su disabled, nessun client può usare il webhook. È possibile impostare questa proprietà quando si crea il webhook o qualsiasi altro momento dopo la relativa creazione. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametri usati quando il webhook avvia un Runbook

Un webhook può definire i valori per i parametri Runbook usati all'avvio del Runbook. Il webhook deve includere i valori per i parametri Runbook obbligatori e può includere valori per i parametri facoltativi. Un valore di parametro configurato per un webhook può essere modificato anche dopo la creazione di un webhook. Più webhook collegati a un singolo Runbook possono usare valori di parametri Runbook diversi. Quando avvia un runbook con un webhook, un client non può eseguire l'override dei valori di parametri definiti nel webhook.

Per ricevere dati dal client, Runbook supporta un solo parametro denominato `WebhookData`. Questo parametro definisce un oggetto contenente i dati inclusi dal client in una richiesta POST.

![Proprietà di WebhookData](media/automation-webhooks/webhook-data-properties.png)

Il parametro `WebhookData` ha le proprietà seguenti:

| Proprietà | Descrizione |
|:--- |:--- |
| `WebhookName` | Nome del webhook. |
| `RequestHeader` | Tabella hash contenente le intestazioni della richiesta POST in ingresso. |
| `RequestBody` | Corpo della richiesta POST in ingresso. Questo corpo mantiene la formattazione dei dati, ad esempio stringa, JSON, XML o con codifica form. Il Runbook deve essere scritto in modo che funzioni con il formato di dati previsto. |

Non è necessaria alcuna configurazione del webhook per supportare il `WebhookData` parametro e il Runbook non è necessario per accettarlo. Se il Runbook non definisce il parametro, tutti i dettagli della richiesta inviata dal client verranno ignorati.

> [!NOTE]
> Quando si chiama un webhook, il client deve sempre archiviare tutti i valori dei parametri in caso di errore della chiamata. Se si verifica un'interruzione di rete o un problema di connessione, l'applicazione non può recuperare le chiamate al webhook non riuscite.

Se si specifica un valore per `WebhookData` la creazione di Webhook, questo viene sostituito quando il webhook avvia il Runbook con i dati della richiesta post del client. Ciò si verifica anche se l'applicazione non include dati nel corpo della richiesta. 

Se si avvia un Runbook che definisce `WebhookData` l'uso di un meccanismo diverso da un webhook, è possibile fornire un `WebhookData` valore per il riconoscimento da parte di Runbook. Questo valore deve essere un oggetto con le stesse [Proprietà](#webhook-properties) del `WebhookData` parametro in modo che il Runbook possa funzionare con esso esattamente come funziona con gli oggetti `WebhookData` effettivi passati da un webhook.

Ad esempio, se si avviano i Runbook seguenti dal portale di Azure e si vogliono passare alcuni dati di Webhook di esempio per il test, è necessario passare i dati in JSON nell'interfaccia utente.

![Parametro WebhookData dall'interfaccia utente](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Per l'esempio Runbook successivo, definire le proprietà seguenti per `WebhookData`:

* **Webhookname**: MyWebhook
* **RequestBody**:`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

A questo punto, viene passato l'oggetto JSON seguente nell'interfaccia `WebhookData` utente per il parametro. Questo esempio, con i caratteri di ritorno a capo e di nuova riga, corrisponde al formato passato da un webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Parametro WebhookData Start dall'interfaccia utente](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Automazione di Azure registra i valori di tutti i parametri di input con il processo Runbook. Quindi, qualsiasi input fornito dal client nella richiesta del webhook viene registrato e disponibile a chiunque abbia accesso al processo di automazione. Per questo motivo è consigliabile procedere con cautela quando si includono dati sensibili nelle chiamate di webhook.

## <a name="webhook-security"></a>Sicurezza webhook

La sicurezza di un webhook si basa sulla privacy del relativo URL, che contiene un token di sicurezza che consente di richiamare il webhook. Automazione di Azure non esegue alcuna autenticazione su una richiesta purché venga eseguita nell'URL corretto. Per questo motivo, i client non devono usare i webhook per manuali operativi che eseguono operazioni estremamente sensibili senza usare un metodo alternativo per convalidare la richiesta.

È possibile includere la logica in un Runbook per determinare se viene chiamata da un webhook. Fare in Runbook controllare la `WebhookName` proprietà del `WebhookData` parametro. Runbook è in grado di eseguire ulteriori operazioni di convalida cercando informazioni specifiche `RequestHeader` nelle `RequestBody` proprietà e.

Un'altra strategia consiste nel fare in modo che il Runbook esegua una convalida di una condizione esterna quando riceve una richiesta di webhook. Si consideri, ad esempio, un Runbook che viene chiamato da GitHub ogni volta che viene eseguito un nuovo commit in un repository GitHub. Runbook potrebbe connettersi a GitHub per verificare che si sia verificato un nuovo commit prima di continuare.

## <a name="create-a-webhook"></a>Creare un webhook

Seguire questa procedura per creare un nuovo webhook collegato a un Runbook nel portale di Azure.

1. Dalla pagina manuali operativi nella portale di Azure fare clic sul Runbook di avvio del webhook per visualizzare i dettagli Runbook. Verificare che il campo **stato** Runbook sia impostato su **pubblicato**.
2. Fare clic su **Webhook** nella parte superiore della pagina per aprire la pagina Aggiungi webhook.
3. Fare clic su **Crea nuovo webhook** per aprire la pagina Crea webhook.
4. Inserire i campi **nome** e **Data di scadenza** per il webhook e specificare se deve essere abilitato. Per ulteriori informazioni su queste proprietà, vedere [Proprietà webhook](#webhook-properties) .
5. Fare clic sull'icona di copia e premere CTRL+C per copiare l'URL del webhook. Annotarlo in un luogo sicuro. 

    > [!NOTE]
    > Dopo aver creato il webhook, non è possibile recuperare di nuovo l'URL.

   ![URL webhook](media/automation-webhooks/copy-webhook-url.png)

1. Fare clic su **Parameters** per specificare i valori per i parametri del Runbook. Se il Runbook ha parametri obbligatori, non è possibile creare il webhook a meno che non si forniscano i valori.
1. Fare clic su **Create** per creare il webhook.

## <a name="use-a-webhook"></a>Usare un webhook

Per usare un webhook dopo che è stato creato, il client deve emettere una richiesta `POST` http con l'URL del webhook. La sintassi è:

```http
http://<Webhook Server>/token?=<Token Value>
```

Il client riceve uno dei seguenti codici restituiti dalla `POST` richiesta.

| Codice | Testo | Descrizione |
|:--- |:--- |:--- |
| 202 |Accepted |La richiesta è stata accettata e il Runbook è stato accodato. |
| 400 |Bad Request |La richiesta non è stata accettata per uno dei motivi seguenti: <ul> <li>Il webhook è scaduto.</li> <li>Il webhook è disabilitato.</li> <li>Il token nell'URL non è valido.</li>  </ul> |
| 404 |Non trovato |La richiesta non è stata accettata per uno dei motivi seguenti: <ul> <li>Il webhook non è stato trovato.</li> <li>Il runbook non è stato trovato.</li> <li>L'account non è stato trovato.</li>  </ul> |
| 500 |Internal Server Error |L'URL è valido, ma si è verificato un errore. Inviare di nuovo la richiesta. |

Supponendo che la richiesta abbia esito positivo, la risposta del webhook contiene l'ID processo in formato JSON, come illustrato di seguito. Contiene un singolo ID processo, ma il formato JSON consente possibili miglioramenti futuri.

```json
{"JobIds":["<JobId>"]}
```

Il client non è in grado di determinare quando viene completato il processo del runbook o lo stato di avanzamento dal webhook. È possibile trovare queste informazioni usando l'ID processo con un altro meccanismo, ad esempio [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) o l' [API di automazione di Azure](/rest/api/automation/job).

## <a name="renew-a-webhook"></a>Rinnovare un webhook

Quando viene creato un webhook, ha un periodo di validità di dieci anni, dopo il quale scade automaticamente. Una volta scaduto il webhook, non è possibile riattivarlo. È possibile rimuovere solo e ricrearlo. 

È possibile estendere un webhook che non ha raggiunto la data di scadenza. Per estendere un webhook:

1. Passare a Runbook che contiene il webhook. 
2. Selezionare **Webhook** sotto **Risorse**. 
3. Fare clic sul webhook che si desidera estendere. 
4. Nella pagina webhook scegliere una nuova data e ora di scadenza e fare clic su **Salva**.

## <a name="sample-runbook"></a>Runbook di esempio

Il runbook di esempio seguente accetta i dati del webhook e avvia le macchine virtuali specificate nel corpo della richiesta. Per testare questo Runbook, fare clic su **Crea un Runbook**nell'account di automazione in **manuali operativi**. Se non si conosce la procedura di creazione di un runbook, vedere [Creare un runbook di Automazione di Azure](automation-quickstart-create-runbook.md).

> [!NOTE]
> Per manuali operativi di PowerShell non grafici, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli, anche se è stato appena creato un nuovo account di Automazione.

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

L'esempio seguente usa Windows PowerShell per avviare un Runbook con un webhook. Qualsiasi linguaggio in grado di effettuare una richiesta HTTP può usare un webhook. Windows PowerShell viene usato come esempio.

Il Runbook prevede un elenco di macchine virtuali in formato JSON nel corpo della richiesta. Runbook convalida anche che le intestazioni contengono un messaggio definito per verificare che il chiamante del webhook sia valido.

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

Nell'esempio seguente viene illustrato il corpo della richiesta disponibile per Runbook nella `RequestBody` proprietà di. `WebhookData` Questo valore è formattato in JSON per essere compatibile con il formato incluso nel corpo della richiesta.

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

* Per informazioni su come usare automazione di Azure per eseguire azioni sugli avvisi di Azure, vedere [usare un avviso per attivare un Runbook di automazione di Azure](automation-create-alert-triggered-runbook.md).
