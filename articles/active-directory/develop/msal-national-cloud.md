---
title: Uso di MSAL in un'app cloud nazionale Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni di acquisire token per chiamare API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web partner o la propria API Web. MSAL supporta più architetture e piattaforme applicative.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f3bb4dd1c564e5f6c4a8ee1bb5bf7424a74a339e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533990"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Utilizzare MSAL in un ambiente cloud nazionaleUse MSAL in a national cloud environment

[Le nubi nazionali,](authentication-national-cloud.md)note anche come nuvole sovrane, sono istanze fisicamente isolate di Azure.National clouds , also known as Sovereign clouds, are physically isolated instances of Azure. Queste aree di Azure consentono di verificare che i requisiti di residenza, sovranità e conformità dei dati vengano rispettati entro i limiti geografici.

Oltre al cloud Microsoft in tutto il mondo, Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni nei cloud nazionali di acquisire token per autenticare e chiamare LE API Web protette. Queste API Web possono essere Microsoft Graph o altre API Microsoft.These web APIs can be Microsoft Graph or other Microsoft APIs.

Includendo il cloud globale, Azure Active Directory (Azure AD) viene distribuito nei cloud nazionali seguenti:Including the global cloud, Azure Active Directory (Azure AD) is deployed in the following national clouds:  

- Azure Government
- 21Vianet per Azure Cina
- Azure Germania

Questa guida illustra come accedere agli account aziendali e dell'istituto di istruzione, ottenere un token di accesso e chiamare l'API Microsoft Graph nell'ambiente [cloud di Azure per enti pubblici.](https://azure.microsoft.com/global-infrastructure/government/)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di soddisfare questi prerequisiti.

### <a name="choose-the-appropriate-identities"></a>Scegliere le identità appropriate

