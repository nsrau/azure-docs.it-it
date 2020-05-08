---
title: Accesso con la concessione delle credenziali password del proprietario della risorsa | Azure
titleSuffix: Microsoft identity platform
description: Supportare i flussi di autenticazione senza browser usando la concessione delle credenziali password del proprietario della risorsa (ROPC).
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
ms.openlocfilehash: 26b3cb343aba2d45d5a14944a7f8856715bca100
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690092"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Credenziali della password del proprietario della risorsa OAuth 2,0 e della piattaforma di identità Microsoft

Microsoft Identity Platform supporta la [concessione delle credenziali password del proprietario della risorsa OAuth 2,0 (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), che consente a un'applicazione di accedere all'utente gestendo direttamente la password.  Questo articolo descrive come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile usare invece le librerie di autenticazione Microsoft (MSAL) supportate per [acquisire i token e chiamare le API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Esaminare anche le [app di esempio che usano MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft consiglia di _non_ usare il flusso ROPC. Nella maggior parte degli scenari sono disponibili e consigliate alternative più sicure. Questo flusso richiede un livello di attendibilità molto elevato nell'applicazione e comporta rischi che non sono presenti in altri flussi. Usare questo flusso solo quando non è possibile usare altri flussi più sicuri.

> [!IMPORTANT]
>
> * L'endpoint della piattaforma Microsoft Identity supporta solo ROPC per Azure AD tenant e non per account personali. Questo significa che è necessario usare un endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`) o l'endpoint `organizations`.
> * Gli account personali che sono invitati in un tenant di Azure AD non possono usare ROPC.
> * Gli account che non dispongono di password non possono accedere tramite ROPC. Per questo scenario, è consigliabile utilizzare un flusso diverso per l'app.
> * Se gli utenti devono usare l'autenticazione a più fattori (MFA) per accedere all'applicazione, saranno invece bloccati.
> * ROPC non è supportato in scenari di [Federazione di identità ibrida](/azure/active-directory/hybrid/whatis-fed) , ad esempio Azure ad e ADFS usati per autenticare gli account locali. Se gli utenti vengono reindirizzati a una pagina intera a un provider di identità locale, Azure AD non è in grado di testare il nome utente e la password con tale provider di identità. Tuttavia, [l'autenticazione pass-through](/azure/active-directory/hybrid/how-to-connect-pta) è supportata con ROPC.

## <a name="protocol-diagram"></a>Diagramma del protocollo

Il diagramma seguente mostra il flusso di concessione delle credenziali password del proprietario della risorsa.

![Diagramma che mostra il flusso di credenziali della password del proprietario della risorsa](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Richiesta di autorizzazione

Il flusso ROPC è una singola richiesta: Invia le credenziali dell'utente e dell'identificazione del client al provider di identità e quindi riceve i token in fase di restituzione. Prima di eseguire questa operazione, il client deve richiedere l'indirizzo di posta elettronica (UPN) e la password dell'utente. Subito dopo una richiesta con esito positivo, il client deve rilasciare in modo sicuro le credenziali dell'utente dalla memoria. Non deve mai salvarle.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![Provare a eseguire la richiesta in un post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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
| `tenant` | Necessario | Il tenant della directory in cui si desidera registrare l'utente. Può essere fornito nel formato di nome descrittivo o GUID. Questo parametro non può essere impostato su `common` oppure `consumers`, ma può essere impostato su `organizations`. |
| `client_id` | Necessario | ID dell'applicazione (client) che la pagina [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) assegnata all'app. |
| `grant_type` | Necessario | Il valore deve essere impostato su `password`. |
| `username` | Necessario | Indirizzo di posta elettronica dell'utente |
| `password` | Necessario | Password dell'utente. |
| `scope` | Consigliato | Un elenco delimitato da spazi di [ambiti](v2-permissions-and-consent.md), o privilegi, richiesti dall'app. In un flusso interattivo, l'amministratore o l'utente deve dare il consenso in anticipo a questi ambiti. |
| `client_secret`| A volte obbligatorio | Se l'app è un client pubblico, non è `client_secret` possibile `client_assertion` includere o.  Se l'app è un client riservato, è necessario includerla. |
| `client_assertion` | A volte obbligatorio | Formato diverso di `client_secret`, generato utilizzando un certificato.  Per ulteriori informazioni, vedere [credenziali del certificato](active-directory-certificate-credentials.md) . |

### <a name="successful-authentication-response"></a>Risposta di autenticazione di esito positivo

Nell'esempio seguente viene illustrata una risposta di token di esito positivo:

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
| `scope` | Stringhe separate da uno spazio | Se è stato restituito un token di accesso, questo parametro elenca gli ambiti per i quali è valido il token di accesso. |
| `expires_in`| INT | Numero di secondi durante i quali è valido il token di accesso incluso. |
| `access_token`| Stringa opaca | Rilasciato per gli [ambiti](v2-permissions-and-consent.md) richiesti. |
| `id_token` | Token JSON Web | Emessa nel parametro `scope` originale incluso nell'ambito `openid`. |
| `refresh_token` | Stringa opaca | Emessa nel parametro `scope` originale incluso `offline_access`. |

È possibile usare il token di aggiornamento per acquisire nuovi token di accesso e token di aggiornamento usando lo stesso flusso descritto nella [documentazione del flusso del codice OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Risposta di errore

Se l'utente non ha fornito il nome utente o la password corretti, o il client non ha ricevuto il consenso richiesto, l'autenticazione avrà esito negativo.

| Errore | Descrizione | Azione client |
|------ | ----------- | -------------|
| `invalid_grant` | L'autenticazione non è riuscita | Le credenziali non sono corrette o il client non ha il consenso per gli ambiti richiesti. Se gli ambiti non sono concessi `consent_required` , viene restituito un errore. In questo caso, il client deve reindirizzare l'utente a un prompt interattivo con una webview o un browser. |
| `invalid_request` | La richiesta è stata costruita in modo non corretto | Il tipo di concessione non è supportato `/common` nei `/consumers` contesti di autenticazione o.  In `/organizations` alternativa, usare o un ID tenant. |

## <a name="learn-more"></a>Altre informazioni

* È possibile provare ROPC grazie all'[applicazione console di esempio](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Per determinare se è necessario usare l'endpoint v 2.0, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).
