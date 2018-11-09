---
title: Usare Azure AD v2.0 per consentire l'accesso agli utenti nei dispositivi senza browser | Microsoft Docs
description: Compilare flussi di autenticazione incorporati e senza browser usando la concessione del codice del dispositivo.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 29cbb96cc6dec4bac601e8795599f77c955c418a
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230830"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-device-code-flow"></a>Azure Active Directory v2.0 e il flusso del codice del dispositivo OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Azure AD supporta la [concessione del codice del dispositivo](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), che consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una smart TV, un dispositivo IoT o una stampante.  Per abilitare questo flusso, il dispositivo richiede all'utente di visitare una pagina Web nel browser in un altro dispositivo per l'accesso.  Una volta che l'utente avrà eseguito l'accesso, il dispositivo potrà ottenere i token di accesso e di aggiornamento in base alle esigenze.  

> [!Important] 
> Attualmente, l'endpoint v2.0 supporta il flusso del dispositivo unicamente per i tenant di Azure AD, ma non per gli account personali.  Ciò significa che è necessario usare un endpoint del tenant o l'endpoint dell'organizzazione.  
>
> Gli account personali che sono invitati in un tenant di Azure AD potranno usare la concessione del flusso del dispositivo, ma solo nel contesto del tenant.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint 2.0, vedere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).
>

## <a name="protocol-diagram"></a>Diagramma di protocollo

L'intero flusso del codice del dispositivo ha un aspetto simile a quello illustrato nel diagramma seguente. Tutti i passaggi vengono descritti in seguito nell'articolo.

![Flusso del codice del dispositivo](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>Richiesta di autorizzazione del dispositivo

Il client deve prima di tutto verificare sul server di autenticazione un codice dispositivo e utente, usati per avviare l'autenticazione.  Il client raccoglie la richiesta dall'endpoint `/devicecode`. In questa richiesta, il client deve includere anche le autorizzazioni che deve acquisire dall'utente.  Dal momento in cui la richiesta viene inviata, l'utente ha solo 15 minuti per eseguire l'accesso (il valore consueto per `expires_in`), quindi effettuare questa richiesta solo quando l'utente ha indicato di essere pronto a eseguire l'accesso.

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametro | Condizione | DESCRIZIONE |
| --- | --- | --- |
| tenant |Obbligatoria |Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere fornito nel formato di nome descrittivo o GUID.  |
| client_id |Obbligatoria |ID applicazione che il [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ha assegnato all'app. |
| scope | Consigliato | Elenco separato da spazi di [ambiti](v2-permissions-and-consent.md) a cui si vuole che l'utente dia il consenso.  |

### <a name="device-authorization-response"></a>Risposta di autorizzazione dispositivo

Una risposta con esito positivo sarà un oggetto JSON contenente le informazioni richieste per consentire all'utente di accedere.  

| Parametro | Format | DESCRIZIONE |
| ---              | --- | --- |
|`device_code`     |string| Una stringa lunga usata per verificare la sessione tra il client e il server di autorizzazione.  Viene usata dal client per richiedere il token di accesso dal server di autorizzazione. |
|`user_code`       |string| Una stringa breve visualizzata dall'utente, usata per identificare la sessione in un dispositivo secondario.|
|`verification_uri`|URI| L'URI a cui l'utente deve passare con il `user_code` per eseguire l'accesso. |
|`verification_uri_complete`|URI| Un URI che combina il `user_code` e l'`verification_uri` usati per la trasmissione non testuale all'utente (ad esempio, via Bluetooth a un dispositivo o attraverso un codice a matrice).  |
|`expires_in`      |int| Il numero di secondi prima della scadenza del `device_code` e del `user_code`. |
|`interval`        |int| Il numero di secondi di attesa del client tra le richieste di polling. |
| `message`        |string| Una stringa leggibile dall'utente con le istruzioni per l'utente.  Può essere localizzata includendo un **parametro di query** nella richiesta del form `?mkt=xx-XX`, compilando l'apposito codice della lingua di destinazione. |

## <a name="authenticating-the-user"></a>Autenticazione dell'utente

Dopo avere ricevuto il `user_code` e l'`verification_uri`, il client li mostra all'utente, istruendolo ad accedere usando il browser su telefono cellulare o PC.  Il client può anche usare un codice a matrice o un meccanismo simile per visualizzare l'`verfication_uri_complete`, per cui l'utente dovrà immettere il `user_code`.

Mentre l'utente esegue l'autenticazione all'`verification_uri`, il client deve eseguire il polling dell'endpoint `/token`per il token richiesto usando il `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

|Parametro | Obbligatoria | DESCRIZIONE|
| -------- | -------- | ---------- |
|`grant_type` | Obbligatoria| Deve essere `urn:ietf:params:oauth:grant-type:device_code`|
|`client_id`  | Obbligatoria| Deve corrispondere al `client_id` usato nella richiesta iniziale. |
|`device_code`| Obbligatoria| Il `device_code` restituito nella richiesta di autorizzazione del dispositivo.  |

### <a name="expected-errors"></a>Errori previsti

Dal momento che il flusso del codice del dispositivo è un protocollo di polling, il client deve prevedere di ricevere gli errori prima che l'utente abbia terminato l'autenticazione.  

| Tipi di errore | DESCRIZIONE | Azione client |
|------ | ----------- | -------------|
| `authorization_pending` |  L'utente non ha ancora terminato l'autenticazione, ma non ha annullato il flusso. | Ripetere la richiesta dopo almeno `interval` secondi. |
| `authorization_declined`|  L'utente finale ha rifiutato la richiesta di autorizzazione.| Arrestare il polling e ripristinare uno stato non autenticato.  |
| `bad_verification_code`|Il `device_code` inviato all'endpoint `/token` non è stato riconosciuto. | Verificare che il client stia inviando il `device_code` corretto nella richiesta. |
| `expired_token`|  Sono trascorsi almeno `expires_in` secondi e l'autenticazione non è più possibile con questo `device_code`. | Arrestare il polling e ripristinare uno stato non autenticato. |


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
|`token_type` | Stringa| Sempre "Bearer". |
|`scope` | Stringhe separate da uno spazio | Se è stato restituito un token di accesso, verranno elencati gli ambiti per cui è valido. |
|`expires_in`| int | Numero di secondi per cui il token di accesso incluso verrà considerato valido. |
|`access_token`| Stringa opaca | Emessa per gli [ambiti](v2-permissions-and-consent.md) che sono stati richiesti.  |
|`id_token`   | Token JSON Web | Emessa nel parametro `scope` originale incluso nell'ambito `openid`.  |
|`refresh_token` | Stringa opaca | Emessa nel parametro `scope` originale incluso `offline_access`.  |

Il token di aggiornamento è utilizzabile per acquisire nuovi token di accesso e token di aggiornamento usando lo stesso flusso descritto in dettaglio nella [documentazione del flusso del codice OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  