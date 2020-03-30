---
title: Eseguire la migrazione di applicazioni e API a b2clogin.com
titleSuffix: Azure AD B2C
description: Informazioni sull'uso di b2clogin.com negli URL di reindirizzamento per Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189991"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Impostare gli URL di reindirizzamento su b2clogin.com per Azure Active Directory B2C

Quando si configura un provider di identità per l'iscrizione e l'accesso nell'applicazione Azure Active Directory B2C (Azure AD B2C), è necessario specificare un URL di reindirizzamento. Non è più necessario fare riferimento a *login.microsoftonline.com* nelle applicazioni e nelle API. Utilizzare *invece b2clogin.com* per tutte le nuove applicazioni ed eseguire la migrazione delle applicazioni esistenti da *login.microsoftonline.com* a *b2clogin.com.*

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Deprecazione delllogin.microsoftonline.com

Il 04 dicembre 2019 è stato annunciato il ritiro pianificato del supporto di login.microsoftonline.com in Azure AD B2C il **04 dicembre 2020:**

[Azure Active Directory B2C sta deprecando login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

La deprecazione di login.microsoftonline.com diventa effettiva per tutti i tenant B2C di Azure AD il 04 dicembre 2020, fornendo ai tenant esistenti un anno (1) per eseguire la migrazione a b2clogin.com. I nuovi tenant creati dopo il 04 dicembre 2019 non accetteranno richieste da login.microsoftonline.com. Tutte le funzionalità rimangono invariate sull'endpoint b2clogin.com.

La deprecazione di login.microsoftonline.com non influisce sui tenant di Azure Active Directory.The deprecation of login.microsoftonline.com does not impact Azure Active Directory tenants. Solo i tenant di Azure Active Directory B2C sono interessati da questa modifica.

## <a name="benefits-of-b2clogincom"></a>Vantaggi della b2clogin.com

Quando utilizzi *b2clogin.com* come URL di reindirizzamento:

* Lo spazio usato nell'intestazione cookie dei servizi Microsoft viene ridotto.
* Gli URL di reindirizzamento non devono più includere un riferimento a Microsoft.Your redirect URLs no longer need to include a reference to Microsoft.
* Il codice lato client JavaScript è supportato (attualmente in [anteprima)](user-flow-javascript-overview.md)nelle pagine personalizzate. A causa di restrizioni di sicurezza, il codice JavaScript e gli elementi del modulo HTML vengono rimossi dalle pagine personalizzate se si utilizza *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Panoramica delle modifiche necessarie

Esistono diverse modifiche che potrebbe essere necessario apportare per eseguire la migrazione delle applicazioni a *b2clogin.com:*

* Modificare l'URL di reindirizzamento nelle applicazioni del provider di identità in modo che faccia riferimento a *b2clogin.com*.
* Aggiornare le applicazioni B2C di Azure AD in modo che *b2clogin.com* nei riferimenti al flusso utente e all'endpoint token.
* Aggiornare **le origini consentite** definite nelle impostazioni CORS per la [personalizzazione dell'interfaccia utente.](custom-policy-ui-customization.md)

## <a name="change-identity-provider-redirect-urls"></a>Modificare gli URL di reindirizzamento del provider di identità

Nel sito Web di ogni provider di identità in cui è stata `your-tenant-name.b2clogin.com` creata un'applicazione modificare tutti gli URL attendibili a cui eseguire il reindirizzamento anziché *login.microsoftonline.com*.

Esistono due formati che è possibile utilizzare per gli URL di reindirizzamento b2clogin.com. Il primo offre il vantaggio di non avere "Microsoft" in qualsiasi punto dell'URL utilizzando l'ID tenant (un GUID) al posto del nome di dominio tenant:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

La seconda opzione utilizza il nome `your-tenant-name.onmicrosoft.com`di dominio tenant nel formato di . Ad esempio:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Per entrambi i formati:

* Sostituire `{your-tenant-name}` con il nome del tenant di Azure AD B2C.
* Rimuovi `/te` se presente nell'URL.

## <a name="update-your-applications-and-apis"></a>Aggiornare le applicazioni e le API

Il codice nelle api e nelle applicazioni abilitate per `login.microsoftonline.com` Il B2C di Azure AD può fare riferimento in diverse posizioni. Ad esempio, il codice potrebbe avere riferimenti a i flussi utente e gli endpoint token. Aggiornare quanto segue `your-tenant-name.b2clogin.com`per fare riferimento invece a :

* Authorization endpoint (Endpoint di autorizzazione)
* Token endpoint (Endpoint di token)
* Emittente di token

Ad esempio, l'endpoint dell'autorità per i criteri di iscrizione/accesso di Contoso sarà ora:For example, the authority endpoint for Contoso's sign-up/sign-in policy would now:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Per informazioni sulla migrazione di applicazioni Web basate su OWIN a b2clogin.com, vedere Eseguire la migrazione di [un'API Web basata](multiple-token-endpoints.md)su OWIN a b2clogin.com .

Per la migrazione delle API di Gestione API di Azure protette da Azure AD B2C, vedere la sezione [Eseguire la migrazione a b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) in Proteggere un'API di gestione API di Azure con Azure AD [B2C.](secure-api-management.md)

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority (proprietà)

Se si utilizza [MSAL.NET][msal-dotnet] versione 2 o precedente, `false` impostare la proprietà **ValidateAuthority** su nella creazione di istanze client per consentire i reindirizzamenti a *b2clogin.com*. Questa impostazione non è necessaria per MSAL.NET v3 e versioni successive.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Se si utilizza [MSAL per JavaScript:][msal-js]

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla migrazione di applicazioni Web basate su OWIN a b2clogin.com, vedere Eseguire la migrazione di [un'API Web basata](multiple-token-endpoints.md)su OWIN a b2clogin.com .

Per la migrazione delle API di Gestione API di Azure protette da Azure AD B2C, vedere la sezione [Eseguire la migrazione a b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) in Proteggere un'API di gestione API di Azure con Azure AD [B2C.](secure-api-management.md)

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
