---
title: Usare la piattaforma di identità Microsoft per accedere agli utenti usando la concessione delle credenziali password del proprietario della risorsa (ROPC) | Azure
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
ms.date: 10/11/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 322e0e5f740bd416c7831f32e0d74f9290335fe3
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195756"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credentials"></a>Microsoft Identity Platform e le credenziali password del proprietario della risorsa OAuth 2,0

Microsoft Identity Platform supporta la [concessione delle credenziali password del proprietario della risorsa OAuth 2,0 (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), che consente a un'applicazione di accedere all'utente gestendo direttamente la password.

> [!WARNING]
> Microsoft consiglia di _non_ usare il flusso ROPC. Nella maggior parte degli scenari sono disponibili e consigliate alternative più sicure. Questo flusso richiede un livello di attendibilità molto elevato nell'applicazione e comporta rischi che non sono presenti in altri flussi. Usare questo flusso solo quando non è possibile usare altri flussi più sicuri.

> [!IMPORTANT]
>
> * L'endpoint della piattaforma Microsoft Identity supporta solo ROPC per Azure AD tenant e non per account personali. Questo significa che è necessario usare un endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`) o l'endpoint `organizations`.
> * Gli account personali che sono invitati in un tenant di Azure AD non possono usare ROPC.
> * Gli account che non dispongono di password non possono accedere tramite ROPC. Per questo scenario, è consigliabile utilizzare un flusso diverso per l'app.
> * Se gli utenti devono usare l'autenticazione a più fattori (MFA) per accedere all'applicazione, saranno invece bloccati.
> * ROPC non è supportato in scenari di [Federazione di identità ibrida](/azure/active-directory/hybrid/whatis-fed) , ad esempio Azure ad e ADFS usati per autenticare gli account locali. Se gli utenti vengono reindirizzati a una pagina intera a un provider di identità locale, Azure AD non è in grado di testare il nome utente e la password con tale provider di identità. Tuttavia, [l'autenticazione pass-through](/azure/active-directory/hybrid/how-to-connect-pta) è supportata con ROPC.

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra il flusso ROPC.

![Diagramma che mostra il flusso di credenziali della password del proprietario della risorsa](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Authorization request (Richiesta di autorizzazione)

Il flusso ROPC è una singola richiesta: Invia le credenziali dell'utente e dell'identificazione del client al provider di identità e quindi riceve i token in fase di restituzione. Il client deve richiedere l'indirizzo di posta elettronica (UPN) e la password dell'utente prima di procedere. Subito dopo una richiesta con esito positivo, il client dovrebbe rilasciare le credenziali dell'utente dalla memoria in modo sicuro. Non deve mai salvarle.

> [!TIP]
> Provare a eseguire la richiesta in Postman.
> [![provare a eseguire la richiesta in un post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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

| . | Condizione | DESCRIZIONE |
| --- | --- | --- |
| `tenant` | obbligatori | Il tenant della directory in cui si desidera registrare l'utente. Può essere fornito nel formato di nome descrittivo o GUID. Questo parametro non può essere impostato su `common` oppure `consumers`, ma può essere impostato su `organizations`. |
| `client_id` | obbligatori | ID dell'applicazione (client) che la pagina [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) assegnata all'app. | 
| `grant_type` | obbligatori | Il valore deve essere impostato su `password`. |
| `username` | obbligatori | L'indirizzo di posta elettronica dell'utente. |
| `password` | obbligatori | La password dell'utente. |
| `scope` | Consigliato | Un elenco delimitato da spazi di [ambiti](v2-permissions-and-consent.md), o privilegi, richiesti dall'app. In un flusso interattivo, l'amministratore o l'utente deve dare il consenso in anticipo a questi ambiti. |
| `client_secret`| A volte obbligatorio | Se l'app è un client pubblico, non è possibile includere il `client_secret` o `client_assertion`.  Se l'app è un client riservato, è necessario includerla. | 
| `client_assertion` | A volte obbligatorio | Una forma diversa di `client_secret`, generata utilizzando un certificato.  Per ulteriori informazioni, vedere [credenziali del certificato](active-directory-certificate-credentials.md) . | 

### <a name="successful-authentication-response"></a>Risposta di autenticazione con esito positivo

Nell'esempio seguente viene illustrata una risposta di token riuscita:

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

| . | Format | DESCRIZIONE |
| --------- | ------ | ----------- |
| `token_type` | String | Sempre impostato su `Bearer`. |
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
| `invalid_grant` | L'autenticazione non è riuscita | Le credenziali non sono corrette o il client non ha il consenso per gli ambiti richiesti. Se gli ambiti non sono concessi, viene restituito un errore di `consent_required`. In questo caso, il client deve reindirizzare l'utente a un prompt interattivo con una webview o un browser. |
| `invalid_request` | La richiesta è stata costruita in modo non corretto | Il tipo di concessione non è supportato nei contesti di autenticazione `/common` o `/consumers`.  Usare invece `/organizations` o un ID tenant. |

## <a name="learn-more"></a>Altre informazioni

* È possibile provare ROPC grazie all'[applicazione console di esempio](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Per determinare se è necessario usare l'endpoint v 2.0, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).
