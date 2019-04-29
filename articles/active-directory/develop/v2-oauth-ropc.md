---
title: Piattaforma delle identità Microsoft Usa per consentire agli utenti con concessione delle credenziali (ROPC) password del proprietario della risorsa | Azure
description: Come supportare i flussi di autenticazione senza browser tramite la concessione di credenziali password del proprietario della risorsa.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cfa28cae87c8a9a97e1c64b96f75ae4c6eab08d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112290"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Piattaforma delle identità Microsoft e le credenziali di password proprietario risorsa OAuth 2.0

Sono supportati sulla piattaforma Microsoft identity il [concedere le credenziali password proprietario risorsa (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), che consente a un'applicazione di accedere all'utente gestendo direttamente le loro password. Il flusso ROPC richiede un elevato livello di esposizione di attendibilità e l'utente ed è consigliabile usare questo flusso solo quando non è possibile usare altri, più sicuro, i flussi.

> [!IMPORTANT]
>
> * L'endpoint di piattaforma di identità Microsoft supporta solo ROPC per tenant di Azure AD, gli account non personali. Questo significa che è necessario usare un endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`) o l'endpoint `organizations`.
> * Gli account personali che sono invitati in un tenant di Azure AD non possono usare ROPC.
> * Gli account che non dispongono di password non possono accedere tramite ROPC. Per questo scenario, è consigliabile utilizzare un flusso diverso per l'app.
> * Se gli utenti devono usare l'autenticazione a più fattori (MFA) per accedere all'applicazione, saranno invece bloccati.

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra il flusso ROPC.

![Flusso ROPC](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Authorization request (Richiesta di autorizzazione)

Il flusso ROPC è una richiesta singola: invia l'identificazione del client e le credenziali dell'utente all'IDP, quindi riceve i token in cambio. Il client deve richiedere l'indirizzo di posta elettronica (UPN) e la password dell'utente prima di procedere. Subito dopo una richiesta con esito positivo, il client dovrebbe rilasciare le credenziali dell'utente dalla memoria in modo sicuro. Non deve mai salvarle.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![Eseguire in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parametro | Condizione | DESCRIZIONE |
| --- | --- | --- |
| `tenant` | Obbligatorio | Il tenant della directory in cui si desidera registrare l'utente. Può essere fornito nel formato di nome descrittivo o GUID. Questo parametro non può essere impostato su `common` oppure `consumers`, ma può essere impostato su `organizations`. |
| `grant_type` | Obbligatorio | Il valore deve essere impostato su `password`. |
| `username` | Obbligatorio | L'indirizzo di posta elettronica dell'utente. |
| `password` | Obbligatorio | La password dell'utente. |
| `scope` | Consigliato | Un elenco delimitato da spazi di [ambiti](v2-permissions-and-consent.md), o privilegi, richiesti dall'app. In un flusso interattivo, l'amministratore o l'utente deve fornire il consenso a questi ambiti di anticipo. |

### <a name="successful-authentication-response"></a>Risposta di autenticazione con esito positivo

Nell'esempio seguente mostra una risposta token con esito positivo:

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
| `invalid_grant` | L'autenticazione non è riuscita | Le credenziali non sono corrette o il client non ha il consenso per gli ambiti richiesti. Se gli ambiti non sono concesse, un `consent_required` viene restituito l'errore. In questo caso, il client deve reindirizzare l'utente a un prompt interattivo con una webview o un browser. |
| `invalid_request` | La richiesta è stata costruita in modo non corretto | Il tipo di concessione non è supportato il `/common` o `/consumers` contesti di autenticazione.  Usare invece `/organizations`. |
| `invalid_client` | L'app è configurata in modo non corretto | Questa situazione può verificarsi se il `allowPublicClient` non è impostata su true nella [manifesto dell'applicazione](reference-app-manifest.md). La proprietà `allowPublicClient` è necessaria perché la concessione ROPC non dispone di un URI di reindirizzamento. Azure AD non può determinare se l'app è un'applicazione client pubblica o riservata se la proprietà non è impostata. ROPC è supportata solo per le app client pubblico. |

## <a name="learn-more"></a>Altre informazioni

* È possibile provare ROPC grazie all'[applicazione console di esempio](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Per determinare se è necessario usare l'endpoint v2.0, leggere l'argomento sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).
