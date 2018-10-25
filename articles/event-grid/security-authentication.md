---
title: Sicurezza e autenticazione di Griglia di eventi di Azure
description: Vengono descritti il servizio Griglia di eventi di Azure e i concetti correlati.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: babanisa
ms.openlocfilehash: 2fd8712cbe5d34baed158a56e6f06b6235f5d4b2
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068196"
---
# <a name="event-grid-security-and-authentication"></a>Sicurezza e autenticazione di Griglia di eventi 

Griglia di eventi di Azure ha tre tipi di autenticazione:

* Recapito eventi webhook
* Sottoscrizioni di eventi
* Pubblicazione di argomenti personalizzata

## <a name="webhook-event-delivery"></a>Recapito eventi webhook

I webhook sono uno dei modi per ricevere gli eventi da Griglia di eventi di Azure. Quando un nuovo evento è pronto, il servizio Griglia di eventi esegue il POST di una richiesta HTTP nell'endpoint configurato con l'evento nel corpo della richiesta.

Analogamente a molti altri servizi che supportano i webhook, Griglia di eventi richiede la dimostrazione della "proprietà" dell'endpoint del webhook prima dell'inizio del recapito di eventi a tale endpoint. Questo requisito consente di impedire a un endpoint di diventare inconsapevolmente l'endpoint di destinazione per il recapito di eventi da Griglia di eventi. Quando tuttavia si usa uno dei tre servizi di Azure elencati di seguito, l'infrastruttura di Azure gestisce automaticamente questa convalida:

* App per la logica di Azure
* Automazione di Azure
* Funzioni di Azure per un trigger di Griglia di eventi

Se si usa un altro tipo di endpoint, ad esempio un trigger HTTP basato su una funzione di Azure, il codice dell'endpoint deve partecipare a un handshake di convalida con Griglia di eventi. Griglia di eventi supporta due diversi modelli di handshake di convalida:

1. **Handshake ValidationCode**: durante la creazione della sottoscrizione di eventi, il servizio Griglia di eventi esegue il POST di un "evento di convalida della sottoscrizione" nell'endpoint. Lo schema di questo evento è simile a qualsiasi altro evento di Griglia eventi e la parte "data" di tale evento include una proprietà `validationCode`. Quando l'applicazione ha verificato che la richiesta di convalida è relativa a una sottoscrizione di eventi prevista, il codice dell'applicazione deve rispondere restituendo il codice di convalida a Griglia di eventi. Questo meccanismo di handshake è supportato in tutte le versioni di Griglia di eventi.

