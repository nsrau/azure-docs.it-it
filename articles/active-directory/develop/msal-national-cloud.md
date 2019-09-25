---
title: Usare Microsoft Authentication Library (MSAL) nei cloud nazionali-piattaforma di identità Microsoft
description: Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni di acquisire token per chiamare le API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web dei partner o API Web. MSAL supporta più architetture e piattaforme applicative.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97855a52831a63a92a46bd0d25d23ba3fc91a07b
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268574"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Usare MSAL in un ambiente cloud nazionale

I [cloud nazionali](authentication-national-cloud.md) sono istanze fisicamente isolate di Azure. Queste aree di Azure contribuiscono a garantire che i requisiti di residenza dei dati, sovranità e conformità vengano rispettati entro i limiti geografici.

Oltre al cloud Microsoft nel mondo, Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni nei cloud nazionali di acquisire token per autenticare e chiamare API Web protette. Queste API Web possono essere Microsoft Graph o altre API Microsoft.

Incluso il cloud globale, Azure Active Directory (Azure AD) viene distribuito nei cloud nazionali seguenti:  

- Azure Government
- 21Vianet per Azure Cina
- Azure Germania

Questa guida illustra come accedere agli account aziendali e dell'Istituto di istruzione, ottenere un token di accesso e chiamare l'API Microsoft Graph nell'ambiente [cloud di Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/) .

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che siano soddisfatti questi prerequisiti.

### <a name="choose-the-appropriate-identities"></a>Scegliere le identità appropriate

Le applicazioni di [Azure per enti](https://docs.microsoft.com/azure/azure-government/) pubblici possono usare Azure ad identità governative e Azure ad identità pubbliche per autenticare gli utenti. Poiché è possibile usare una di queste identità, è necessario decidere quale endpoint dell'autorità scegliere per lo scenario:

- Azure AD pubblico: Usato comunemente se l'organizzazione dispone già di un tenant pubblico Azure AD per supportare Office 365 (pubblico o GCC) o un'altra applicazione.
- Azure AD per enti pubblici: Comunemente utilizzato se l'organizzazione dispone già di un tenant di Azure AD Government per supportare Office 365 (GCC High o DoD) o sta creando un nuovo tenant in Azure AD Government.

Una volta deciso, una particolare considerazione è la posizione in cui si esegue la registrazione dell'app. Se si sceglie Azure AD identità pubbliche per l'applicazione Azure per enti pubblici, è necessario registrare l'applicazione nel tenant pubblico di Azure AD.

### <a name="get-an-azure-government-subscription"></a>Ottenere una sottoscrizione di Azure per enti pubblici

Per ottenere una sottoscrizione di Azure per enti pubblici, vedere [gestione e connessione alla sottoscrizione in Azure per enti](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)pubblici.

Se non si ha una sottoscrizione di Azure per enti pubblici, creare un [account gratuito](https://azure.microsoft.com/global-infrastructure/government/request/) prima di iniziare.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Passaggio 1: Registra l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.us/).
    
   Per trovare portale di Azure endpoint per altri cloud nazionali, vedere [endpoint di registrazione delle app](authentication-national-cloud.md#app-registration-endpoints).

1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Passare alla pagina [registrazioni app](https://aka.ms/ra/ff) della piattaforma Microsoft Identity per sviluppatori.
1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
1. In **tipi di account supportati**selezionare **account in qualsiasi directory dell'organizzazione**.
1. Nella sezione **URI di reindirizzamento** selezionare la piattaforma **Web** e impostare il valore sull'URL dell'applicazione in base al server Web. Vedere le sezioni successive per istruzioni su come impostare e ottenere l'URL di reindirizzamento in Visual Studio e nel nodo.
1. Selezionare **Registra**.
1. Nella pagina **Panoramica**  dell'app prendere nota del valore del campo **ID applicazione (client)** .
1. Per questa esercitazione è necessario abilitare il [flusso di concessione implicito](v2-oauth2-implicit-grant-flow.md). Nel riquadro sinistro dell'applicazione registrata selezionare **Autenticazione**.
1. In **Impostazioni avanzate**, in **Concessione implicita**, selezionare entrambe le caselle di controllo **Token ID** e **Token di accesso**. I token ID e i token di accesso sono necessari perché l'app deve eseguire l'accesso degli utenti e chiamare un'API.
1. Selezionare **Salva**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passaggio 2:  Impostare il server Web o il progetto

- [Scaricare i file di progetto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) per un server Web locale, ad esempio node.

  oppure

- [Scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Ignorare quindi la [configurazione di JavaScript Spa](#step-4-configure-your-javascript-spa) per configurare il codice di esempio prima di eseguirlo.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Passaggio 3: Usare Microsoft Authentication Library per accedere all'utente

Seguire i passaggi nell' [esercitazione su JavaScript](tutorial-v2-javascript-spa.md#create-your-project) per creare il progetto e integrarsi con MSAL per accedere all'utente.

### <a name="step-4-configure-your-javascript-spa"></a>Passaggio 4: Configurare JavaScript SPA

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

Nel codice:

- `Enter_the_Application_Id_here`è il valore ID dell'applicazione **(client)** per l'applicazione registrata.
- `Enter_the_Tenant_Info_Here`è impostato su una delle opzioni seguenti:
    - Se l'applicazione supporta gli **account in questa directory aziendale**, sostituire questo valore con l'ID tenant o il nome del tenant (ad esempio, contoso.Microsoft.com).
    - Se l'applicazione supporta gli **account in qualsiasi directory organizzativa**, sostituire questo valore `organizations`con.
    
    Per trovare gli endpoint di autenticazione per tutti i cloud nazionali, vedere [Azure ad endpoint di autenticazione](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Gli account Microsoft personali non sono supportati nei cloud nazionali.
  
- `graphEndpoint`è l'endpoint Microsoft Graph per Microsoft Cloud per il governo degli Stati Uniti.

   Per trovare endpoint Microsoft Graph per tutti i cloud nazionali, vedere [Microsoft Graph endpoint nei cloud nazionali](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

È possibile usare MSAL.NET per accedere agli utenti, acquisire i token e chiamare l'API Microsoft Graph nei cloud nazionali.

Le esercitazioni seguenti illustrano come compilare un'app Web MVC .NET Core 2,2. L'app usa OpenID Connect per accedere agli utenti con un account aziendale o dell'Istituto di istruzione in un'organizzazione che appartiene a un cloud nazionale.

- Per accedere agli utenti e acquisire i token, seguire [questa esercitazione](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Per chiamare l'API Microsoft Graph, seguire [questa esercitazione](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="msal-for-ios-and-macos"></a>MSAL per iOS e macOS

È possibile usare MSAL per iOS e macOS per acquisire i token nei cloud nazionali, ma è necessaria una configurazione aggiuntiva `MSALPublicClientApplication`durante la creazione.

Ad esempio, se si vuole che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale (in questo governo degli Stati Uniti), è possibile scrivere:

Objective-C:

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

Swift

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure Cina 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germania](https://docs.microsoft.com/azure/germany/)