[Le](https://docs.microsoft.com/azure/azure-government/) applicazioni di Azure per enti per enti pubblici possono usare le identità di Azure AD per enti pubblici e le identità pubbliche di Azure AD per autenticare gli utenti. Poiché è possibile usare una di queste identità, è necessario decidere quale endpoint dell'autorità scegliere per lo scenario:Because you can use any of these identities, you need to decide which authority endpoint you should choose for your scenario:

- Azure AD Pubblico: comunemente usato se l'organizzazione dispone già di un tenant pubblico di Azure AD per supportare Office 365 (pubblico o GCC) o un'altra applicazione.
- Azure AD per enti pubblici: comunemente usato se l'organizzazione dispone già di un tenant di Azure AD per enti pubblici per supportare Office 365 (GCC High o DoD) o sta creando un nuovo tenant in Azure AD per enti pubblici.

Dopo aver deciso, una considerazione speciale è dove si esegue la registrazione dell'app. Se si scelgono le identità pubbliche di Azure AD per l'applicazione Azure per enti pubblici, è necessario registrare l'applicazione nel tenant pubblico di Azure AD.

### <a name="get-an-azure-government-subscription"></a>Ottenere una sottoscrizione di Azure per enti pubbliciGet an Azure Government subscription

Per ottenere una sottoscrizione di Azure per enti pubblici, vedere [Gestione e connessione alla sottoscrizione in Azure per enti pubblici.](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)

Se non si ha una sottoscrizione di Azure per enti pubblici, creare un [account gratuito](https://azure.microsoft.com/global-infrastructure/government/request/) prima di iniziare.

Per informazioni dettagliate sull'uso di un cloud nazionale con un particolare linguaggio di programmazione, scegliere la scheda corrispondente al linguaggio:

## <a name="net"></a>[.NET](#tab/donet)

Puoi usare MSAL.NET per accedere agli utenti, acquisire token e chiamare l'API Microsoft Graph nei cloud nazionali.

Le esercitazioni seguenti illustrano come compilare un'app Web MVC .NET Core 2.2. L'app usa OpenID Connect per accedere agli utenti con un account aziendale e dell'istituto di istruzione in un'organizzazione appartenente a un cloud nazionale.

- Per accedere agli utenti e acquisire token, seguire questa esercitazione: [Creare un'app Web di ASP.NET utenti che accedono in cloud sovrani con la piattaforma di identità Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Per chiamare l'API Microsoft Graph, seguire questa esercitazione: Utilizzo della piattaforma Microsoft [Identity per chiamare l'API Microsoft Graph da un'app Web di ASP.NET Core 2.x, per conto di un utente che accede utilizzando l'account aziendale e dell'istituto](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)di istruzione in Microsoft National Cloud .

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Per abilitare l'applicazione MSAL.js per i cloud sovrani:

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.us/).

   Per trovare gli endpoint del portale di Azure per altri cloud nazionali, vedere Endpoint di [registrazione app.](authentication-national-cloud.md#app-registration-endpoints)

1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo superiore destro e impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Passare alla pagina [Registrazioni app](https://aka.ms/ra/ff) nella piattaforma di identità Microsoft per gli sviluppatori.
1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
1. In **Tipi di account supportati**selezionare Account in qualsiasi directory **dell'organizzazione.**
1. Nella sezione **URI di reindirizzamento** selezionare la piattaforma **Web** e impostare il valore sull'URL dell'applicazione in base al server Web. Vedere le sezioni successive per istruzioni su come impostare e ottenere l'URL di reindirizzamento in Visual Studio e Node.See the next sections for instructions on how to set and obtain the redirect URL in Visual Studio and Node.
1. Selezionare **Registra**.
1. Nella pagina **Panoramica ** dell'app prendere nota del valore del campo **ID applicazione (client)**.
1. Questa esercitazione richiede l'abilitazione del flusso di [concessione implicita.](v2-oauth2-implicit-grant-flow.md) Nel riquadro sinistro dell'applicazione registrata selezionare **Autenticazione**.
1. In **Impostazioni avanzate**, in **Concessione implicita**, selezionare entrambe le caselle di controllo **Token ID** e **Token di accesso**. I token ID e i token di accesso sono necessari perché questa app deve accedere agli utenti e chiamare un'API.
1. Selezionare **Salva**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passaggio 2: Configurare il server Web o il progetto

- [Scaricare i file di progetto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) per un server Web locale, ad esempio Nodo.

  o

- [Scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Passare quindi a [Configurare la SPA JavaScript](#step-4-configure-your-javascript-spa) per configurare l'esempio di codice prima di eseguirlo.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Passaggio 3: Utilizzare la libreria di autenticazione Microsoft per accedere all'utente

Seguire i passaggi [nell'esercitazione su JavaScript](tutorial-v2-javascript-spa.md#create-your-project) per creare il progetto e integrarsi con MSAL per accedere all'utente.

### <a name="step-4-configure-your-javascript-spa"></a>Passaggio 4: Configurare JavaScript SPAStep 4: Configure your JavaScript SPA

Nel file `index.html` creato durante la configurazione del progetto aggiungere le informazioni di registrazione dell'applicazione. Aggiungere il codice seguente all'inizio tra i tag `<script></script>` nel corpo del file `index.html`:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

In tale codice:

- `Enter_the_Application_Id_here`è il valore **ID applicazione (client)** per l'applicazione registrata.
- `Enter_the_Tenant_Info_Here`è impostato su una delle seguenti opzioni:
    - Se l'applicazione supporta **Account in questa directory dell'organizzazione,** sostituire questo valore con l'ID tenant o il nome del tenant, ad esempio contoso.microsoft.com.
    - Se l'applicazione supporta Accounts in qualsiasi directory `organizations` **dell'organizzazione,** sostituire questo valore con .

    Per trovare gli endpoint di autenticazione per tutti i cloud nazionali, vedere Endpoint di autenticazione di [Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

    > [!NOTE]
    > Gli account Microsoft personali non sono supportati nei cloud nazionali.

- `graphEndpoint`è l'endpoint Microsoft Graph per il cloud Microsoft per gli enti statunitensi.

   Per trovare gli endpoint di Microsoft Graph per tutti i cloud nazionali, vedere [Endpoint di Microsoft Graph nei cloud nazionali.](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="python"></a>[Python](#tab/python)

Per abilitare l'applicazione MSAL Python per i cloud sovrani:

- Registrare l'applicazione in un portale specifico, a seconda del cloud. Per altre informazioni su come scegliere il portale, fare riferimento agli endpoint di registrazione delle appFor more information on how to choose the portal refer [App registration endpoints](authentication-national-cloud.md#app-registration-endpoints)
- Usare uno degli [esempi](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) del repository con alcune modifiche alla configurazione, a seconda del cloud, menzionato successivamente.
- Usare un'autorità specifica, a seconda del cloud in cui è stata registrata l'applicazione. Per altre informazioni sulle autorità per cloud diversi, vedere Endpoint di autenticazione di [Azure AD.](authentication-national-cloud.md#azure-ad-authentication-endpoints)

    Di seguito è riportato un esempio di autorità:Here's an example authority:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Per chiamare Microsoft Graph è necessario un URL di endpoint Graph specifico che dipende dal cloud in uso. Per trovare gli endpoint di Microsoft Graph per tutti i cloud nazionali, fare riferimento agli endpoint radice del [servizio Microsoft Graph ed Graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Ecco un esempio di endpoint grafico, con ambito:Here's an example of a graph endpoint, with scope:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Per abilitare l'applicazione MSAL per Java per i cloud sovrani:

- Registrare l'applicazione in un portale specifico, a seconda del cloud. Per altre informazioni su come scegliere il portale, fare riferimento agli endpoint di registrazione delle appFor more information on how to choose the portal refer [App registration endpoints](authentication-national-cloud.md#app-registration-endpoints)
- Usare uno degli [esempi](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) del repository con alcune modifiche alla configurazione, a seconda del cloud, che vengono menzionati di seguito.
- Usare un'autorità specifica, a seconda del cloud in cui è stata registrata l'applicazione. Per altre informazioni sulle autorità per cloud diversi, vedere Endpoint di autenticazione di [Azure AD.](authentication-national-cloud.md#azure-ad-authentication-endpoints)

Di seguito è riportato un esempio di autorità:Here's an example authority:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Per chiamare Microsoft Graph è necessario un URL di endpoint Graph specifico che dipende dal cloud in uso. Per trovare gli endpoint di Microsoft Graph per tutti i cloud nazionali, fare riferimento agli endpoint radice del [servizio Microsoft Graph ed Graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Ecco un esempio di endpoint grafico, con ambito:Here's an example of a graph endpoint, with scope:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL per iOS e macOS può essere utilizzato per acquisire token nei `MSALPublicClientApplication`cloud nazionali, ma richiede una configurazione aggiuntiva durante la creazione di file .

Ad esempio, se si desidera che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale (qui il governo degli Stati Uniti), è possibile scrivere:For instance, if you want your application to be a multi-tenant application in a national cloud (here US Government), you could write:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

MSAL per iOS e macOS può essere utilizzato per acquisire token nei `MSALPublicClientApplication`cloud nazionali, ma richiede una configurazione aggiuntiva durante la creazione di file .

Ad esempio, se si desidera che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale (qui il governo degli Stati Uniti), è possibile scrivere:For instance, if you want your application to be a multi-tenant application in a national cloud (here US Government), you could write:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Autenticazione nelle cloud nazionali](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet per Azure Cina](https://docs.microsoft.com/azure/china/)
- [Azure Germania](https://docs.microsoft.com/azure/germany/)
