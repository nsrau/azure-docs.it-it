---
title: Usare Azure AD v2.0 per consentire l'accesso agli utenti con ROPC | Microsoft Docs
description: Come supportare i flussi di autenticazione senza browser tramite la concessione di credenziali password del proprietario della risorsa.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4eb6850e4b6e267e0b4ef83f7639e90308cee989
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841439"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-resource-owner-password-credential"></a>Azure Active Directory v2.0 e le credenziali password del proprietario della risorsa OAuth 2.0

Azure Active Directory (Azure AD) supporta la [concessione di credenziali password del proprietario della risorsa (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), attraverso la quale un'applicazione consente all'utente di accedere gestendo direttamente la sua password. Il flusso ROPC richiede un elevato livello di attendibilità ed esposizione dell'utente. Gli sviluppatori devono usare questo flusso solo quando non è possibile usare altri flussi più sicuri.

> [!Important]
> * L'endpoint v2.0 di Azure AD supporta solo ROPC per tenant di Azure AD, non gli account personali. Questo significa che è necessario usare un endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`) o l'endpoint `organizations`.
> * Gli account personali che sono invitati in un tenant di Azure AD non possono usare ROPC.
> * Gli account che non dispongono di password non possono accedere tramite ROPC. Per questo scenario, è consigliabile utilizzare un flusso diverso per l'app.
> * Se gli utenti devono usare l'autenticazione a più fattori (MFA) per accedere all'applicazione, saranno invece bloccati.

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra il flusso ROPC.

![Flusso ROPC](media/v2-oauth2-ropc/v2-oauth-ropc.png)

## <a name="authorization-request"></a>Authorization request (Richiesta di autorizzazione)

Il flusso ROPC è una richiesta singola: invia l'identificazione del client e le credenziali dell'utente all'IDP, quindi riceve i token in cambio. Il client deve richiedere l'indirizzo di posta elettronica (UPN) e la password dell'utente prima di procedere. Subito dopo una richiesta con esito positivo, il client dovrebbe rilasciare le credenziali dell'utente dalla memoria in modo sicuro. Non deve mai salvarle.

```
// Line breaks and spaces are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token?

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&client_secret=wkubdywbc2894u
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parametro | Condizione | DESCRIZIONE |
| --- | --- | --- |
| `tenant` | Obbligatoria | Il tenant della directory in cui si desidera registrare l'utente. Può essere fornito nel formato di nome descrittivo o GUID. Questo parametro non può essere impostato su `common` oppure `consumers`, ma può essere impostato su `organizations`. |
| `grant_type` | Obbligatoria | Il valore deve essere impostato su `password`. |
| `username` | Obbligatoria | L'indirizzo di posta elettronica dell'utente. |
| `password` | Obbligatoria | La password dell'utente. |
| `scope` | Consigliato | Un elenco delimitato da spazi di [ambiti](v2-permissions-and-consent.md), o privilegi, richiesti dall'app. Questi ambiti devono essere concessi in anticipo da un amministratore o dall'utente in un flusso interattivo. |

### <a name="successful-authentication-response"></a>Risposta di autenticazione con esito positivo

Il seguente è un esempio di risposta del token con esito positivo:

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

| Parametro | Format | DESCRIZIONE |
| --------- | ------ | ----------- |
| `token_type` | string | Sempre impostato su `Bearer`. |
| `scope` | Stringhe separate da uno spazio | Se è stato restituito un token di accesso, questo parametro elenca gli ambiti per cui è valido. |
| `expires_in`| int | Numero di secondi per cui il token di accesso incluso verrà considerato valido. |
| `access_token`| Stringa opaca | Emessa per gli [ambiti](v2-permissions-and-consent.md) che sono stati richiesti. |
| `id_token` | Token JSON Web | Emessa nel parametro `scope` originale incluso nell'ambito `openid`. |
| `refresh_token` | Stringa opaca | Emessa nel parametro `scope` originale incluso `offline_access`. |

È possibile usare il token di aggiornamento per acquisire nuovi token di accesso e token di aggiornamento usando lo stesso flusso descritto nella [documentazione del flusso del codice OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Risposta di errore

Se l'utente non ha fornito il nome utente o la password corretti, o il client non ha ricevuto il consenso richiesto, l'autenticazione avrà esito negativo.

| Tipi di errore | DESCRIZIONE | Azione client |
|------ | ----------- | -------------|
| `invalid_grant` | L'autenticazione non è riuscita | Le credenziali non sono corrette o il client non ha il consenso per gli ambiti richiesti. Se gli ambiti non sono concessi, verrà restituito un errore secondario `consent_required`. In questo caso, il client deve reindirizzare l'utente a un prompt interattivo con una webview o un browser. |
| `invalid_request` | La richiesta è stata costruita in modo non corretto | Il tipo di concessione non è supportato nei contesti di autenticazione `/common` o `/consumers`.  Usare invece `/organizations`. |
| `invalid_client` | L'app è configurata in modo non corretto | Questa situazione può verificarsi se la proprietà `allowPublicClient` non è impostata su True nel [manifesto dell'applicazione](reference-app-manifest.md). La proprietà `allowPublicClient` è necessaria perché la concessione ROPC non dispone di un URI di reindirizzamento. Azure AD non può determinare se l'app è un'applicazione client pubblica o riservata se la proprietà non è impostata. Notare che ROPC è supportato solo per app client pubbliche. |

## <a name="learn-more"></a>Altre informazioni

* È possibile provare ROPC grazie all'[applicazione console di esempio](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Per determinare se è necessario usare l'endpoint 2.0, vedere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).
