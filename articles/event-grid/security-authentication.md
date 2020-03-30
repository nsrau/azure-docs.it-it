---
title: Sicurezza e autenticazione di Griglia di eventi di Azure
description: In questo articolo vengono descritti diversi modi per autenticare l'accesso alle risorse della griglia di eventi (WebHook, sottoscrizioni, argomenti personalizzati)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281015"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Autenticazione dell'accesso alle risorse della griglia di eventiAuthenticating access to Event Grid resources

Griglia di eventi di Azure ha tre tipi di autenticazione:

* Recapito eventi webhook
* Sottoscrizioni di eventi
* Pubblicazione di argomenti personalizzata

## <a name="webhook-event-delivery"></a>Recapito eventi webhook

I webhook sono uno dei modi per ricevere gli eventi da Griglia di eventi di Azure. Quando un nuovo evento è pronto, il servizio Griglia di eventi esegue il POST di una richiesta HTTP nell'endpoint configurato con l'evento nel corpo della richiesta.

Analogamente a molti altri servizi che supportano i webhook, Griglia di eventi richiede la dimostrazione della proprietà dell'endpoint del webhook prima dell'inizio del recapito di eventi a tale endpoint. Questo requisito impedisce a un utente malintenzionato di sovraccaricare l'endpoint con eventi. Quando si usa uno dei tre servizi di Azure elencati di seguito, l'infrastruttura di Azure gestisce automaticamente questa convalida:

