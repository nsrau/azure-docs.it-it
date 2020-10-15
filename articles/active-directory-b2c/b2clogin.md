---
title: Eseguire la migrazione di applicazioni e API a b2clogin.com
titleSuffix: Azure AD B2C
description: Informazioni sull'uso di b2clogin.com negli URL di reindirizzamento per Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 53d41b5024b29a8c6c394d65a3ce36f8bb878fc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90524981"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Impostare gli URL di reindirizzamento su b2clogin.com per Azure Active Directory B2C

Quando si configura un provider di identità per l'iscrizione e l'accesso nell'applicazione Azure Active Directory B2C (Azure AD B2C), è necessario specificare un URL di reindirizzamento. Non è più necessario fare riferimento a *login.microsoftonline.com* nelle applicazioni e nelle API per l'autenticazione degli utenti con Azure ad B2C. Usare invece *b2clogin.com* per tutte le nuove applicazioni ed eseguire la migrazione delle applicazioni esistenti da *login.microsoftonline.com* a *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Deprecazione di login.microsoftonline.com

Il 04 dicembre 2019 abbiamo annunciato il ritiro pianificato del supporto login.microsoftonline.com in Azure AD B2C il **04 dicembre 2020**:

[Azure Active Directory B2C deprecato login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

La deprecazione di login.microsoftonline.com viene applicata a tutti i tenant Azure AD B2C il 04 dicembre 2020, fornendo ai tenant esistenti uno (1) anno di migrazione a b2clogin.com. I nuovi tenant creati dopo il 04 dicembre 2019 non accetteranno le richieste da login.microsoftonline.com. Tutte le funzionalità rimangono invariate sull'endpoint b2clogin.com.

La deprecazione di login.microsoftonline.com non ha alcun effetto Azure Active Directory tenant. Questa modifica ha effetto solo sui tenant Azure Active Directory B2C.

## <a name="what-endpoints-does-this-apply-to"></a>A quali endpoint si applica questa operazione
La transizione a b2clogin.com si applica solo agli endpoint di autenticazione che usano criteri di Azure AD B2C (flussi utente o criteri personalizzati) per autenticare gli utenti. Questi endpoint hanno un `<policy-name>` parametro che specifica i criteri Azure ad B2C deve usare. [Altre informazioni sui criteri di Azure ad B2C](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

Questi endpoint possono avere un aspetto simile al seguente:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

In alternativa, `<policy-name>` può essere passato come parametro di query:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> Gli endpoint che usano il parametro ' Policy ' devono essere aggiornati e gli [URL di reindirizzamento del provider di identità](#change-identity-provider-redirect-urls).

Alcuni Azure AD B2C clienti usano le funzionalità condivise di Azure AD tenant aziendali come il flusso di concessione delle credenziali client OAuth 2,0. È possibile accedere a queste funzionalità usando gli endpoint login.microsoftonline.com di Azure AD, *che non contengono un parametro dei criteri*. __Questi endpoint non sono interessati__.

## <a name="benefits-of-b2clogincom"></a>Vantaggi di b2clogin.com

Quando si usa *b2clogin.com* come URL di reindirizzamento:

* Lo spazio usato nell'intestazione cookie dei servizi Microsoft viene ridotto.
* Gli URL di reindirizzamento non devono più includere un riferimento a Microsoft.
* Il codice lato client JavaScript è supportato (attualmente in [Anteprima](user-flow-javascript-overview.md)) in pagine personalizzate. A causa delle restrizioni di sicurezza, gli elementi del codice JavaScript e del modulo HTML vengono rimossi dalle pagine personalizzate se si utilizza *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Panoramica delle modifiche necessarie

Per eseguire la migrazione delle applicazioni a *b2clogin.com*, potrebbe essere necessario apportare diverse modifiche:

* Modificare l'URL di reindirizzamento nelle applicazioni del provider di identità in modo che faccia riferimento a *b2clogin.com*.
* Aggiornare le applicazioni Azure AD B2C in modo da usare *b2clogin.com* nei riferimenti all'endpoint del flusso utente e del token. Ciò può includere l'aggiornamento dell'utilizzo di una libreria di autenticazione come Microsoft Authentication Library (MSAL).
* Aggiornare le **origini consentite** definite nelle impostazioni CORS per la [personalizzazione dell'interfaccia utente](custom-policy-ui-customization.md).

Un endpoint precedente potrebbe essere simile al seguente:
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

Un endpoint aggiornato corrispondente sarà simile al seguente:
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>Modificare gli URL di reindirizzamento del provider di identità

Nel sito Web del provider di identità in cui è stata creata un'applicazione, modificare tutti gli URL attendibili per il reindirizzamento al `your-tenant-name.b2clogin.com` anziché *login.microsoftonline.com*.

Esistono due formati che è possibile usare per gli URL di reindirizzamento di b2clogin.com. Il primo consente di non visualizzare "Microsoft" in qualsiasi punto dell'URL usando l'ID tenant (GUID) al posto del nome di dominio del tenant:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

La seconda opzione Usa il nome di dominio del tenant nel formato `your-tenant-name.onmicrosoft.com` . Ad esempio:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Per entrambi i formati:

* Sostituire `{your-tenant-name}` con il nome del tenant di Azure AD B2C.
* Rimuovere `/te` se è presente nell'URL.

## <a name="update-your-applications-and-apis"></a>Aggiornare le applicazioni e le API

Il codice nelle applicazioni e nelle API abilitate per Azure AD B2C può riferirsi a `login.microsoftonline.com` in diversi punti. Ad esempio, il codice potrebbe avere riferimenti a flussi utente ed endpoint token. Aggiornare il codice seguente in modo che faccia riferimento a `your-tenant-name.b2clogin.com` :

* Authorization endpoint (Endpoint di autorizzazione)
* Token endpoint (Endpoint di token)
* Emittente del token

Ad esempio, l'endpoint dell'autorità per i criteri di iscrizione/accesso di Contoso ora sarà:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Per informazioni sulla migrazione di applicazioni Web basate su OWIN in b2clogin.com, vedere [eseguire la migrazione di un'API Web basata su OWIN a b2clogin.com](multiple-token-endpoints.md).

Per la migrazione delle API di gestione API di Azure protette da Azure AD B2C, vedere la sezione [eseguire la migrazione a b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) di [proteggere un'API di gestione API di Azure con Azure ad B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="msalnet-validateauthority-property"></a>Proprietà ValidateAuthority di MSAL.NET

Se si usa [MSAL.NET][msal-dotnet] v2 o versioni precedenti, impostare la proprietà **ValidateAuthority** `false` su on creazione di un'istanza del client per consentire reindirizzamenti a *b2clogin.com*. L'impostazione di questo valore su `false` non è necessaria per MSAL.NET V3 e versioni successive.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>Proprietà validateAuthority di MSAL per JavaScript

Se si usa [MSAL per JavaScript][msal-js] v 1.2.2 o versioni precedenti, impostare la proprietà **validateAuthority** su `false` .

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

Se si imposta `validateAuthority: true` in MSAL.js 1.3.0 + (impostazione predefinita), è necessario specificare anche un emittente di token valido con `knownAuthorities` :

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla migrazione di applicazioni Web basate su OWIN in b2clogin.com, vedere [eseguire la migrazione di un'API Web basata su OWIN a b2clogin.com](multiple-token-endpoints.md).

Per la migrazione delle API di gestione API di Azure protette da Azure AD B2C, vedere la sezione [eseguire la migrazione a b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) di [proteggere un'API di gestione API di Azure con Azure ad B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
