---
title: Impostare gli URL di reindirizzamento su b2clogin.com - Azure Active Directory B2C | Microsoft Docs
description: Informazioni sull'uso di b2clogin.com negli URL di reindirizzamento per Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 080c1933f88d9e824969a42212de2eacd0f62e14
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927274"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Impostare gli URL di reindirizzamento su b2clogin.com per Azure Active Directory B2C

Quando si configura un provider di identità per l'iscrizione e l'accesso nell'applicazione Azure Active Directory (Azure AD) B2C, è necessario specificare un URL di reindirizzamento. In passato veniva usato login.microsoftonline.com, ma ora è consigliabile usare b2clogin.com.

> [!NOTE]
> È possibile usare il codice lato client JavaScript (attualmente in anteprima) in b2clogin.com. Se si usa login.microsoftonline.com, il codice JavaScript verrà rimosso dalla pagina personalizzata. Anche le restrizioni di sicurezza aggiuntive vengono applicate a login.microsoftonline.com, ad esempio la rimozione di elementi del modulo HTML dalla pagina personalizzata. 

L'uso di b2clogin.com offre vantaggi aggiuntivi, ad esempio:

- Lo spazio usato nell'intestazione cookie dei servizi Microsoft viene ridotto.
- Gli URL non includono più un riferimento a Microsoft. Ad esempio `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

> [!NOTE]
> È possibile usare sia il nome del tenant che il GUID del tenant come indicato di seguito:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`(che fa ancora riferimento `onmicrosoft.com`a)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid`(in questo caso non esiste alcun riferimento a Microsoft)
>
> Tuttavia, non è possibile usare un _dominio personalizzato_ per il tenant di Azure Active Directory B2C, `https://your-tenant-name.b2clogin.com/your-custom-domain-name` ad esempio, _non_ funzionerebbe.

Prendere in considerazione queste impostazioni che potrebbe essere necessario modificare quando si usa b2clogin.com:

- Impostare gli URL di reindirizzamento nelle applicazioni del provider di identità per l'uso di b2clogin.com. 
- Impostare l'applicazione Azure AD B2C per l'uso di b2clogin.com per i riferimenti dei flussi utente e gli endpoint di token. 
- Se si usa MSAL, è necessario impostare la proprietà **ValidateAuthority** su `false`.
- Assicurarsi di modificare le **Origini consentite** definite nelle impostazioni CORS per la [personalizzazione dell'interfaccia utente](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Modificare gli URL di reindirizzamento

Per usare b2clogin.com, nelle impostazioni per l'applicazione del provider di identità cercare e modificare l'elenco degli URL attendibili per eseguire il reindirizzamento ad Azure AD B2C.  Attualmente, è probabile che sia configurato per il reindirizzamento a un sito login.microsoftonline.com. 

Sarà necessario modificare l'URL di reindirizzamento in modo che `your-tenant-name.b2clogin.com` sia autorizzato. Assicurarsi di sostituire `your-tenant-name` con il nome del tenant di Azure AD B2C e rimuovere `/te`, se presente nell'URL. Dato che esistono lievi variazioni in questo URL per ogni provider di identità, vedere la pagina corrispondente per ottenere l'URL esatto.

Le informazioni relative alla configurazione per i provider di identità sono disponibili negli articoli seguenti:

- [Account Microsoft](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [OIDC personalizzato](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Aggiornare l'applicazione

L'applicazione Azure AD B2C fa probabilmente riferimento a `login.microsoftonline.com` in diverse posizioni, ad esempio nei riferimenti dei flussi utente e negli endpoint di token.  Verificare che l'endpoint di autorizzazione, l'endpoint di token e l'autorità di certificazione siano stati aggiornati in modo da usare `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Impostare la proprietà ValidateAuthority

Se si usa MSAL, impostare la proprietà **ValidateAuthority** su `false`. Quando **ValidateAuthority** è impostata su `false`, sono consentiti i reindirizzamenti a b2clogin.com. 

L'esempio seguente illustra come si può impostare la proprietà:

In [MSAL per .Net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

In [MSAL per JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
