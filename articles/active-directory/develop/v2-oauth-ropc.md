---
title: Accedi con la password del proprietario della risorsa concesse le credenziali. Azure
titleSuffix: Microsoft identity platform
description: Supporta i flussi di autenticazione senza browser usando la concessione di credenziali della password del proprietario della risorsa (ROPC).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 173f632a6baed0179b7b4facd2962f9c9764af47
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886331"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Piattaforma di identità Microsoft e credenziali della password del proprietario della risorsa OAuth 2.0

La piattaforma di identità Microsoft supporta la [concessione di credenziali ROPC (Resource Owner Password Credentials) OAuth 2.0,](https://tools.ietf.org/html/rfc6749#section-4.3)che consente a un'applicazione di accedere all'utente gestendo direttamente la password.  In questo articolo viene descritto come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile utilizzare le librerie di autenticazione Microsoft (MSAL) supportate per [acquisire token e chiamare API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dai anche un'occhiata alle [app di esempio che utilizzano MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft consiglia di _non_ utilizzare il flusso ROPC. Nella maggior parte degli scenari, sono disponibili e consigliate alternative più sicure. Questo flusso richiede un livello molto elevato di attendibilità nell'applicazione e comporta rischi che non sono presenti in altri flussi. È consigliabile usare questo flusso solo quando non è possibile usare altri flussi più sicuri.

> [!IMPORTANT]
>
> * L'endpoint della piattaforma di identità Microsoft supporta solo ROPC per i tenant di Azure AD, non per gli account personali. Questo significa che è necessario usare un endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`) o l'endpoint `organizations`.
> * Gli account personali che sono invitati in un tenant di Azure AD non possono usare ROPC.
> * Gli account che non dispongono di password non possono accedere tramite ROPC. Per questo scenario, è consigliabile utilizzare un flusso diverso per l'app.
> * Se gli utenti devono usare l'autenticazione a più fattori (MFA) per accedere all'applicazione, saranno invece bloccati.
> * ROPC non è supportato negli scenari [di federazione ibrida](/azure/active-directory/hybrid/whatis-fed) delle identità (ad esempio, Azure AD e ADFS usati per autenticare gli account locali). Se gli utenti vengono reindirizzati a pagina intera a un provider di identità locale, Azure AD non è in grado di testare il nome utente e la password rispetto a tale provider di identità. [Tuttavia, l'autenticazione pass-through](/azure/active-directory/hybrid/how-to-connect-pta) è supportata con ROPC.

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra il flusso ROPC.

![Diagramma che mostra il flusso delle credenziali della password del proprietario della risorsa](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Authorization request (Richiesta di autorizzazione)

Il flusso ROPC è una singola richiesta: invia l'identificazione del client e le credenziali dell'utente all'IDP e quindi riceve i token in cambio. Il client deve richiedere l'indirizzo di posta elettronica (UPN) e la password dell'utente prima di procedere. Subito dopo una richiesta con esito positivo, il client dovrebbe rilasciare le credenziali dell'utente dalla memoria in modo sicuro. Non deve mai salvarle.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![Prova a eseguire questa richiesta in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | Il tenant della directory in cui si desidera registrare l'utente. Può essere fornito nel formato di nome descrittivo o GUID. Questo parametro non può essere impostato su `common` oppure `consumers`, ma può essere impostato su `organizations`. |
| `client_id` | Obbligatoria | ID applicazione (client) assegnato alla pagina Portale [di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) assegnata all'app. | 
| `grant_type` | Obbligatoria | Il valore deve essere impostato su `password`. |
| `username` | Obbligatoria | L'indirizzo di posta elettronica dell'utente. |
| `password` | Obbligatoria | La password dell'utente. |
| `scope` | Consigliato | Un elenco delimitato da spazi di [ambiti](v2-permissions-and-consent.md), o privilegi, richiesti dall'app. In un flusso interattivo, l'amministratore o l'utente deve acconsentire a questi ambiti in anticipo. |
| `client_secret`| A volte richiesto | Se l'app è un `client_secret` client `client_assertion` pubblico, o non può essere incluso.  Se l'app è un client riservato, deve essere inclusa. | 
| `client_assertion` | A volte richiesto | Una forma `client_secret`diversa di , generata utilizzando un certificato.  Per ulteriori dettagli, [vedere le credenziali del certificato.](active-directory-certificate-credentials.md) | 

### <a name="successful-authentication-response"></a>Risposta di autenticazione con esito positivo

L'esempio seguente mostra una risposta del token riuscita:The following example shows a successful token response:

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
| `invalid_grant` | L'autenticazione non è riuscita | Le credenziali non sono corrette o il client non ha il consenso per gli ambiti richiesti. Se gli ambiti non vengono `consent_required` concessi, verrà restituito un errore. In questo caso, il client deve reindirizzare l'utente a un prompt interattivo con una webview o un browser. |
| `invalid_request` | La richiesta è stata costruita in modo non corretto | Il tipo di concessione `/common` non `/consumers` è supportato nei contesti di autenticazione o .  Utilizzare `/organizations` o un ID tenant invece. |

## <a name="learn-more"></a>Altre informazioni

* È possibile provare ROPC grazie all'[applicazione console di esempio](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Per determinare se è necessario utilizzare l'endpoint v2.0, leggere informazioni sulle limitazioni della piattaforma di [identità Microsoft](active-directory-v2-limitations.md).
