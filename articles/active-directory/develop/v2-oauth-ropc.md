---
title: Eseguire l'accesso con la concessione delle credenziali di tipo password del proprietario della risorsa | Azure
titleSuffix: Microsoft identity platform
description: Supportare i flussi di autenticazione senza browser usando la concessione di credenziali di tipo password del proprietario della risorsa.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bfc6b6fa6a2af8750c868aaacb289d39306ce06e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770977"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft Identity Platform e credenziali di tipo password del proprietario della risorsa OAuth 2.0

Microsoft Identity Platform supporta la [concessione di credenziali di tipo password del proprietario della risorsa (ROPC) OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3), attraverso la quale un'applicazione consente all'utente di accedere gestendo direttamente la sua password.  Questo articolo descrive come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile usare le librerie di autenticazione Microsoft (MSAL) supportate anziché [acquisire i token e chiamare le API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Vedere anche le [app di esempio che usano MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft consiglia di _non_ usare il flusso ROPC. Per la maggior parte degli scenari sono disponibili e consigliate alternative più sicure. Questo flusso richiede un livello di attendibilità molto elevato nell'applicazione e comporta rischi che non sono presenti in altri flussi. Usare questo flusso solo quando non è possibile usare altri flussi più sicuri.

> [!IMPORTANT]
>
> * L'endpoint di Microsoft Identity Platform supporta solo ROPC per tenant di Azure AD, non per gli account personali. Questo significa che è necessario usare un endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`) o l'endpoint `organizations`.
> * Gli account personali che sono invitati in un tenant di Azure AD non possono usare ROPC.
> * Gli account che non dispongono di password non possono accedere tramite ROPC. Per questo scenario, è consigliabile utilizzare un flusso diverso per l'app.
> * Se gli utenti devono usare [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) per accedere all'applicazione, saranno invece bloccati.
> * ROPC non è supportato negli scenari di [federazione delle identità ibride](/azure/active-directory/hybrid/whatis-fed) (ad esempio, Azure AD e il file system distribuito di Azure usati per autenticare gli account locali). Se gli utenti vengono reindirizzati con l'intera pagina a un provider di identità locale, Azure AD non riesce a verificare il nome utente e la password con tale provider di identità. L'[autenticazione pass-through](/azure/active-directory/hybrid/how-to-connect-pta) è tuttavia supportata con ROPC.

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra il flusso ROPC.

![Diagramma che mostra il flusso delle credenziali di tipo password del proprietario della risorsa](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Authorization request (Richiesta di autorizzazione)

Il flusso ROPC è una richiesta singola: invia l'identificazione del client e le credenziali dell'utente all'IDP, quindi riceve i token in cambio. Il client deve richiedere l'indirizzo di posta elettronica (UPN) e la password dell'utente prima di procedere. Subito dopo una richiesta con esito positivo, il client dovrebbe rilasciare le credenziali dell'utente dalla memoria in modo sicuro. Non deve mai salvarle.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![Provare a eseguire la richiesta in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
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

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | Il tenant della directory in cui si desidera registrare l'utente. Può essere fornito nel formato di nome descrittivo o GUID. Questo parametro non può essere impostato su `common` oppure `consumers`, ma può essere impostato su `organizations`. |
| `client_id` | Obbligatoria | ID dell'applicazione (client) che la pagina [Portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) ha assegnato all'app. |
| `grant_type` | Obbligatoria | Il valore deve essere impostato su `password`. |
| `username` | Obbligatoria | L'indirizzo di posta elettronica dell'utente. |
| `password` | Obbligatoria | La password dell'utente. |
| `scope` | Consigliato | Un elenco delimitato da spazi di [ambiti](v2-permissions-and-consent.md), o privilegi, richiesti dall'app. In un flusso interattivo l'amministratore o l'utente deve dare il consenso in anticipo a questi ambiti. |
| `client_secret`| A volte obbligatorio | Se l'app è un client pubblico, non è possibile includere `client_secret` o `client_assertion`.  Se l'app è un client riservato, è necessario includerlo. |
| `client_assertion` | A volte obbligatorio | Una forma diversa di `client_secret`, generata usando un certificato.  Per informazioni dettagliate, vedere [Credenziali di un certificato](active-directory-certificate-credentials.md). |

### <a name="successful-authentication-response"></a>Risposta di autenticazione con esito positivo

L'esempio seguente illustra una risposta del token con esito positivo:

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
| `token_type` | string | Sempre impostato su `Bearer`. |
| `scope` | Stringhe separate da uno spazio | Se è stato restituito un token di accesso, questo parametro elenca gli ambiti per cui è valido. |
| `expires_in`| INT | Numero di secondi per cui il token di accesso incluso verrà considerato valido. |
| `access_token`| Stringa opaca | Emessa per gli [ambiti](v2-permissions-and-consent.md) che sono stati richiesti. |
| `id_token` | Token JSON Web | Emessa nel parametro `scope` originale incluso nell'ambito `openid`. |
| `refresh_token` | Stringa opaca | Emessa nel parametro `scope` originale incluso `offline_access`. |

È possibile usare il token di aggiornamento per acquisire nuovi token di accesso e token di aggiornamento usando lo stesso flusso descritto nella [documentazione del flusso del codice OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Risposta di errore

Se l'utente non ha fornito il nome utente o la password corretti, o il client non ha ricevuto il consenso richiesto, l'autenticazione avrà esito negativo.

| Errore | Descrizione | Azione client |
|------ | ----------- | -------------|
| `invalid_grant` | L'autenticazione non è riuscita | Le credenziali non sono corrette o il client non ha il consenso per gli ambiti richiesti. Se gli ambiti non sono concessi, verrà restituito un errore `consent_required`. In questo caso, il client deve reindirizzare l'utente a un prompt interattivo con una webview o un browser. |
| `invalid_request` | La richiesta è stata costruita in modo non corretto | Il tipo di concessione non è supportato nei contesti di autenticazione `/common` o `/consumers`.  Usare invece `/organizations` o un ID tenant. |

## <a name="learn-more"></a>Altre informazioni

* È possibile provare ROPC grazie all'[applicazione console di esempio](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Per determinare se è necessario usare l'endpoint 2.0, vedere le [limitazioni di Microsoft Identity Platform](active-directory-v2-limitations.md).
