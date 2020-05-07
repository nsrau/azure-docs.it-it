---
title: Sicurezza e autenticazione di Griglia di eventi di Azure
description: Questo articolo descrive le diverse modalità di autenticazione dell'accesso alle risorse di griglia di eventi (webhook, sottoscrizioni, argomenti personalizzati)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 528c3613549ee49009f99d45e5bd9c2cf1745d78
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779995"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Autenticazione dell'accesso alle risorse di griglia di eventi di Azure
Questo articolo fornisce informazioni sugli scenari seguenti:  

- Autenticare i client che pubblicano gli eventi negli argomenti di griglia di eventi di Azure usando la firma di accesso condiviso o la chiave. 
- Proteggere l'endpoint del webhook usando Azure Active Directory (Azure AD) per autenticare griglia di eventi per **recapitare** gli eventi all'endpoint.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Autenticare i client di pubblicazione tramite SAS o Key
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

La risorsa è il percorso dell'argomento di Griglia di eventi a cui si inviano gli eventi. Ad esempio, un percorso di risorsa valido è `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`:. Per visualizzare tutte le versioni API supportate, vedere [tipi di risorse Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

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

Tutti gli eventi o i dati scritti sul disco dal servizio griglia di eventi vengono crittografati da una chiave gestita da Microsoft, assicurando che siano crittografati. Inoltre, il periodo massimo di tempo per cui gli eventi o i dati conservati sono pari a 24 ore rispetto ai [criteri di ripetizione dei tentativi di griglia di eventi](delivery-and-retry.md). Griglia di eventi eliminerà automaticamente tutti gli eventi o i dati dopo 24 ore o la durata (TTL) dell'evento, a seconda del numero minore.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticare il recapito di eventi agli endpoint del webhook
Le sezioni seguenti descrivono come autenticare il recapito di eventi agli endpoint del webhook. È necessario usare un meccanismo di handshake di convalida indipendentemente dal metodo usato. Per informazioni dettagliate, vedere [recapito di eventi webhook](webhook-event-delivery.md) . 

### <a name="using-azure-active-directory-azure-ad"></a>Utilizzo di Azure Active Directory (Azure AD)
È possibile proteggere l'endpoint del webhook usando Azure Active Directory (Azure AD) per autenticare e autorizzare griglia di eventi per recapitare gli eventi agli endpoint. È necessario creare un'applicazione Azure AD, creare un ruolo e un principio di servizio nell'applicazione autorizzazione griglia di eventi e configurare la sottoscrizione di eventi per l'uso dell'applicazione Azure AD. [Informazioni su come configurare Azure Active Directory con griglia di eventi](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Uso del segreto client come parametro di query
È possibile proteggere l'endpoint webhook aggiungendo i parametri di query all'URL del webhook durante la creazione di una sottoscrizione di eventi. Impostare uno di questi parametri di query in modo che sia un segreto client, ad esempio un [token di accesso](https://en.wikipedia.org/wiki/Access_token) o un segreto condiviso. che il webhook può usare per riconoscere che l'evento proviene da Griglia di eventi con autorizzazioni valide. Griglia di eventi includerà questi parametri di query in ogni recapito di eventi al webhook. Se il segreto client viene aggiornato, è necessario aggiornare anche la sottoscrizione di eventi. Per evitare errori di recapito durante questa rotazione del segreto, fare in modo che il webhook accetti sia i segreti vecchi che quelli nuovi per una durata limitata. 

Poiché i parametri di query possono contenere segreti client, vengono gestiti con maggiore attenzione. Vengono archiviati come crittografati e non accessibili agli operatori di servizio. Non vengono registrate come parte dei log/tracce del servizio. Quando si modifica la sottoscrizione dell'evento, i parametri di query non sono visualizzati o restituiti a meno che non venga usato il parametro [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) nell'[interfaccia della riga di comando](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) di Azure.

Per altre informazioni su come recapitare gli eventi ai webhook, vedere [recapito di eventi webhook](webhook-event-delivery.md)

> [!IMPORTANT]
Griglia di eventi di Azure supporta solo endpoint di Webhook **https** . 

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione a griglia di eventi, vedere [informazioni su griglia di eventi](overview.md)
