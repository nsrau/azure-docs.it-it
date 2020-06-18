---
title: Sicurezza e autenticazione di Griglia di eventi di Azure
description: Questo articolo descrive le diverse modalità di autenticazione dell'accesso alle risorse di Griglia di eventi (webhook, sottoscrizioni, argomenti personalizzati)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: bca450022322db7a7569fa1dc7ce80ec75a9ce69
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774302"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Autenticazione dell'accesso alle risorse di Griglia di eventi di Azure
Questo articolo fornisce informazioni sugli scenari seguenti:  

- Autenticare client che pubblicano eventi negli argomenti di Griglia di eventi di Azure usando la firma di accesso condiviso o la chiave. 
- Proteggere l'endpoint del webhook usato per ricevere eventi da Griglia di eventi usando Azure Active Directory (Azure AD) o un segreto condiviso.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Autenticare i client di pubblicazione con la firma di accesso condiviso o la chiave
Gli argomenti personalizzati usano la firma di accesso condiviso (SAS) o l'autenticazione della chiave. È consigliabile la firma di accesso condiviso, ma l'autenticazione della chiave fornisce una programmazione semplice ed è compatibile con molte entità di pubblicazione di webhook esistenti.

Il valore dell'autenticazione viene incluso nell'intestazione HTTP. Per la firma di accesso condiviso, usare **aeg-sas-token** per il valore dell'intestazione. Per l'autenticazione della chiave, usare **aeg-sas-key** per il valore dell'intestazione.

### <a name="key-authentication"></a>Autenticazione della chiave

L'autenticazione della chiave è la forma più semplice di autenticazione. Usare il formato `aeg-sas-key: <your key>` nell'intestazione del messaggio.

Ad esempio, passare una chiave con:

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Come parametro di query è anche possibile specificare `aeg-sas-key`. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Token di firma di accesso condiviso

I token di firma di accesso condiviso per Griglia di eventi includono la risorsa, un'ora di scadenza e una firma. Il formato del token di firma di accesso condiviso è: `r={resource}&e={expiration}&s={signature}`.

La risorsa è il percorso dell'argomento di Griglia di eventi a cui si inviano gli eventi. Un percorso di risorsa valido, ad esempio, è: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Per visualizzare tutte le versioni dell'API supportate, vedere [Tipi di risorsa di Microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

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

Tutti gli eventi o i dati scritti sul disco dal servizio Griglia di eventi vengono crittografati con una chiave gestita da Microsoft, per garantire che siano crittografati quando sono inattivi. Il periodo massimo di conservazione di eventi o dati è inoltre pari a 24 ore, in conformità ai [criteri di ripetizione di Griglia di eventi](delivery-and-retry.md). Griglia di eventi eliminerà automaticamente tutti gli eventi o i dati una volta trascorse 24 ore o il tempo impostato per la durata (TTL) dell'evento, a seconda dell'evento che si verifica per primo.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticare il recapito di eventi agli endpoint webhook
Le sezioni seguenti descrivono come autenticare il recapito di eventi agli endpoint webhook. È necessario usare un meccanismo di handshake di convalida indipendentemente dal metodo usato. Per informazioni dettagliate, vedere [Recapito eventi webhook](webhook-event-delivery.md). 

### <a name="using-azure-active-directory-azure-ad"></a>Uso di Azure Active Directory (Azure AD)
È possibile proteggere l'endpoint del webhook usato per ricevere eventi da Griglia di eventi usando Azure AD. È necessario creare un'applicazione Azure AD, creare un ruolo e un'entità servizio nell'applicazione che autorizza Griglia di eventi e configurare la sottoscrizione dell'evento per usare l'applicazione Azure AD. Informazioni su come [configurare Azure Active Directory con Griglia di eventi](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Uso del segreto client come parametro di query
È possibile proteggere l'endpoint webhook aggiungendo i parametri di query all'URL di destinazione del webhook indicato come parte della creazione di una sottoscrizione di eventi. Impostare uno dei parametri di query in modo che sia un segreto client, ad esempio un [token di accesso](https://en.wikipedia.org/wiki/Access_token) o un segreto condiviso. Il servizio Griglia di eventi includerà tutti questi parametri di query in ogni richiesta di recapito di eventi al webhook. Il servizio webhook può recuperare e convalidare il segreto. Se il segreto client viene aggiornato, è necessario aggiornare anche la sottoscrizione dell'evento. Per evitare errori di recapito durante questa rotazione del segreto, fare in modo che il webhook accetti sia i segreti vecchi che quelli nuovi per un periodo limitato prima di aggiornare la sottoscrizione con il nuovo segreto. 

Poiché i parametri di query potrebbero contenere segreti client, vengono gestiti con maggiore attenzione. Vengono archiviati come crittografati e non sono accessibili agli operatori del servizio. Non vengono registrati in log/tracce del servizio. Quando si recuperano le proprietà della sottoscrizione di eventi, i parametri delle query di destinazione non vengono restituiti per impostazione predefinita. Ad esempio, il parametro [--include-full-endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) deve essere usato nell'[interfaccia della riga di comando](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) di Azure.

Per altre informazioni su come recapitare gli eventi ai webhook, vedere [Recapito eventi webhook](webhook-event-delivery.md).

> [!IMPORTANT]
Griglia di eventi di Azure supporta solo endpoint webhook **HTTPS**. 

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
