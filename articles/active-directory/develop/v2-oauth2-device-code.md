---
title: Flusso del codice del dispositivo OAuth 2.0 Azure
titleSuffix: Microsoft identity platform
description: Accedere agli utenti senza un browser. Creare flussi di autenticazione incorporati e senza browser usando la concessione di autorizzazione del dispositivo.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 9186f633b773a243a84692c30ddc2c2261fb69ba
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309416"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Piattaforma di identità Microsoft e flusso di concessione dell'autorizzazione del dispositivo OAuth 2.0

La piattaforma di identità Microsoft supporta la concessione di autorizzazione del [dispositivo,](https://tools.ietf.org/html/rfc8628)che consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una smart TV, un dispositivo IoT o una stampante.  Per abilitare questo flusso, il dispositivo richiede all'utente di visitare una pagina Web nel browser in un altro dispositivo per l'accesso.  Una volta che l'utente avrà eseguito l'accesso, il dispositivo potrà ottenere i token di accesso e di aggiornamento in base alle esigenze.  

In questo articolo viene descritto come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile utilizzare le librerie di autenticazione Microsoft (MSAL) supportate per [acquisire token e chiamare API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dai anche un'occhiata alle [app di esempio che utilizzano MSAL](sample-v2-code.md).

> [!NOTE]
> L'endpoint della piattaforma di identità Microsoft non supporta tutti gli scenari e le funzionalità di Azure Active Directory.The Microsoft identity platform endpoint doesn't support all Azure Active Directory scenarios and features. Per determinare se è necessario utilizzare l'endpoint della piattaforma di identità Microsoft, leggere le informazioni sulle [limitazioni della piattaforma](active-directory-v2-limitations.md)di identità Microsoft .

## <a name="protocol-diagram"></a>Diagramma di protocollo

L'intero flusso del codice del dispositivo ha un aspetto simile a quello illustrato nel diagramma seguente. Tutti i passaggi vengono descritti in seguito nell'articolo.

![Flusso del codice del dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Richiesta di autorizzazione del dispositivo

Il client deve prima verificare con il server di autenticazione un dispositivo e il codice utente utilizzato per avviare l'autenticazione. Il client raccoglie la richiesta dall'endpoint `/devicecode`. In questa richiesta, il client deve includere anche le autorizzazioni che deve acquisire dall'utente. Dal momento in cui la richiesta viene inviata, l'utente ha solo 15 minuti per eseguire l'accesso (il valore consueto per `expires_in`), quindi effettuare questa richiesta solo quando l'utente ha indicato di essere pronto a eseguire l'accesso.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![Prova a eseguire questa richiesta in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | Può essere /common, /consumers o /organizations.  Può anche essere il tenant di directory da cui si desidera richiedere l'autorizzazione in formato GUID o nome descrittivo.  |
| `client_id` | Obbligatoria | ID **applicazione (client)** assegnato all'app dal [portale di Azure.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `scope` | Consigliato | Elenco separato da spazi di [ambiti](v2-permissions-and-consent.md) a cui si vuole che l'utente dia il consenso.  |

### <a name="device-authorization-response"></a>Risposta di autorizzazione dispositivo

Una risposta con esito positivo sarà un oggetto JSON contenente le informazioni richieste per consentire all'utente di accedere.  

| Parametro | Format | Descrizione |
| ---              | --- | --- |
|`device_code`     | string | Una stringa lunga usata per verificare la sessione tra il client e il server di autorizzazione. Il client utilizza questo parametro per richiedere il token di accesso dal server di autorizzazione. |
|`user_code`       | string | Breve stringa visualizzata all'utente utilizzata per identificare la sessione in un dispositivo secondario.|
|`verification_uri`| URI | L'URI a cui l'utente deve passare con il `user_code` per eseguire l'accesso. |
|`expires_in`      | INT | Il numero di secondi prima della scadenza del `device_code` e del `user_code`. |
|`interval`        | INT | Il numero di secondi di attesa del client tra le richieste di polling. |
| `message`        | string | Una stringa leggibile dall'utente con le istruzioni per l'utente. Può essere localizzata includendo un **parametro di query** nella richiesta del form `?mkt=xx-XX`, compilando l'apposito codice della lingua di destinazione. |

> [!NOTE]
> Il `verification_uri_complete` campo di risposta non è incluso o supportato in questo momento.  Ne parliamo perché se leggi [lo](https://tools.ietf.org/html/rfc8628) `verification_uri_complete` standard che vedi che è elencato come una parte facoltativa dello standard di flusso del codice del dispositivo.

## <a name="authenticating-the-user"></a>Autenticazione dell'utente

Dopo aver `user_code` `verification_uri`ricevuto il e , il client li visualizza all'utente, indicandogli di accedere utilizzando il proprio telefono cellulare o browser PER PC.

Se l'utente esegue l'autenticazione con un account personale (in /common o /consumers), verrà chiesto di accedere nuovamente per trasferire lo stato di autenticazione al dispositivo.  Essi saranno anche invitati a fornire il consenso, per garantire che siano a conoscenza delle autorizzazioni concesse.  Ciò non si applica agli account aziendali o dell'istituto di istruzione utilizzati per l'autenticazione. 

Mentre l'utente esegue l'autenticazione all'`verification_uri`, il client deve eseguire il polling dell'endpoint `/token`per il token richiesto usando il `device_code`.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametro | Obbligatoria | Descrizione|
| -------- | -------- | ---------- |
| `tenant`  | Obbligatoria | Lo stesso alias tenant o tenant utilizzato nella richiesta iniziale. | 
| `grant_type` | Obbligatoria | Deve essere `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obbligatoria | Deve corrispondere al `client_id` usato nella richiesta iniziale. |
| `device_code`| Obbligatoria | Il `device_code` restituito nella richiesta di autorizzazione del dispositivo.  |

### <a name="expected-errors"></a>Errori previsti

Il flusso del codice del dispositivo è un protocollo di polling, pertanto il client deve aspettarsi di ricevere errori prima che l'utente abbia terminato l'autenticazione.  

| Errore | Descrizione | Azione client |
| ------ | ----------- | -------------|
| `authorization_pending` | L'utente non ha terminato l'autenticazione, ma non ha annullato il flusso. | Ripetere la richiesta dopo almeno `interval` secondi. |
| `authorization_declined` | L'utente finale ha rifiutato la richiesta di autorizzazione.| Arrestare il polling e ripristinare uno stato non autenticato.  |
| `bad_verification_code`| L'invio `device_code` `/token` all'endpoint non è stato riconosciuto. | Verificare che il client stia inviando il `device_code` corretto nella richiesta. |
| `expired_token` | Sono trascorsi almeno `expires_in` secondi e l'autenticazione non è più possibile con questo `device_code`. | Interrompere il polling e tornare a uno stato non autenticato. |   

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

| Parametro | Format | Descrizione |
| --------- | ------ | ----------- |
| `token_type` | string| Sempre "Bearer". |
| `scope` | Stringhe separate da uno spazio | Se è stato restituito un token di accesso, verranno elencati gli ambiti per cui è valido. |
| `expires_in`| INT | Numero di secondi per cui il token di accesso incluso verrà considerato valido. |
| `access_token`| Stringa opaca | Emessa per gli [ambiti](v2-permissions-and-consent.md) che sono stati richiesti.  |
| `id_token`   | Token JSON Web | Emessa nel parametro `scope` originale incluso nell'ambito `openid`.  |
| `refresh_token` | Stringa opaca | Emessa nel parametro `scope` originale incluso `offline_access`.  |

È possibile utilizzare il token di aggiornamento per acquisire nuovi token di accesso e token di aggiornamento utilizzando lo stesso flusso documentato nella documentazione del [flusso di codice OAuth.](v2-oauth2-auth-code-flow.md#refresh-the-access-token)  
