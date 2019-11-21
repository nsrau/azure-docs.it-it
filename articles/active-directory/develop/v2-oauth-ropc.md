---
title: Use Microsoft identity platform to sign in users using resource owner password credential (ROPC) grant | Azure
description: Come supportare i flussi di autenticazione senza browser tramite la concessione di credenziali password del proprietario della risorsa.
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
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4504a1ae60aaac790ca15c120433159c2ff78fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207775"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credentials"></a>Microsoft identity platform and the OAuth 2.0 Resource Owner Password Credentials

Microsoft identity platform supports the [OAuth 2.0 Resource Owner Password Credentials (ROPC) grant](https://tools.ietf.org/html/rfc6749#section-4.3), which allows an application to sign in the user by directly handling their password.  This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft recommends you do _not_ use the ROPC flow. In most scenarios, more secure alternatives are available and recommended. This flow requires a very high degree of trust in the application, and carries risks which are not present in other flows. You should only use this flow when other more secure flows can't be used.

> [!IMPORTANT]
>
> * The Microsoft identity platform endpoint only supports ROPC for Azure AD tenants, not personal accounts. Questo significa che è necessario usare un endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`) o l'endpoint `organizations`.
> * Gli account personali che sono invitati in un tenant di Azure AD non possono usare ROPC.
> * Gli account che non dispongono di password non possono accedere tramite ROPC. Per questo scenario, è consigliabile utilizzare un flusso diverso per l'app.
> * Se gli utenti devono usare l'autenticazione a più fattori (MFA) per accedere all'applicazione, saranno invece bloccati.
> * ROPC is not supported in [hybrid identity federation](/azure/active-directory/hybrid/whatis-fed) scenarios (for example, Azure AD and ADFS used to authenticate on-premises accounts). If users are full-page redirected to an on-premises identity providers, Azure AD is not able to test the username and password against that identity provider. [Pass-through authentication](/azure/active-directory/hybrid/how-to-connect-pta) is supported with ROPC, however.

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra il flusso ROPC.

![Diagram showing the resource owner password credential flow](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Authorization request (Richiesta di autorizzazione)

The ROPC flow is a single request: it sends the client identification and user's credentials to the IDP, and then receives tokens in return. Il client deve richiedere l'indirizzo di posta elettronica (UPN) e la password dell'utente prima di procedere. Subito dopo una richiesta con esito positivo, il client dovrebbe rilasciare le credenziali dell'utente dalla memoria in modo sicuro. Non deve mai salvarle.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parametro | Condizione | Description |
| --- | --- | --- |
| `tenant` | Obbligatoria | Il tenant della directory in cui si desidera registrare l'utente. Può essere fornito nel formato di nome descrittivo o GUID. Questo parametro non può essere impostato su `common` oppure `consumers`, ma può essere impostato su `organizations`. |
| `client_id` | Obbligatoria | The Application (client) ID that the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page assigned to your app. | 
| `grant_type` | Obbligatoria | Il valore deve essere impostato su `password`. |
| `username` | Obbligatoria | L'indirizzo di posta elettronica dell'utente. |
| `password` | Obbligatoria | La password dell'utente. |
| `scope` | Consigliato | Un elenco delimitato da spazi di [ambiti](v2-permissions-and-consent.md), o privilegi, richiesti dall'app. In an interactive flow, the admin or the user must consent to these scopes ahead of time. |
| `client_secret`| Sometimes required | If your app is a public client, then the `client_secret` or `client_assertion` cannot be included.  If the app is a confidential client, then it must be included. | 
| `client_assertion` | Sometimes required | A different form of `client_secret`, generated using a certificate.  See [certificate credentials](active-directory-certificate-credentials.md) for more details. | 

### <a name="successful-authentication-response"></a>Risposta di autenticazione con esito positivo

The following example shows a successful token response:

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

| Parametro | Format | Description |
| --------- | ------ | ----------- |
| `token_type` | Stringa | Sempre impostato su `Bearer`. |
| `scope` | Stringhe separate da uno spazio | Se è stato restituito un token di accesso, questo parametro elenca gli ambiti per cui è valido. |
| `expires_in`| int | Numero di secondi per cui il token di accesso incluso verrà considerato valido. |
| `access_token`| Stringa opaca | Emessa per gli [ambiti](v2-permissions-and-consent.md) che sono stati richiesti. |
| `id_token` | Token JSON Web | Emessa nel parametro `scope` originale incluso nell'ambito `openid`. |
| `refresh_token` | Stringa opaca | Emessa nel parametro `scope` originale incluso `offline_access`. |

È possibile usare il token di aggiornamento per acquisire nuovi token di accesso e token di aggiornamento usando lo stesso flusso descritto nella [documentazione del flusso del codice OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Risposta di errore

Se l'utente non ha fornito il nome utente o la password corretti, o il client non ha ricevuto il consenso richiesto, l'autenticazione avrà esito negativo.

| Errore | Description | Azione client |
|------ | ----------- | -------------|
| `invalid_grant` | L'autenticazione non è riuscita | Le credenziali non sono corrette o il client non ha il consenso per gli ambiti richiesti. If the scopes aren't granted, a `consent_required` error will be returned. In questo caso, il client deve reindirizzare l'utente a un prompt interattivo con una webview o un browser. |
| `invalid_request` | La richiesta è stata costruita in modo non corretto | The grant type isn't supported on the `/common` or `/consumers` authentication contexts.  Use `/organizations` or a tenant ID instead. |

## <a name="learn-more"></a>Altre informazioni.

* È possibile provare ROPC grazie all'[applicazione console di esempio](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* To determine whether you should use the v2.0 endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).
