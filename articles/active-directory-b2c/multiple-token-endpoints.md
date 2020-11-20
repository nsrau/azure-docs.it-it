---
title: Eseguire la migrazione di API Web basate su OWIN in b2clogin.com
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare un'API Web .NET per supportare i token rilasciati da più autorità di certificazione durante la migrazione delle applicazioni a b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c362ce256259606c85af0a7e13ccde1715bb012b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953934"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Eseguire la migrazione di un'API Web basata su OWIN in b2clogin.com

Questo articolo descrive una tecnica per l'abilitazione del supporto per più emittenti di token in API Web che implementano l' [interfaccia Web aperta per .NET (OWIN)](http://owin.org/). Il supporto di più endpoint token è utile quando si esegue la migrazione di API Azure Active Directory B2C (Azure AD B2C) e delle relative applicazioni da *login.microsoftonline.com* a *b2clogin.com*.

Aggiungendo il supporto nell'API per accettare i token rilasciati da b2clogin.com e login.microsoftonline.com, è possibile eseguire la migrazione delle applicazioni Web in modalità di gestione temporanea prima di rimuovere il supporto per i token rilasciati da login.microsoftonline.com dall'API.

Le sezioni seguenti presentano un esempio di come abilitare più autorità di certificazione in un'API Web che usa i componenti del middleware [Microsoft OWIN][katana] (Katana). Sebbene gli esempi di codice siano specifici del middleware Microsoft OWIN, la tecnica generale dovrebbe essere applicabile ad altre librerie OWIN.

> [!NOTE]
> Questo articolo è destinato a Azure AD B2C clienti con API e applicazioni attualmente distribuite che fanno riferimento a `login.microsoftonline.com` e che desiderano eseguire la migrazione all' `b2clogin.com` endpoint consigliato. Se si sta configurando una nuova applicazione, usare [b2clogin.com](b2clogin.md) come indicato.

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con i passaggi descritti in questo articolo, è necessario disporre delle risorse Azure AD B2C seguenti:

* [Flussi utente](tutorial-create-user-flows.md) o [criteri personalizzati](custom-policy-get-started.md) creati nel tenant

## <a name="get-token-issuer-endpoints"></a>Ottenere gli endpoint dell'emittente del token

È prima di tutto necessario ottenere gli URI dell'endpoint dell'emittente del token per ogni emittente che si vuole supportare nell'API. Per ottenere gli endpoint *b2clogin.com* e *login.microsoftonline.com* supportati dal tenant di Azure ad B2C, usare la procedura seguente nel portale di Azure.

Per iniziare, selezionare uno dei flussi utente esistenti:

1. Passare al tenant di Azure AD B2C nel [portale di Azure](https://portal.azure.com)
1. In **criteri** selezionare **flussi utente (criteri)**
1. Selezionare un criterio esistente, ad esempio *B2C_1_signupsignin1*, quindi selezionare **Esegui flusso utente**
1. Nell'intestazione **Esegui flusso utente** nella parte superiore della pagina selezionare il collegamento ipertestuale per passare all'endpoint di individuazione di OpenID Connect per quel flusso utente.

    ![Collegamento ipertestuale URI well-known nella pagina Esegui adesso del portale di Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. Nella pagina visualizzata nel browser registrare il valore di `issuer`, ad esempio:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Usare l'elenco a discesa **Seleziona dominio** per selezionare l'altro dominio, quindi eseguire di nuovo i due passaggi precedenti e registrare il relativo `issuer` valore.

A questo punto dovrebbero essere registrati due URI simili ai seguenti:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Criteri personalizzati

Se sono presenti criteri personalizzati anziché flussi utente, è possibile usare un processo simile per ottenere gli URI dell'autorità emittente.

1. Passare al tenant di Azure AD B2C
1. Selezionare il **Framework dell'esperienza di identità**
1. Selezionare uno dei criteri di relying party, ad esempio *B2C_1A_signup_signin*
1. Usare l'elenco a discesa **Seleziona dominio** per selezionare un dominio, ad esempio *yourtenant.b2clogin.com*
1. Selezionare il collegamento ipertestuale visualizzato nell' **endpoint di individuazione di OpenID Connect**
1. Registrare il `issuer` valore
1. Eseguire i passaggi 4-6 per l'altro dominio, ad esempio *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Scaricare il codice di esempio

Ora che si dispone di entrambi gli URI dell'endpoint del token, è necessario aggiornare il codice per specificare che entrambi gli endpoint sono emittenti valide. Per esaminare un esempio, scaricare o clonare l'applicazione di esempio, quindi aggiornare l'esempio per supportare entrambi gli endpoint come autorità emittenti valide.

Scaricare l'archivio: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Abilitare più autorità emittenti nell'API Web

In questa sezione viene aggiornato il codice per specificare che entrambi gli endpoint dell'emittente del token sono validi.

1. Aprire la soluzione **B2C-WebAPI-dotnet. sln** in Visual Studio
1. Nel progetto **TaskService** aprire il file * TaskService \\ app_start \\ **Startup.auth.cs** _ nell'editor
1. Aggiungere la `using` direttiva seguente all'inizio del file:

    `using System.Collections.Generic;`
1. Aggiungere la [`ValidIssuers`][validissuers] proprietà alla [`TokenValidationParameters`][tokenvalidationparameters] definizione e specificare entrambi gli URI registrati nella sezione precedente:

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

`TokenValidationParameters` viene fornito da MSAL.NET e viene utilizzato dal middleware OWIN nella sezione successiva del codice in _Startup. auth. cs *. Se sono state specificate più autorità emittenti valide, la pipeline dell'applicazione OWIN viene resa presente che entrambi gli endpoint del token sono emittenti validi.

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

Ad esempio, è possibile configurare l'applicazione Web di esempio in modo che usi il nuovo endpoint modificando il `ida:AadInstance` valore nel *file TaskWebApp \\ **Web.config** _ del progetto _* TaskWebApp * *.

Modificare il `ida:AadInstance` valore nel *Web.config* di TaskWebApp in modo che faccia riferimento `{your-b2c-tenant-name}.b2clogin.com` al posto di `login.microsoftonline.com` .

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

Quando le stringhe dell'endpoint vengono costruite durante l'esecuzione dell'app Web, gli endpoint basati su b2clogin.com vengono usati per la richiesta di token.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha presentato un metodo di configurazione di un'API Web che implementa il middleware Microsoft OWIN (Katana) per accettare i token da più endpoint dell'autorità emittente. Come si può notare, esistono diverse altre stringhe nei file di *Web.Config* dei progetti TaskService e TaskWebApp che devono essere modificati se si vuole compilare ed eseguire questi progetti nel proprio tenant. È possibile modificare i progetti in modo appropriato se si desidera visualizzarli in azione. Tuttavia, una procedura dettagliata completa di questa operazione esula dall'ambito di questo articolo.

Per ulteriori informazioni sui diversi tipi di token di sicurezza emessi da Azure AD B2C, vedere [Cenni preliminari sui token in Azure Active Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters