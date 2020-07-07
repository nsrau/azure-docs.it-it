---
title: Flusso del codice del dispositivo OAuth 2,0 | Azure
titleSuffix: Microsoft identity platform
description: Utenti di accesso senza browser. Crea flussi di autenticazione incorporati e senza browser usando la concessione di autorizzazione del dispositivo.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a0677603f02b429c269c0f93ef348b2b1d717a9f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82689771"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft Identity Platform e il flusso di concessione dell'autorizzazione del dispositivo OAuth 2,0

La piattaforma Microsoft Identity supporta la [concessione di autorizzazioni](https://tools.ietf.org/html/rfc8628)per i dispositivi, che consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una Smart TV, un dispositivo Internet o una stampante.  Per abilitare questo flusso, il dispositivo richiede all'utente di visitare una pagina Web nel browser di un altro dispositivo per eseguire l'accesso.  Dopo che l'utente ha eseguito l'accesso, il dispositivo è in grado di ottenere i token di accesso e i token di aggiornamento in base alle esigenze.

Questo articolo descrive come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile usare le librerie di autenticazione Microsoft (MSAL) supportate anziché [acquisire i token e chiamare le API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Vedere anche le [app di esempio che usano MSAL](sample-v2-code.md).

## <a name="protocol-diagram"></a>Diagramma del protocollo

L'intero flusso del codice del dispositivo ha un aspetto simile al diagramma seguente. Ciascuno di questi passaggi viene descritto più avanti in questo articolo.

![Flusso di codice del dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Richiesta di autorizzazione per il dispositivo

Il client deve prima cercare con il server di autenticazione un dispositivo e un codice utente usato per avviare l'autenticazione. Il client raccoglie la richiesta dall'endpoint `/devicecode`. In questa richiesta il client deve anche includere le autorizzazioni che deve acquisire dall'utente. Dal momento in cui la richiesta viene inviata, l'utente ha solo 15 minuti per eseguire l'accesso (il valore consueto per `expires_in`), quindi effettuare questa richiesta solo quando l'utente ha indicato di essere pronto a eseguire l'accesso.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![Provare a eseguire la richiesta in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametro | Condizione | Description |
| --- | --- | --- |
| `tenant` | Necessario | Può essere/Common,/consumers o/Organizations.  Può anche essere il tenant di directory per cui si desidera richiedere l'autorizzazione nel formato GUID o nome descrittivo.  |
| `client_id` | Obbligatoria | L'**ID dell'applicazione (client)** assegnato all'app dall'esperienza[Portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908). |
| `scope` | Implementazione consigliata | Elenco separato da spazi di [ambiti](v2-permissions-and-consent.md) a cui si vuole che l'utente dia il consenso.  |

### <a name="device-authorization-response"></a>Risposta di autorizzazione per il dispositivo

Una risposta di esito positivo sarà un oggetto JSON contenente le informazioni necessarie per consentire all'utente di eseguire l'accesso.

| Parametro | Format | Descrizione |
| ---              | --- | --- |
|`device_code`     | string | Stringa lunga usata per verificare la sessione tra il client e il server di autorizzazione. Il client usa questo parametro per richiedere al server di autorizzazione il token di accesso. |
|`user_code`       | string | Stringa breve mostrata all'utente e usata per identificare la sessione in un dispositivo secondario.|
|`verification_uri`| URI | L'URI a cui l'utente deve passare con il `user_code` per eseguire l'accesso. |
|`expires_in`      | INT | Il numero di secondi prima della scadenza del `device_code` e del `user_code`. |
|`interval`        | INT | Numero di secondi di attesa del client tra le richieste di polling. |
| `message`        | string | Stringa leggibile con le istruzioni per l'utente. Può essere localizzata includendo un **parametro di query** nella richiesta del form `?mkt=xx-XX`, compilando l'apposito codice della lingua di destinazione. |

> [!NOTE]
> Il `verification_uri_complete` campo della risposta non è incluso né supportato in questo momento.  Questa operazione viene citata perché, se [standard](https://tools.ietf.org/html/rfc8628) si legge lo standard `verification_uri_complete` , viene elencato come parte facoltativa dello standard del flusso del codice del dispositivo.

## <a name="authenticating-the-user"></a>Autenticazione dell'utente

Dopo aver ricevuto `user_code` e `verification_uri` , il client li visualizza all'utente, indicando loro di eseguire l'accesso con il telefono cellulare o il browser del PC.

Se l'utente esegue l'autenticazione con un account personale (in/Common o/consumers), verrà richiesto di eseguire di nuovo l'accesso per trasferire lo stato di autenticazione al dispositivo.  Verrà inoltre richiesto di fornire il consenso per assicurarsi che siano consapevoli delle autorizzazioni concesse.  Questa operazione non si applica agli account aziendali o dell'Istituto di istruzione usati per l'autenticazione.

Mentre l'utente esegue l'autenticazione all'`verification_uri`, il client deve eseguire il polling dell'endpoint `/token`per il token richiesto usando il `device_code`.

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametro | Obbligatorio | Description|
| -------- | -------- | ---------- |
| `tenant`  | Necessario | Stesso alias tenant o tenant usato nella richiesta iniziale. |
| `grant_type` | Necessario | Deve essere `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Necessario | Deve corrispondere al `client_id` usato nella richiesta iniziale. |
| `device_code`| Necessario | Il `device_code` restituito nella richiesta di autorizzazione del dispositivo.  |

### <a name="expected-errors"></a>Errori previsti

Il flusso del codice del dispositivo è un protocollo di polling, quindi il client deve aspettarsi di ricevere errori prima che l'utente abbia terminato l'autenticazione.

| Errore | Descrizione | Azione client |
| ------ | ----------- | -------------|
| `authorization_pending` | L'utente non ha completato l'autenticazione, ma non ha annullato il flusso. | Ripetere la richiesta dopo almeno `interval` secondi. |
| `authorization_declined` | L'utente finale ha rifiutato la richiesta di autorizzazione.| Arrestare il polling e ripristinare uno stato non autenticato.  |
| `bad_verification_code`| Il `device_code` inviato all' `/token` endpoint non è stato riconosciuto. | Verificare che il client stia inviando il `device_code` corretto nella richiesta. |
| `expired_token` | Sono trascorsi almeno `expires_in` secondi e l'autenticazione non è più possibile con questo `device_code`. | Arrestare il polling e ripristinare uno stato non autenticato. |

### <a name="successful-authentication-response"></a>Risposta di autenticazione di esito positivo

Una risposta di token di esito positivo sarà simile alla seguente:

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
| `token_type` | string| Sempre "Bearer. |
| `scope` | Stringhe separate da uno spazio | Se è stato restituito un token di accesso, questo parametro elenca gli ambiti per i quali è valido il token di accesso. |
| `expires_in`| INT | Numero di secondi durante i quali è valido il token di accesso incluso. |
| `access_token`| Stringa opaca | Emessa per gli [ambiti](v2-permissions-and-consent.md) che sono stati richiesti.  |
| `id_token`   | Token JSON Web | Emessa nel parametro `scope` originale incluso nell'ambito `openid`.  |
| `refresh_token` | Stringa opaca | Emessa nel parametro `scope` originale incluso `offline_access`.  |

È possibile usare il token di aggiornamento per acquisire nuovi token di accesso e i token di aggiornamento usando lo stesso flusso documentato nella [documentazione relativa al flusso di codice OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).
