---
title: Supportare più autorità emittenti di token in un'applicazione Web basata su OWIN-Azure Active Directory B2C
description: Informazioni su come abilitare un'applicazione Web .NET per supportare i token rilasciati da più domini.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 31ab19b8b3adbef1f0ea573af13b98750d278db8
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716739"
---
# <a name="support-multiple-token-issuers-in-an-owin-based-web-application"></a>Supportare più autorità emittenti di token in un'applicazione Web basata su OWIN

Questo articolo descrive una tecnica per l'abilitazione del supporto per più autorità emittenti di token in app Web e API che implementano [Open Web Interface for .NET (OWIN)](http://owin.org/). Il supporto di più endpoint token è utile quando si esegue la migrazione di applicazioni B2C Azure Active Directory (Azure AD) da *login.microsoftonline.com* a *b2clogin.com*.

Le sezioni seguenti presentano un esempio di come abilitare più autorità di certificazione in un'applicazione Web e l'API Web corrispondente che usano i componenti del middleware [Microsoft OWIN][katana] (Katana). Sebbene gli esempi di codice siano specifici del middleware Microsoft OWIN, la tecnica generale dovrebbe essere applicabile ad altre librerie OWIN.

> [!NOTE]
> Questo articolo è destinato a Azure ad B2C clienti con applicazioni attualmente distribuite che `login.microsoftonline.com` fanno riferimento a e che desiderano eseguire `b2clogin.com` la migrazione all'endpoint consigliato. Se si sta configurando una nuova applicazione, usare [b2clogin.com](b2clogin.md) come indicato.

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con i passaggi descritti in questo articolo, è necessario disporre delle risorse Azure AD B2C seguenti:

* [Flussi utente](tutorial-create-user-flows.md) o [criteri personalizzati](active-directory-b2c-get-started-custom.md) creati nel tenant

## <a name="get-token-issuer-endpoints"></a>Ottenere gli endpoint dell'emittente del token

Prima di tutto è necessario ottenere gli URI dell'endpoint dell'emittente di token per ogni emittente che si desidera supportare nell'applicazione. Per ottenere gli endpoint *b2clogin.com* e *login.microsoftonline.com* supportati dal tenant di Azure ad B2C, usare la procedura seguente nel portale di Azure.

Per iniziare, selezionare uno dei flussi utente esistenti:

1. Passare al tenant di Azure AD B2C nel [portale di Azure](https://portal.azure.com)
1. In **criteri**selezionare **flussi utente (criteri)**
1. Selezionare un criterio esistente, ad esempio *B2C_1_signupsignin1*, quindi selezionare **Esegui flusso utente**
1. Nell'intestazione **Esegui flusso utente** nella parte superiore della pagina selezionare il collegamento ipertestuale per passare all'endpoint di individuazione di OpenID Connect per quel flusso utente.

    ![Collegamento ipertestuale URI noto nella pagina Esegui ora della portale di Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. Nella pagina visualizzata nel browser registrare il `issuer` valore, ad esempio:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Usare l'elenco a discesa **Seleziona dominio** per selezionare l'altro dominio, quindi eseguire di nuovo i due passaggi precedenti e registrare il `issuer` relativo valore.

A questo punto dovrebbero essere registrati due URI simili ai seguenti:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Criteri personalizzati

Se sono presenti criteri personalizzati anziché flussi utente, è possibile usare un processo simile per ottenere gli URI dell'autorità emittente.

1. Passare al tenant di Azure AD B2C
1. Selezionare **Framework dell'esperienza di gestione delle identità**
1. Selezionare uno dei criteri di relying party, ad esempio *B2C_1A_signup_signin*
1. Usare l'elenco a discesa **Seleziona dominio** per selezionare un dominio, ad esempio *yourtenant.b2clogin.com*
1. Selezionare il collegamento ipertestuale visualizzato nell' **endpoint di individuazione di OpenID Connect**
1. Registrare il `issuer` valore
1. Eseguire i passaggi 4-6 per l'altro dominio, ad esempio *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Scaricare il codice di esempio

Ora che si dispone di entrambi gli URI dell'endpoint del token, è necessario aggiornare il codice per specificare che entrambi gli endpoint sono emittenti valide. Per esaminare un esempio, scaricare o clonare l'applicazione di esempio, quindi aggiornare l'esempio per supportare entrambi gli endpoint come autorità emittenti valide.

Scaricare l'archivio: [Active-Directory-B2C-DotNet-webapp-and-WebAPI-master. zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Abilitare più autorità emittenti nell'API Web

In questa sezione viene aggiornato il codice per specificare che entrambi gli endpoint dell'emittente del token sono validi.

1. Aprire la soluzione **B2C-WebAPI-dotnet. sln** in Visual Studio
1. Nel progetto **TaskService** aprire il file *TaskService\\app_start\\* * Startup.auth.cs** * nell'editor
1. Aggiungere la direttiva `using` seguente all'inizio del file:

    `using System.Collections.Generic;`
1. Aggiungere la [`ValidIssuers`][validissuers] proprietà [`TokenValidationParameters`][tokenvalidationparameters] alla definizione e specificare entrambi gli URI registrati nella sezione precedente:

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

`TokenValidationParameters`viene fornito da MSAL.NET e viene utilizzato dal middleware OWIN nella sezione successiva del codice in *Startup.auth.cs*. Se sono state specificate più autorità emittenti valide, la pipeline dell'applicazione OWIN viene resa presente che entrambi gli endpoint del token sono emittenti validi.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Come indicato in precedenza, altre librerie OWIN in genere forniscono una funzionalità simile per supportare più autorità emittenti. Sebbene la fornitura di esempi per ogni libreria esula dall'ambito di questo articolo, è possibile usare una tecnica simile per la maggior parte delle librerie.

## <a name="switch-endpoints-in-web-app"></a>Scambia gli endpoint nell'app Web

Con entrambi gli URI ora supportati dall'API Web, è ora necessario aggiornare l'applicazione Web in modo che recuperi i token dall'endpoint b2clogin.com.

Ad esempio, è possibile configurare l'applicazione Web di esempio in modo che usi il nuovo endpoint `ida:AadInstance` modificando il valore nel file *TaskWebApp\\* * Web. config** * del progetto **TaskWebApp** .

Modificare il `ida:AadInstance` valore nel *file Web. config* di TaskWebApp in modo che faccia `{your-b2c-tenant-name}.b2clogin.com` riferimento al `login.microsoftonline.com`posto di.

Prima:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Dopo (sostituire `{your-b2c-tenant}` con il nome del tenant B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Quando le stringhe dell'endpoint vengono costruite durante l'esecuzione dell'app Web, gli endpoint basati su b2clogin.com vengono usati per la richiesta di token.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha presentato un metodo di configurazione di un'API Web che implementa il middleware Microsoft OWIN (Katana) per accettare i token da più endpoint dell'autorità emittente. Come si può notare, esistono diverse altre stringhe nei file *Web. config* dei progetti TaskService e TaskWebApp che devono essere modificate se si vuole compilare ed eseguire questi progetti nel proprio tenant. È possibile modificare i progetti in modo appropriato se si desidera visualizzarli in azione. Tuttavia, una procedura dettagliata completa di questa operazione esula dall'ambito di questo articolo.

Per ulteriori informazioni sui diversi tipi di token di sicurezza emessi da Azure AD B2C, vedere [Cenni preliminari sui token in Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
