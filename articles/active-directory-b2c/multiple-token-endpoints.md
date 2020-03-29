---
title: Eseguire la migrazione delle API Web basate su OWIN a b2clogin.com
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare un'API Web .NET per supportare i token rilasciati da più autorità di certificazione di token durante la migrazione delle applicazioni a b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184095"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Eseguire la migrazione di un'API Web basata su OWIN a b2clogin.com

In questo articolo viene descritta una tecnica per abilitare il supporto per più autorità di certificazione di token nelle API Web che implementano [l'interfaccia Web aperta per .NET (OWIN).](http://owin.org/) Il supporto di più endpoint token è utile quando si esegue la migrazione delle API B2C (Azure AD B2C) di Azure Active Directory (Azure AD B2C) e delle relative applicazioni da *login.microsoftonline.com* a *b2clogin.com.*

Aggiungendo il supporto nell'API per l'accettazione di token emessi sia da b2clogin.com che da login.microsoftonline.com, è possibile eseguire la migrazione delle applicazioni Web in modo infasi prima di rimuovere il supporto per i token emessi da login.microsoftonline.com dall'API.

Le sezioni seguenti presentano un esempio di come abilitare più autorità di certificazione in un'API Web che utilizza i componenti middleware [di Microsoft OWIN][katana] (Katana). Anche se gli esempi di codice sono specifici per il middleware Microsoft OWIN, la tecnica generale deve essere applicabile ad altre librerie OWIN.

> [!NOTE]
> Questo articolo è destinato ai clienti B2C di Azure `login.microsoftonline.com` AD con API e `b2clogin.com` applicazioni attualmente distribuite che fanno riferimento e che desiderano eseguire la migrazione all'endpoint consigliato. Se si sta configurando una nuova applicazione, utilizzare [b2clogin.com](b2clogin.md) indicato di seguito.

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con i passaggi descritti in questo articolo, sono necessarie le risorse B2C di Azure AD seguenti:You need the following Azure AD B2C resources in place before continue with the steps in this article:

* [Flussi utente](tutorial-create-user-flows.md) o criteri personalizzati creati nel tenantUser flows or [custom policies](custom-policy-get-started.md) created in your tenant

## <a name="get-token-issuer-endpoints"></a>Ottenere gli endpoint dell'autorità emittente di tokenGet token issuer endpoints

È innanzitutto necessario ottenere gli URI dell'endpoint dell'autorità emittente di token per ogni autorità emittente che si vuole supportare nell'API. Per ottenere il *b2clogin.com* e *login.microsoftonline.com* gli endpoint supportati dal tenant di Azure AD B2C, usare la procedura seguente nel portale di Azure.To get the b2clogin.com and login.microsoftonline.com endpoints supported by your Azure AD B2C tenant, use the following procedure in the Azure portal.

Iniziare selezionando uno dei flussi utente esistenti:

1. Passare al tenant di Azure AD B2C nel portale di [AzureNavigate](https://portal.azure.com) to your Azure AD B2C tenant in the Azure portal
1. In **Criteri**selezionare **Flussi utente (criteri)**
1. Selezionare un criterio esistente, ad esempio *B2C_1_signupsignin1*, quindi selezionare **Esegui flusso utente**
1. Nell'intestazione **Esegui flusso utente** nella parte superiore della pagina selezionare il collegamento ipertestuale per passare all'endpoint di individuazione OpenID Connect per tale flusso utente.

    ![Collegamento ipertestuale URI noto nella pagina Esegui ora del portale di Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. Nella pagina visualizzata nel browser, `issuer` registrare il valore, ad esempio:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Utilizzare l'elenco a discesa **Seleziona dominio** per selezionare l'altro dominio, quindi eseguire di nuovo i due passaggi precedenti e registrarne il `issuer` valore.

A questo punto dovrebbero essere registrati due URI simili a:You should now have two URIs recorded that are similar to:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Criteri personalizzati

Se si dispone di criteri personalizzati anziché di flussi utente, è possibile usare un processo simile per ottenere gli URI dell'autorità emittente.

1. Passare al tenant B2C di Azure AD
1. Selezionare **Framework dell'esperienza di gestione delle identità**
1. Selezionare uno dei criteri della relying party, ad esempio *B2C_1A_signup_signin*
1. Utilizzare l'elenco a discesa **Seleziona dominio** per selezionare un dominio, ad esempio *yourtenant.b2clogin.com*
1. Selezionare il collegamento ipertestuale visualizzato in Endpoint di **individuazione OpenID Connect**
1. Registrare `issuer` il valore
1. Eseguire i passaggi da 4 a 6 per l'altro dominio, ad esempio *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Scaricare il codice di esempio

Ora che si dispone di entrambi gli URI degli endpoint di token, è necessario aggiornare il codice per specificare che entrambi gli endpoint sono autorità di certificazione valide. Per esaminare un esempio, scaricare o clonare l'applicazione di esempio, quindi aggiornare l'esempio per supportare entrambi gli endpoint come autorità di certificazione valide.

Scarica l'archivio: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Abilitare più autorità di certificazione nell'API WebEnable multiple issuers in web API

In questa sezione viene aggiornato il codice per specificare che entrambi gli endpoint dell'autorità emittente di token sono validi.

1. Aprire la soluzione **B2C-WebAPI-DotNet.sln** in Visual Studio
1. Nel progetto **TaskService,** aprire il file *\\TaskService App_Start\\, Startup.Auth.cs*nell'editor
1. Aggiungere la `using` seguente direttiva all'inizio del file:

    `using System.Collections.Generic;`
1. Aggiungere [`ValidIssuers`][validissuers] la proprietà [`TokenValidationParameters`][tokenvalidationparameters] alla definizione e specificare entrambi gli URI registrati nella sezione precedente:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`viene fornito da MSAL.NET e viene utilizzato dal middleware OWIN nella sezione successiva di codice in *Startup.Auth.cs*. Dopo aver specificato più autorità di certificazione valide, la pipeline dell'applicazione OWIN viene informata del fatto che entrambi gli endpoint token sono autorità di certificazione valide.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Come accennato in precedenza, altre librerie OWIN forniscono in genere una funzionalità simile per il supporto di più emittenti. Anche se fornire esempi per ogni libreria non rientra nell'ambito di questo articolo, è possibile usare una tecnica simile per la maggior parte delle librerie.

## <a name="switch-endpoints-in-web-app"></a>Passare da un endpoint all'altro nell'app Web

Con entrambi gli URI ora supportati dall'API Web, è ora necessario aggiornare l'applicazione Web in modo che recuperi i token dall'endpoint b2clogin.com.

Ad esempio, è possibile configurare l'applicazione Web `ida:AadInstance` di esempio per l'utilizzo del nuovo endpoint modificando il valore nel file *TaskWebApp\\, Web.config*, del progetto **TaskWebApp.**

Modificare `ida:AadInstance` il valore nel *file Web.config* di `{your-b2c-tenant-name}.b2clogin.com` TaskWebApp in modo che vi faccia riferimento anziché `login.microsoftonline.com`.

Prima di:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Dopo (sostituire `{your-b2c-tenant}` con il nome del tenant B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Quando le stringhe dell'endpoint vengono costruite durante l'esecuzione dell'app Web, gli endpoint basati sul b2clogin.com vengono usati quando richiede i token.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato presentato un metodo di configurazione di un'API Web che implementa il middleware Microsoft OWIN (Katana) per accettare i token da più endpoint dell'autorità emittente. Come si può notare, sono presenti diverse altre stringhe nei file *Web.Config* di entrambi i progetti TaskService e TaskWebApp che dovrebbero essere modificati se si desidera compilare ed eseguire questi progetti sul proprio tenant. È possibile modificare i progetti in modo appropriato se si desidera vederli in azione, tuttavia, una procedura completa di questa operazione non rientra nell'ambito di questo articolo.

Per altre informazioni sui diversi tipi di token di sicurezza generati da Azure AD B2C, vedere Panoramica dei token in Azure Active Directory B2C.For more information about the different types of security tokens emitted by Azure AD B2C, see [Overview of tokens in Azure Active Directory B2C.](tokens-overview.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
