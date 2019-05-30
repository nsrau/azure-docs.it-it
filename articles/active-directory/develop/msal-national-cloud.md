---
title: Usare Microsoft Authentication Library (MSAL) nei cloud nazionali - piattaforma delle identità Microsoft
description: Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni di acquisire i token per chiamare le API web protetta. Queste API web può essere Microsoft Graph, altre APIs Microsoft, API web di partner o un'API web. MSAL supporta più architetture e piattaforme applicative.
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
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235273"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Usare MSAL in un ambiente cloud nazionale

[I cloud nazionali](authentication-national-cloud.md) sono isolate fisicamente le istanze di Azure. Queste aree di Azure consentono di verificare che i requisiti di conformità, sovranità e residenza dei dati vengono rispettati entro limiti geografici.

Oltre a Microsoft cloud in tutto il mondo, Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni nei cloud nazionali acquisire i token per autenticare e chiamare le API web protetta. Queste API web può essere Microsoft Graph o altre APIs Microsoft.

Tra cui il cloud globale, Azure Active Directory (Azure AD) viene distribuito nei cloud nazionali seguenti:  

- Azure Government
- 21Vianet per Azure Cina
- Azure Germania

Questa guida viene illustrato come accedere al lavoro e gli account dell'istituto di istruzione, ottenere un accesso token e chiamare l'API Microsoft Graph nel [cloud di Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/) ambiente.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi che siano soddisfatti questi prerequisiti.

### <a name="choose-the-appropriate-identities"></a>Scegliere le identità appropriate

[Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/) applicazioni possono usare le identità di Azure AD per enti pubblici e le identità pubblica di Azure AD per autenticare gli utenti. Poiché è possibile utilizzare uno qualsiasi di queste identità, è necessario decidere a quale endpoint dell'autorità è consigliabile scegliere per il proprio scenario:

- Pubblico di Azure AD: In genere utilizzato se l'organizzazione ha già un tenant Azure AD pubblica per il supporto di Office 365 (pubblico o GCC) o un'altra applicazione.
- Enti pubblici di Azure AD: In genere utilizzato se l'organizzazione ha un tenant di Azure AD per enti pubblici per il supporto di Office 365 (GCC High o DoD) già o è la creazione di un nuovo tenant in Azure AD per enti pubblici.

Dopo aver deciso, una considerazione speciale è in cui si esegue la registrazione dell'app. Se si sceglie le identità di Azure AD pubblica per l'applicazione di Azure per enti pubblici, è necessario registrare l'applicazione nel tenant di Azure AD pubblica.

### <a name="get-an-azure-government-subscription"></a>Ottenere una sottoscrizione di Azure per enti pubblici

Per ottenere una sottoscrizione di Azure per enti pubblici, vedere [la gestione e la connessione alla sottoscrizione di Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Se non hai una sottoscrizione di Azure per enti pubblici, creare un [account gratuito](https://azure.microsoft.com/global-infrastructure/government/request/) prima di iniziare.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.us/).
    
   Per trovare l'endpoint nel portale di Azure per altri cloud nazionali, vedere [endpoint di registrazione App](authentication-national-cloud.md#app-registration-endpoints).

1. Se l'account consente l'accesso a più di un tenant, selezionare il proprio account nell'angolo superiore destro e impostare la sessione del portale di Azure AD desiderato del tenant.
1. Andare alla [registrazioni per l'App](https://aka.ms/ra/ff) pagina su piattaforma delle identità Microsoft per gli sviluppatori.
1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
1. Sotto **tipi di account supportati**, selezionare **gli account in qualsiasi directory dell'organizzazione**.
1. Nel **URI di reindirizzamento** selezionare la **Web** piattaforma e impostare il valore per l'URL dell'applicazione basato sul server web. Vedere le sezioni successive per istruzioni su come impostare e ottenere l'URL di reindirizzamento in Visual Studio e il nodo.
1. Selezionare **Registra**.
1. Nella pagina **Panoramica**  dell'app prendere nota del valore del campo **ID applicazione (client)** .
1. Questa esercitazione è necessario abilitare la [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md). Nel riquadro a sinistra dell'applicazione registrato, selezionare **autenticazione**.
1. Nelle **impostazioni avanzate**, in **concessione implicita**, selezionare il **i token ID** e **i token di accesso** caselle di controllo. I token ID e token di accesso sono necessari perché questa app deve accedere gli utenti e chiamare un'API.
1. Selezionare **Salva**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passaggio 2:  Configurare il server web o di un progetto

- [Scaricare i file di progetto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) per un server web locale, ad esempio nodo.

  oppure

- [Scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Andare al [configurare la SPA di JavaScript](#step-4-configure-your-javascript-spa) per configurare l'esempio di codice prima dell'esecuzione.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Passaggio 3: Usare Microsoft Authentication Library per l'accesso dell'utente

Seguire i passaggi nel [JavaScript esercitazione](tutorial-v2-javascript-spa.md#create-your-project) per creare il progetto e integrare con MSAL per l'accesso dell'utente.

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

In tale codice:

- `Enter_the_Application_Id_here` è il **ID applicazione (client)** valore per l'applicazione registrata.
- `Enter_the_Tenant_Info_Here` è impostato su una delle opzioni seguenti:
    - Se l'applicazione supporta **gli account in questa directory dell'organizzazione**, sostituire questo valore con l'ID tenant o nome (ad esempio, contoso.microsoft.com) del tenant.
    - Se l'applicazione supporta **gli account in qualsiasi directory dell'organizzazione**, sostituire questo valore con `organizations`.
    
    Per trovare gli endpoint di autenticazione per tutti i cloud nazionali, vedere [endpoint di autenticazione di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Gli account Microsoft personali non sono supportati nei cloud nazionali.
  
- `graphEndpoint` è l'endpoint di Microsoft Graph per il cloud Microsoft per enti pubblici degli Stati Uniti.

   Per trovare gli endpoint di Microsoft Graph per tutti i cloud nazionali, vedere [endpoint di Microsoft Graph nei cloud nazionali](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

È possibile usare MSAL.NET per consentire agli utenti di acquisire i token e chiamare l'API Microsoft Graph nei cloud nazionali.

Le esercitazioni seguenti illustrano come compilare un'app Web MVC 2.2 di .NET Core. L'app Usa OpenID Connect per consentire agli utenti con un account aziendali e dell'istituto di istruzione in un'organizzazione che appartiene a un cloud nazionale.

- Per acquisire i token di accesso degli utenti, seguire [in questa esercitazione](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Per chiamare l'API Microsoft Graph, seguire [in questa esercitazione](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet per Azure Cina](https://docs.microsoft.com/azure/china/)
- [Azure Germania](https://docs.microsoft.com/azure/germany/)