---
title: Usare la piattaforma di identità Microsoft per l'accesso degli utenti nei dispositivi senza browser | Azure
description: Compilare flussi di autenticazione incorporati e senza browser usando la concessione del codice del dispositivo.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdd99899494e9f7b3c0caa4e83f18803b969db1e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192710"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Microsoft Identity Platform e il flusso del codice del dispositivo OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

La piattaforma di identità Microsoft supporta la [concessione del codice del dispositivo](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), che consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una Smart TV, un dispositivo Internet o una stampante.  Per abilitare questo flusso, il dispositivo richiede all'utente di visitare una pagina Web nel browser in un altro dispositivo per l'accesso.  Una volta che l'utente avrà eseguito l'accesso, il dispositivo potrà ottenere i token di accesso e di aggiornamento in base alle esigenze.  

> [!IMPORTANT]
> A questo punto, l'endpoint della piattaforma Microsoft Identity supporta solo il flusso di dispositivi per Azure AD tenant, ma non per gli account personali.  Ciò significa che è necessario usare un endpoint configurato come tenant o `organizations` endpoint.  Questo supporto verrà abilitato a breve. 
>
> Gli account personali che sono invitati in un tenant di Azure AD potranno usare la concessione del flusso del dispositivo, ma solo nel contesto del tenant.
>
> Come ulteriore nota, il campo `verification_uri_complete` della risposta non è incluso né supportato in questo momento.  Questa operazione `verification_uri_complete` viene citata perché, se si legge lo standard, viene elencato come parte facoltativa dello standard del flusso del codice del dispositivo.

> [!NOTE]
> L'endpoint della piattaforma Microsoft Identity non supporta tutti gli scenari e le funzionalità di Azure Active Directory. Per determinare se è necessario usare l'endpoint della piattaforma Microsoft Identity, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagramma di protocollo

L'intero flusso del codice del dispositivo ha un aspetto simile a quello illustrato nel diagramma seguente. Tutti i passaggi vengono descritti in seguito nell'articolo.

![Flusso di codice del dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Richiesta di autorizzazione del dispositivo

Il client deve prima verificare con il server di autenticazione per un dispositivo e un codice utente usato per avviare l'autenticazione. Il client raccoglie la richiesta dall'endpoint `/devicecode`. In questa richiesta, il client deve includere anche le autorizzazioni che deve acquisire dall'utente. Dal momento in cui la richiesta viene inviata, l'utente ha solo 15 minuti per eseguire l'accesso (il valore consueto per `expires_in`), quindi effettuare questa richiesta solo quando l'utente ha indicato di essere pronto a eseguire l'accesso.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![Provare a eseguire la richiesta in un post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria |Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere fornito nel formato di nome descrittivo o GUID.  |
| `client_id` | Obbligatoria | **ID dell'applicazione (client)** che la [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) l'esperienza assegnata all'app. |
| `scope` | Consigliato | Elenco separato da spazi di [ambiti](v2-permissions-and-consent.md) a cui si vuole che l'utente dia il consenso.  |

### <a name="device-authorization-response"></a>Risposta di autorizzazione dispositivo

Una risposta con esito positivo sarà un oggetto JSON contenente le informazioni richieste per consentire all'utente di accedere.  

| Parametro | Formato | Descrizione |
| ---              | --- | --- |
|`device_code`     | String | Una stringa lunga usata per verificare la sessione tra il client e il server di autorizzazione. Il client usa questo parametro per richiedere il token di accesso dal server di autorizzazione. |
|`user_code`       | String | Una stringa breve mostrata all'utente usato per identificare la sessione in un dispositivo secondario.|
|`verification_uri`| URI | L'URI a cui l'utente deve passare con il `user_code` per eseguire l'accesso. |
|`expires_in`      | int | Il numero di secondi prima della scadenza del `device_code` e del `user_code`. |
|`interval`        | int | Il numero di secondi di attesa del client tra le richieste di polling. |
| `message`        | String | Una stringa leggibile dall'utente con le istruzioni per l'utente. Può essere localizzata includendo un **parametro di query** nella richiesta del form `?mkt=xx-XX`, compilando l'apposito codice della lingua di destinazione. |

## <a name="authenticating-the-user"></a>Autenticazione dell'utente

Dopo aver ricevuto `user_code` e `verification_uri`, il client li visualizza all'utente, indicando loro di eseguire l'accesso con il telefono cellulare o il browser del PC.  Il client può anche usare un codice a matrice o un meccanismo simile per visualizzare l'`verfication_uri_complete`, per cui l'utente dovrà immettere il `user_code`.

Mentre l'utente esegue l'autenticazione all'`verification_uri`, il client deve eseguire il polling dell'endpoint `/token`per il token richiesto usando il `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parametro | Obbligatoria | Descrizione|
| -------- | -------- | ---------- |
| `grant_type` | Obbligatoria | Deve essere `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obbligatoria | Deve corrispondere al `client_id` usato nella richiesta iniziale. |
| `device_code`| Obbligatoria | Il `device_code` restituito nella richiesta di autorizzazione del dispositivo.  |

### <a name="expected-errors"></a>Errori previsti

Il flusso del codice del dispositivo è un protocollo di polling, quindi il client deve aspettarsi di ricevere errori prima che l'utente abbia terminato l'autenticazione.  

| Errore | Descrizione | Azione client |
| ------ | ----------- | -------------|
| `authorization_pending` | L'utente non ha completato l'autenticazione, ma non ha annullato il flusso. | Ripetere la richiesta dopo almeno `interval` secondi. |
| `authorization_declined` | L'utente finale ha rifiutato la richiesta di autorizzazione.| Arrestare il polling e ripristinare uno stato non autenticato.  |
| `bad_verification_code`| Il `device_code` inviato`/token` all'endpoint non è stato riconosciuto. | Verificare che il client stia inviando il `device_code` corretto nella richiesta. |
| `expired_token` | Sono trascorsi almeno `expires_in` secondi e l'autenticazione non è più possibile con questo `device_code`. | Arrestare il polling e ripristinare uno stato non autenticato. |

### <a name="successful-authentication-response"></a>Risposta di autenticazione con esito positivo

Una risposta token con esito positivo ha un aspetto simile al seguente:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parametro | Formato | Descrizione |
| --------- | ------ | ----------- |
| `token_type` | String| Sempre "Bearer". |
| `scope` | Stringhe separate da uno spazio | Se è stato restituito un token di accesso, verranno elencati gli ambiti per cui è valido. |
| `expires_in`| int | Numero di secondi per cui il token di accesso incluso verrà considerato valido. |
| `access_token`| Stringa opaca | Emessa per gli [ambiti](v2-permissions-and-consent.md) che sono stati richiesti.  |
| `id_token`   | JWT | Emessa nel parametro `scope` originale incluso nell'ambito `openid`.  |
| `refresh_token` | Stringa opaca | Emessa nel parametro `scope` originale incluso `offline_access`.  |

È possibile usare il token di aggiornamento per acquisire nuovi token di accesso e i token di aggiornamento usando lo stesso flusso documentato nella [documentazione relativa al flusso di codice OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