2. **Handshake ValidationURL (handshake manuale)**: in determinati casi non è possibile controllare il codice sorgente dell'endpoint e quindi non è possibile implementare l'handshake basato su ValidationCode. Se ad esempio si usa un servizio di terze parti, ad esempio [Zapier](https://zapier.com) o [IFTTT](https://ifttt.com/), è possibile che non si riesca a restituire il codice di convalida a livello di programmazione. A partire dalla versione 2018-05-01-preview, EventGrid supporta quindi un handshake di convalida manuale. Se si sta creando una sottoscrizione di eventi tramite SDK/strumenti che usano questa nuova versione dell'API (2018-05-01-preview), EventGrid invia una proprietà `validationUrl` come parte della porzione "data" dell'evento di convalida della sottoscrizione. Per completare l'handshake, è sufficiente inviare una richiesta GET in tale URL, tramite un client REST oppure il Web browser. L'URL di convalida specificato è valido solo per circa 10 minuti. Durante questo periodo, lo stato di provisioning della sottoscrizione di eventi è `AwaitingManualAction`. Se si non completata la convalida manuale entro 10 minuti, lo stato di provisioning è impostato su `Failed`. È possibile creare la sottoscrizione all'evento nuovamente prima di tentare la convalida manuale.

Questo meccanismo di convalida manuale è disponibile in anteprima. Per usarla, è necessario installare l'[estensione Griglia di eventi](/cli/azure/azure-cli-extensions-list) per l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). È possibile installarla con `az extension add --name eventgrid`. Se si usa l'API REST, assicurarsi di usare `api-version=2018-05-01-preview`.

### <a name="validation-details"></a>Dettagli di convalida

* In fase di creazione/aggiornamento della sottoscrizione di eventi, Griglia di eventi inserisce un evento di convalida della sottoscrizione nell'endpoint di destinazione. 
* L'evento contiene un valore di intestazione "aeg-event-type: SubscriptionValidation".
* Il corpo dell'evento ha lo stesso schema degli altri eventi di Griglia di eventi.
* La proprietà eventType dell'evento è `Microsoft.EventGrid.SubscriptionValidationEvent`.
* La proprietà Data dell'evento include una proprietà `validationCode` con una stringa generata in modo casuale. ad esempio "validationCode: acb13…".
* Se si usa la versione 2018-05-01-preview dell'API, i dati dell'evento includono anche una proprietà `validationUrl` con un URL per la convalida manuale della sottoscrizione.
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
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
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

In alternativa, è possibile convalidare manualmente la sottoscrizione inviando una richiesta GET all'URL di convalida. La sottoscrizione dell'evento rimane nello stato in sospeso fino a quando non viene convalidata.

Un esempio C# che illustra come gestire l'handshake di convalida della sottoscrizione è disponibile in https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs.

### <a name="checklist"></a>Elenco di controllo

Durante la creazione di una sottoscrizione di eventi, se viene visualizzato un messaggio di errore analogo a "Il tentativo di convalida dell'endpoint specificato https://your-endpoint-here non è riuscito. Per altri dettagli, vedere https://aka.ms/esvalidation", si è verificato un errore nell'handshake di convalida. Per risolvere questo errore, verificare gli aspetti seguenti:

* È possibile controllare il codice dell'applicazione nell'endpoint di destinazione? Se, ad esempio, si scrive una funzione di Azure basata su un trigger HTTP, si è autorizzati ad accedere al codice dell'applicazione per modificarlo?
* Se si è autorizzati ad accedere al codice dell'applicazione, implementare il meccanismo di handshake basato su ValidationCode, come illustrato nell'esempio precedente.

* Se non si è autorizzati ad accedere al codice dell'applicazione, ad esempio se si usa un servizio di terze parti che supporta webhook, è possibile usare il meccanismo di handshake manuale. Assicurarsi di usare la versione dell'API 2018-05-01-preview o versione successiva (installare l'estensione dell'interfaccia della riga di comando di Azure della griglia di eventi) per ricevere validationUrl nell'evento di convalida. Per completare l'handshake di convalida manuale, ottenere il valore della proprietà `validationUrl` e visitare l'URL nel Web browser. Se la convalida ha esito positivo, verrà visualizzato un messaggio nel Web browser che indica che la convalida ha avuto esito positivo. Si noterà che provisioningState della sottoscrizione dell'evento è "Succeeded". 

### <a name="event-delivery-security"></a>Sicurezza del recapito degli eventi

È possibile proteggere l'endpoint webhook aggiungendo i parametri di query all'URL del webhook durante la creazione di una sottoscrizione di eventi. Impostare uno di questi parametri di query in modo che sia un segreto, ad esempio un [token di accesso](https://en.wikipedia.org/wiki/Access_token) che il webhook può usare per riconoscere l'evento proviene da Griglia di eventi con autorizzazioni valide. Griglia di eventi includerà questi parametri di query in ogni recapito di eventi al webhook.

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

## <a name="management-access-control"></a>Controllo di accesso per la gestione

Griglia di eventi di Azure consente di controllare il livello di accesso assegnato ai diversi utenti per eseguire svariate operazioni di gestione, ad esempio elencare sottoscrizioni di eventi, crearne di nuove e generare chiavi. La Griglia di eventi usa a questo scopo il controllo degli accessi in base al ruolo di Azure.

### <a name="operation-types"></a>Tipi di operazioni

Griglia di eventi di Azure supporta le azioni seguenti:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Le ultime tre operazioni restituiscono informazioni potenzialmente riservate che vengono filtrate dalle normali operazioni di lettura. È consigliabile limitare l'accesso a queste operazioni. I ruoli personalizzati possono essere creati usando [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md), [l'interfaccia della riga di comando di Azure (CLI)](../role-based-access-control/role-assignments-cli.md) e la [API REST](../role-based-access-control/role-assignments-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Applicazione del controllo degli accessi in base al ruolo

Usare i passaggi seguenti per applicare il controllo degli accessi in base al ruolo per utenti diversi:

#### <a name="create-a-custom-role-definition-file-json"></a>Creare un file di definizione del ruolo personalizzato (JSON)

Le seguenti sono definizioni di esempio del ruolo di Griglia di eventi che consentono agli utenti di eseguire diverse azioni.

**EventGridReadOnlyRole.json**: consente solo operazioni di sola lettura.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: consente le azioni di pubblicazione con restrizioni, ma non consente le azioni di eliminazione.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: consente tutte le azioni di Griglia di eventi.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>Creare e assegnare un ruolo personalizzato con l'interfaccia della riga di comando di Azure

Per creare un ruolo personalizzato, usare:

```azurecli
az role definition create --role-definition @<file path>
```

Per assegnare il ruolo a un utente, usare:

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
