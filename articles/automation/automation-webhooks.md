---
title: Avviare un runbook di Automazione di Azure con un webhook
description: Un webhook che consente a un client di avviare un Runbook in Automazione di Azure da una chiamata HTTP.  Questo articolo descrive come creare un webhook e come chiamarne uno per avviare un Runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367024"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Avviare un runbook di Automazione di Azure con un webhook

Un webhook consente a un servizio esterno di avviare un runbook specifico in Automazione di Azure tramite una singola richiesta HTTP. I servizi esterni includono i servizi DevOps di Azure, GitHub, i log di Monitoraggio di Azure e le applicazioni personalizzate. Un servizio di questo tipo può usare un webhook per avviare un runbook senza implementare una soluzione completa usando l'API di automazione di Azure.Such a service can use a webhook to start a runbook without implementing a full solution using the Azure Automation API. È possibile confrontare i webhook con altri metodi di avvio di un runbook in Avvio di [un runbook in Automazione di Azure.](automation-starting-a-runbook.md)

> [!NOTE]
> L'utilizzo di un webhook per avviare un runbook Python non è supportato.

![Panoramica dei webhook](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="webhook-properties"></a>Proprietà webhook

La tabella seguente descrive le proprietà che devono essere configurate per un webhook.

| Proprietà | Descrizione |
|:--- |:--- |
| Nome |Nome del webhook. È possibile fornire qualsiasi nome desiderato, poiché non è esposto al client. Il nome viene usato solo per consentire all'utente di identificare il runbook in Automazione di Azure. Come procedura consigliata è opportuno assegnare al webhook un nome correlato al client in cui verrà usato. |
| URL |URL del webhook. Si tratta dell'indirizzo univoco che un client chiama con un HTTP POST per avviare il runbook collegato al webhook. Viene generato automaticamente al momento della creazione del webhook. Non è possibile specificare un URL personalizzato. <br> <br> L'URL contiene un token di sicurezza che consente a un sistema di terze parti di richiamare il runbook senza ulteriore autenticazione. Per questo motivo, è necessario considerare l'URL come una password. Per motivi di sicurezza, è possibile visualizzare l'URL solo nel portale di Azure durante la creazione del webhook. Prendere nota dell'URL e conservarlo in un luogo sicuro per usi futuri. |
| Expiration date | Data di scadenza del webhook, dopo di che non può più essere utilizzato. È possibile modificare la data di scadenza dopo la creazione del webhook, purché il webhook non sia scaduto. |
| Attivato | Impostazione che indica se il webhook è abilitato per impostazione predefinita al momento della creazione. Se si imposta questa proprietà su Disabled, nessun client può utilizzare il webhook. È possibile impostare questa proprietà quando si crea il webhook o qualsiasi altro momento dopo la creazione. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametri utilizzati all'avvio di un runbook da parte del webhook

Un webhook può definire i valori per i parametri del runbook che vengono utilizzati all'avvio del runbook. Il webhook deve includere valori per qualsiasi parametro del runbook obbligatorio e può includere valori per i parametri facoltativi. Un valore di parametro configurato per un webhook può essere modificato anche dopo la creazione del webhook. Più webhook collegati a un singolo runbook possono usare valori di parametro del runbook diversi. Quando avvia un runbook con un webhook, un client non può eseguire l'override dei valori di parametri definiti nel webhook.

Per ricevere dati dal client, il runbook `WebhookData`supporta un singolo parametro denominato . Questo parametro definisce un oggetto contenente i dati inclusi dal client in una richiesta POST.

![Proprietà WebhookData](media/automation-webhooks/webhook-data-properties.png)

Il parametro `WebhookData` ha le proprietà seguenti:

| Proprietà | Descrizione |
|:--- |:--- |
| `WebhookName` | Nome del webhook. |
| `RequestHeader` | Hashtable contenente le intestazioni della richiesta POST in ingresso. |
| `RequestBody` | Corpo della richiesta POST in ingresso. Questo corpo mantiene qualsiasi formattazione dei dati, ad esempio stringa, JSON, XML o con codifica per form. Il Runbook deve essere scritto in modo che funzioni con il formato di dati previsto. |

Non è necessaria alcuna configurazione del `WebhookData` webhook per supportare il parametro e il runbook non è necessario accettarlo. Se il runbook non definisce il parametro, tutti i dettagli della richiesta inviata dal client vengono ignorati.

> [!NOTE]
> Quando si chiama un webhook, il client deve sempre archiviare i valori dei parametri nel caso in cui la chiamata non riesca. Se si verifica un'interruzione della rete o un problema di connessione, l'applicazione non può recuperare le chiamate webhook non riuscite.

Se si specifica `WebhookData` un valore per al momento della creazione di webhook, viene eseguito l'override quando il webhook avvia il runbook con i dati della richiesta POST del client. Ciò si verifica anche se l'applicazione non include dati nel corpo della richiesta. 

Se si avvia un runbook che definisce `WebhookData` l'utilizzo di un `WebhookData` meccanismo diverso da un webhook, è possibile fornire un valore per tale valore riconosciuto dal runbook. Questo valore deve essere un [properties](#webhook-properties) oggetto `WebhookData` con le stesse proprietà del parametro in modo `WebhookData` che il runbook possa utilizzarlo come funziona con gli oggetti effettivi passati da un webhook.

Ad esempio, se si sta avviando il runbook seguente dal portale di Azure e si desidera passare alcuni dati webhook di esempio per il test, è necessario passare i dati in JSON nell'interfaccia utente.

![Parametro WebhookData dall'interfaccia utente](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Per il successivo esempio di runbook, definiamo le seguenti proprietà per: `WebhookData`

* **WebhookName**: MyWebhook
* **RequestBody**:`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

A questo punto passiamo l'oggetto `WebhookData` JSON seguente nell'interfaccia utente per il parametro. In questo esempio, con ritorni a capo e caratteri di nuova riga, corrisponde al formato passato da un webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Parametro WebhookData Start dall'interfaccia utente](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Automazione di Azure registra i valori di tutti i parametri di input con il processo del runbook. Pertanto, qualsiasi input fornito dal client nella richiesta webhook viene registrato e disponibile per chiunque abbia accesso al processo di automazione. Per questo motivo è consigliabile procedere con cautela quando si includono dati sensibili nelle chiamate di webhook.

## <a name="webhook-security"></a>Sicurezza webhook

La sicurezza di un webhook si basa sulla privacy del relativo URL, che contiene un token di sicurezza che consente di richiamare il webhook. Automazione di Azure non esegue alcuna autenticazione in una richiesta, purché venga effettuata all'URL corretto. Per questo motivo, i client non devono usare webhook per runbook che eseguono operazioni altamente sensibili senza utilizzare un mezzo alternativo per convalidare la richiesta.

È possibile includere la logica all'interno di un runbook per determinare se viene chiamato da un webhook. Fare in modo `WebhookName` che il `WebhookData` runbook controlli la proprietà del parametro. Il runbook può eseguire un'ulteriore `RequestHeader` convalida `RequestBody` cercando informazioni specifiche nelle proprietà e .

Un'altra strategia consiste nel fare in modo che il runbook esegua una convalida di una condizione esterna quando riceve una richiesta di webhook. Si consideri, ad esempio, un runbook che viene chiamato da GitHub ogni volta che è presente un nuovo commit in un repository GitHub.For example, consider a runbook that is called by GitHub any time there's a new commit to a GitHub repository. Il runbook potrebbe connettersi a GitHub per verificare che si sia verificato un nuovo commit prima di continuare.

## <a name="creating-a-webhook"></a>Creazione di un webhook

Seguire questa procedura per creare un nuovo webhook collegato a un Runbook nel portale di Azure.

1. Nella pagina Runbooks del portale di Azure fare clic sul runbook avviato dal webhook per visualizzare i dettagli del runbook. Assicurarsi che il campo **Stato** runbook sia impostato su **Pubblicato**.
2. Fare clic su **Webhook** nella parte superiore della pagina per aprire la pagina Aggiungi webhook.
3. Fare clic su **Crea nuovo webhook** per aprire la pagina Crea webhook.
4. Compilare i campi **Nome** e **Data di scadenza** per il webhook e specificare se deve essere abilitato. Per ulteriori informazioni su queste proprietà, vedere [Proprietà Webhook.](#webhook-properties)
5. Fare clic sull'icona di copia e premere CTRL+C per copiare l'URL del webhook. Annotarlo in un luogo sicuro. 

    > [!NOTE]
    > Dopo aver creato il webhook, non è possibile recuperare di nuovo l'URL.

   ![URL webhook](media/automation-webhooks/copy-webhook-url.png)

1. Fare clic su **Parameters** per specificare i valori per i parametri del Runbook. Se il runbook ha parametri obbligatori, non è possibile creare il webhook a meno che non si forniscano valori.
1. Fare clic su **Create** per creare il webhook.

## <a name="using-a-webhook"></a>Uso di un webhook

Per utilizzare un webhook dopo che è stato `POST` creato, il client deve inviare una richiesta HTTP con l'URL per il webhook. La sintassi è:

```http
http://<Webhook Server>/token?=<Token Value>
```

Il client riceve uno dei seguenti `POST` codici restituiti dalla richiesta.

| Codice | Testo | Descrizione |
|:--- |:--- |:--- |
| 202 |Accepted |La richiesta è stata accettata e il Runbook è stato accodato. |
| 400 |Bad Request |La richiesta non è stata accettata per uno dei motivi seguenti: <ul> <li>Il webhook è scaduto.</li> <li>Il webhook è disabilitato.</li> <li>Il token nell'URL non è valido.</li>  </ul> |
| 404 |Non trovato |La richiesta non è stata accettata per uno dei motivi seguenti: <ul> <li>Il webhook non è stato trovato.</li> <li>Il runbook non è stato trovato.</li> <li>L'account non è stato trovato.</li>  </ul> |
| 500 |Internal Server Error |L'URL è valido, ma si è verificato un errore. Inviare di nuovo la richiesta. |

Supponendo che la richiesta abbia esito positivo, la risposta webhook contiene l'ID processo in formato JSON, come illustrato di seguito. Contiene un singolo ID processo, ma il formato JSON consente potenziali miglioramenti futuri.

```json
{"JobIds":["<JobId>"]}
```

Il client non è in grado di determinare quando viene completato il processo del runbook o lo stato di avanzamento dal webhook. È possibile trovare queste informazioni usando l'ID processo con un altro meccanismo, ad esempio [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) o l'API di automazione di [Azure.](/rest/api/automation/job)

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>Rinnovo di un webhook

Quando viene creato, un webhook ha un periodo di validità di dieci anni, dopo di che scade automaticamente. Una volta scaduto un webhook, non puoi riattivarlo. È possibile solo rimuoverlo e quindi ricrearlo. 

È possibile estendere un webhook che non ha raggiunto l'ora di scadenza. Per estendere un webhook:

1. Passare al runbook che contiene il webhook. 
2. Selezionare **Webhook** sotto **Risorse**. 
3. Fare clic sul webhook che si desidera estendere. 
4. Nella pagina Webhook scegliere una nuova data e un'ora di scadenza e fare clic su **Salva**.

## <a name="sample-runbook"></a>Runbook di esempio

Il runbook di esempio seguente accetta i dati del webhook e avvia le macchine virtuali specificate nel corpo della richiesta. Per testare questo runbook, nell'account di automazione in **Runbooks**fare clic su **Create a runbook**. Se non si conosce la procedura di creazione di un runbook, vedere [Creare un runbook di Automazione di Azure](automation-quickstart-create-runbook.md).

> [!NOTE]
> Per i runbook di `Add-AzAccount` PowerShell non grafici e `Add-AzureRMAccount` sono alias per [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile utilizzare questi cmdlet [oppure aggiornare i moduli](automation-update-azure-modules.md) nell'account di automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli anche se è stato appena creato un nuovo account di automazione.

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

## <a name="testing-the-sample"></a>Test dell'esempio

L'esempio seguente usa Windows PowerShell per avviare un Runbook con un webhook. Qualsiasi lingua che può effettuare una richiesta HTTP può utilizzare un webhook. Windows PowerShell viene usato qui come esempio.

Il Runbook prevede un elenco di macchine virtuali in formato JSON nel corpo della richiesta. Il runbook verifica inoltre che le intestazioni contengano un messaggio definito per convalidare che il chiamante webhook sia valido.

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

Nell'esempio `RequestBody` seguente viene illustrato il corpo della richiesta disponibile `WebhookData`per il runbook nella proprietà di . Questo valore viene formattato in JSON per essere compatibile con il formato incluso nel corpo della richiesta.

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

* Per informazioni su come usare Automazione di Azure per eseguire azioni sugli avvisi di Azure, vedere Usare un avviso per attivare un runbook di Automazione di Azure.To learn how to use Azure Automation to take action on Azure alerts, see [Use an alert to trigger an Azure Automation runbook.](automation-create-alert-triggered-runbook.md)