* App per la logica di Azure con il [connettore Griglia di eventi](https://docs.microsoft.com/connectors/azureeventgrid/)
* Automazione di Azure tramite [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Funzioni di Azure con il [trigger Griglia di eventi](../azure-functions/functions-bindings-event-grid.md)

Se si usa un altro tipo di endpoint, ad esempio un trigger HTTP basato su una funzione di Azure, il codice dell'endpoint deve partecipare a un handshake di convalida con Griglia di eventi. Griglia di eventi supporta due modalità di convalida della sottoscrizione.

1. **ValidationCode handshake (programmatic):** se si controlla il codice sorgente per l'endpoint, questo metodo è consigliato. In fase di creazione della sottoscrizione di eventi, Griglia di eventi invia un evento di convalida della sottoscrizione all'endpoint. Lo schema di questo evento è simile a qualsiasi altro evento di Griglia di eventi. La parte di dati dell'evento include una proprietà `validationCode`. L'applicazione verifica che la richiesta di convalida sia per una sottoscrizione di eventi prevista e restituisce il codice di convalida a Griglia di eventi. Questo meccanismo di handshake è supportato in tutte le versioni di Griglia di eventi.

2. **Handshake ValidationURL (manuale):** in alcuni casi, non è possibile accedere al codice sorgente dell'endpoint per implementare l'handshake ValidationCode. Se ad esempio si usa un servizio di terze parti, come [Zapier](https://zapier.com) o [IFTTT](https://ifttt.com/), non è possibile rispondere con il codice di convalida a livello di programmazione.

   A partire dalla versione 2018-05-01-preview, Griglia di eventi supporta un handshake di convalida manuale. Se si sta creando una sottoscrizione di eventi con un SDK o uno strumento che usa l'API 2018-05-01-preview o versione successiva, Griglia di eventi invia una proprietà `validationUrl` nella parte di dati dell'evento di convalida della sottoscrizione. Per completare l'handshake, trovare l'URL nei dati dell'evento e inviare manualmente una richiesta GET a tali dati. È possibile usare un client REST o un Web browser.

   L'URL fornito è valido per 5 minuti. Durante questo periodo, lo stato di provisioning della sottoscrizione di eventi è `AwaitingManualAction`. Se non si completa la convalida manuale entro 5 `Failed`minuti, lo stato di provisioning viene impostato su . È possibile creare la sottoscrizione all'evento nuovamente prima di avviare la convalida manuale.

    Questo meccanismo di autenticazione richiede inoltre che l'endpoint webhook restituisca un codice di stato HTTP 200 in modo che sappia che il POST per l'evento di convalida è stato accettato prima di poter essere inserito nella modalità di convalida manuale. In altre parole, se l'endpoint restituisce 200 ma non restituisce una risposta di convalida a livello di codice, la modalità viene passata alla modalità di convalida manuale. Se è presente un GET nell'URL di convalida entro 5 minuti, l'handshake di convalida viene considerato riuscito.

> [!NOTE]
> L'utilizzo di certificati autofirmati per la convalida non è supportato. Utilizzare invece un certificato firmato da un'autorità di certificazione (CA).

### <a name="validation-details"></a>Dettagli di convalida

* In fase di creazione/aggiornamento della sottoscrizione di eventi, Griglia di eventi inserisce un evento di convalida della sottoscrizione nell'endpoint di destinazione. 
* L'evento contiene un valore di intestazione "aeg-event-type: SubscriptionValidation".
* Il corpo dell'evento ha lo stesso schema degli altri eventi di Griglia di eventi.
* La proprietà eventType dell'evento è `Microsoft.EventGrid.SubscriptionValidationEvent`.
* La proprietà Data dell'evento include una proprietà `validationCode` con una stringa generata in modo casuale. ad esempio "validationCode: acb13…".
* I dati dell'evento includono anche una proprietà `validationUrl` con un URL che è possibile usare per convalidare manualmente la sottoscrizione.
* La matrice contiene solo l'evento di convalida. Gli altri eventi vengono inviati in una richiesta separata dopo che è stato rimandato il codice di convalida.
* Gli SDK DataPlane di Griglia di eventi includono classi corrispondenti ai dati degli eventi di convalida della sottoscrizione e alla risposta di convalida della sottoscrizione.

Un esempio di SubscriptionValidationEvent è mostrato di seguito:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Per dimostrare la proprietà dell'endpoint, rimandare il codice di convalida nella proprietà validationResponse, come mostrato nell'esempio seguente:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

È necessario restituire un codice di stato risposta HTTP 200 OK. HTTP 202 Accettata non una risposta di convalida di sottoscrizione di Griglia di eventi riconosciuta come valida. La richiesta http deve essere completata entro 30 secondi. Se l'operazione non viene completata entro 30 secondi, l'operazione verrà annullata e potrebbe essere ritentata dopo 5 secondi. Se tutti i tentativi hanno esito negativo, verrà considerato come errore di handshake di convalida.

In alternativa, è possibile convalidare manualmente la sottoscrizione inviando una richiesta GET all'URL di convalida. La sottoscrizione dell'evento rimane nello stato in sospeso fino a quando non viene convalidata. L'URL di convalida utilizza la porta 553.The validation Url uses port 553. Se le regole del firewall bloccano la porta 553, potrebbe essere necessario aggiornare le regole per un handshake manuale riuscito.

Per un esempio di gestione dell'handshake di convalida della sottoscrizione, vedere un [ esempio C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Elenco di controllo

Durante la creazione della sottoscrizione di eventi, se viene visualizzato un messaggio\/di errore del tipo "Il tentativo di convalidare l'endpoint fornito https: /your-endpoint-here non è riuscito. Per ulteriori dettagli,\/visitare https: /aka.ms/esvalidation", indica che si è verificato un errore nell'handshake di convalida. Per risolvere questo errore, verificare gli aspetti seguenti:

* È possibile controllare il codice dell'applicazione in esecuzione nell'endpoint di destinazione? Se, ad esempio, si scrive una funzione di Azure basata su un trigger HTTP, si è autorizzati ad accedere al codice dell'applicazione per modificarlo?
* Se si è autorizzati ad accedere al codice dell'applicazione, implementare il meccanismo di handshake basato su ValidationCode, come illustrato nell'esempio precedente.

* Se non si è autorizzati ad accedere al codice dell'applicazione, ad esempio se si usa un servizio di terze parti che supporta webhook, è possibile usare il meccanismo di handshake manuale. Assicurarsi di usare la versione dell'API 2018-05-01-preview o versione successiva (installare l'estensione dell'interfaccia della riga di comando di Azure della griglia di eventi) per ricevere validationUrl nell'evento di convalida. Per completare l'handshake di convalida manuale, ottenere il valore della proprietà `validationUrl` e visitare l'URL nel Web browser. Se la convalida ha esito positivo, verrà visualizzato un messaggio nel Web browser che indica che la convalida ha avuto esito positivo. Si noterà che provisioningState della sottoscrizione dell'evento è "Succeeded". 

### <a name="event-delivery-security"></a>Sicurezza del recapito degli eventi

#### <a name="azure-ad"></a>Azure AD

È possibile proteggere l'endpoint webhook usando Azure Active Directory per autenticare e autorizzare Griglia di eventi per pubblicare eventi negli endpoint. È necessario creare un'applicazione Azure Active Directory, creare un principio di ruolo e servizio nell'applicazione che autorizza Griglia di eventi e configurare la sottoscrizione di eventi per l'uso dell'applicazione Azure AD. [Informazioni su come configurare AAD con Griglia di eventi](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Parametri di query
È possibile proteggere l'endpoint webhook aggiungendo i parametri di query all'URL del webhook durante la creazione di una sottoscrizione di eventi. Impostare uno di questi parametri di query in modo che sia un segreto, ad esempio un [token di accesso](https://en.wikipedia.org/wiki/Access_token), che il webhook può usare per riconoscere che l'evento proviene da Griglia di eventi con autorizzazioni valide. Griglia di eventi includerà questi parametri di query in ogni recapito di eventi al webhook.

Quando si modifica la sottoscrizione dell'evento, i parametri di query non sono visualizzati o restituiti a meno che non venga usato il parametro [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) nell'[interfaccia della riga di comando](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) di Azure.

È infine importante notare che Griglia di eventi di Azure supporta solo endpoint di webhook HTTPS.

## <a name="event-subscription"></a>Sottoscrizione dell'evento

Per sottoscrivere un evento, è necessario dimostrare di avere accesso all'origine e al gestore dell'evento. Nella sezione precedente è stata trattata la dimostrazione di essere proprietari di un WebHook. Se si usa un gestore eventi che non è un WebHook, ad esempio, un hub eventi o un'archiviazione code, è necessario l'accesso in scrittura a tale risorsa. Questo controllo delle autorizzazioni impedisce che un utente non autorizzato invii eventi alla risorsa.

È necessaria l'autorizzazione **Microsoft.EventGrid/EventSubscriptions/Write** per la risorsa che è l'origine dell'evento. Questa autorizzazione è necessaria perché si sta scrivendo una nuova sottoscrizione nell'ambito della risorsa. La risorsa necessaria è diversa a seconda del fatto che si sottoscriva un argomento di sistema o un argomento personalizzato. Entrambi i tipi sono descritti in questa sezione.

### <a name="system-topics-azure-service-publishers"></a>Argomenti di sistema (entità di pubblicazione dei servizi di Azure)

Per gli argomenti di sistema, è necessaria l'autorizzazione per scrivere una nuova sottoscrizione di evento nell'ambito della risorsa che pubblica l'evento. Il formato della risorsa è: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Per sottoscrivere, ad esempio, un evento in un account di archiviazione denominato **myacct**, è necessaria l'autorizzazione Microsoft.EventGrid/EventSubscriptions/Write per: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Argomenti personalizzati

Per gli argomenti personalizzati, è necessaria l'autorizzazione per scrivere una nuova sottoscrizione di evento nell'ambito dell'argomento di Griglia di eventi. Il formato della risorsa è: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Per sottoscrivere, ad esempio, un argomento personalizzato denominato **mytopic**, è necessaria l'autorizzazione Microsoft.EventGrid/EventSubscriptions/Write per: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Pubblicazione di argomenti personalizzata

Gli argomenti personalizzati usano la firma di accesso condiviso (SAS) o l'autenticazione della chiave. È consigliabile la firma di accesso condiviso, ma l'autenticazione della chiave fornisce una programmazione semplice ed è compatibile con molte entità di pubblicazione di webhook esistenti. 

Il valore dell'autenticazione viene incluso nell'intestazione HTTP. Per la firma di accesso condiviso, usare **aeg-sas-token** per il valore dell'intestazione. Per l'autenticazione della chiave, usare **aeg-sas-key** per il valore dell'intestazione.

### <a name="key-authentication"></a>Autenticazione della chiave

L'autenticazione della chiave è la forma più semplice di autenticazione. Usare il formato: `aeg-sas-key: <your key>`

Ad esempio, passare una chiave con:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Token di firma di accesso condiviso

I token di firma di accesso condiviso per Griglia di eventi includono la risorsa, un'ora di scadenza e una firma. Il formato del token di firma di accesso condiviso è: `r={resource}&e={expiration}&s={signature}`.

La risorsa è il percorso dell'argomento di Griglia di eventi a cui si inviano gli eventi. Un percorso di risorsa valido, ad esempio, è: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

La firma viene generata da una chiave.

Un valore **aeg-sas-token** valido, ad esempio, è:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

L'esempio seguente crea un token di firma di accesso condiviso per l'uso con Griglia di eventi:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

Tutti gli eventi o i dati scritti su disco dal servizio Griglia di eventi vengono crittografati da una chiave gestita da Microsoft, assicurandosi che sia crittografata inattivi. Inoltre, il periodo di tempo massimo per cui gli eventi o i dati conservati sono 24 ore in conformità con il [criterio di ripetizione della griglia di](delivery-and-retry.md)eventi. Griglia di eventi eliminerà automaticamente tutti gli eventi o i dati dopo 24 ore o la time-to-live dell'evento, a seconda del valore minore.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni sulla griglia di eventiFor](overview.md) an introduction to Event Grid, see About Event Grid
