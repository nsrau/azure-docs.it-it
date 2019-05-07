---
title: Usare Microsoft Authentication Library (MSAL) nei cloud nazionali - piattaforma delle identità Microsoft
description: Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni di acquisire i token per chiamare le API web protetta. Queste API web può essere Microsoft Graph, altre APIS Microsoft, API web di terze parti o un'API web. MSAL supporta anche più architetture applicative e piattaforme.
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
ms.openlocfilehash: 18eccd6b6d29a43cc3fa13d133d449bf0b9be657
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075791"
---
# <a name="use-msal-in-national-cloud-environment"></a>Usare MSAL nell'ambiente cloud nazionale

[I cloud nazionali](authentication-national-cloud.md) sono isolate fisicamente le istanze di Azure. Queste aree di Azure sono progettate in modo da garantire il rispetto dei requisiti di residenza, sovranità e conformità dei dati all'interno dei confini geografici.

Oltre a Microsoft cloud in tutto il mondo, Microsoft Authentication Library (MSAL) consente inoltre agli sviluppatori di applicazioni nei cloud nazionali acquisire i token per autenticare e chiamare le API web protetta. Queste API web può essere Microsoft Graph o altre APIS Microsoft.

Tra cui cloud globale, Azure Active Directory (Azure AD) viene distribuito nei cloud nazionali seguenti:  

- Azure per enti pubblici statunitensi
- 21Vianet per Azure Cina
- Azure Germania

Questa guida viene illustrato come accedere gli account aziendali e dell'istituto di istruzione, ottenere un accesso token e chiamare l'API Microsoft Graph nel [cloud Microsoft per il governo degli Stati Uniti](https://azure.microsoft.com/global-infrastructure/government/) ambiente.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi che siano soddisfatti questi prerequisiti.

### <a name="choose-the-appropriate-identities"></a>Scegliere le identità appropriate

[Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/) applicazioni possono usare le identità di Azure AD per enti pubblici, nonché identità pubblica di Azure AD per autenticare gli utenti. Poiché è possibile usare uno qualsiasi di queste identità, è necessario comprendere e decidere quali endpoint dell'autorità è consigliabile scegliere per il proprio scenario:

- Pubblico di Azure AD: In genere utilizzato se l'organizzazione ha già un tenant Azure AD pubblica per il supporto di Office 365 (pubblico o GCC) o un'altra applicazione.
- Enti pubblici di Azure AD: In genere utilizzato se l'organizzazione ha un tenant di Azure AD per enti pubblici per il supporto di Office 365 (GCC High o DoD) già o crea un nuovo tenant in Azure AD per enti pubblici.

Dopo aver stabilito questo, la considerazione speciale è in cui si esegue la registrazione dell'app. Se si sceglie le identità di Azure AD pubblica per l'applicazione di Azure per enti pubblici, è necessario registrare l'applicazione nel tenant di Azure AD pubblica.

### <a name="get-an-azure-government-subscription"></a>Ottenere una sottoscrizione di Azure per enti pubblici

- Per ottenere una sottoscrizione di Azure per enti pubblici, vedere [la gestione e la connessione alla sottoscrizione di Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).
- Se non hai una sottoscrizione di Azure per enti pubblici, creare un [account gratuito](https://azure.microsoft.com/global-infrastructure/government/request/) prima di iniziare.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.us/) per registrare un'applicazione.
    1. Per trovare l'endpoint nel portale di Azure per altri cloud nazionali, vedere [gli endpoint di registrazione delle App](authentication-national-cloud.md#app-registration-endpoints)

1. Se l'account consente l'accesso a più di un tenant, selezionare il proprio account nell'angolo superiore destro e impostare la sessione del portale di Azure AD desiderato del tenant.
1. Passare alla pagina [Registrazioni app](https://aka.ms/ra/ff) di Microsoft Identity Platform per sviluppatori.
1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
1. Sotto **tipi di account supportati**, selezionare **gli account in qualsiasi directory dell'organizzazione**.
1. Nella sezione **URI di reindirizzamento** selezionare la piattaforma **Web** e impostare il valore per l'URL dell'applicazione in base al proprio server Web. Per le istruzioni per impostare e ottenere l'URL di reindirizzamento in Visual Studio e Node, vedere le sezioni seguenti.
1. Al termine, selezionare **Registra**.
1. Nella pagina **Panoramica**  dell'app prendere nota del valore del campo **ID applicazione (client)**.
1. Questa esercitazione richiede il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md) deve essere abilitata. Nel riquadro di spostamento a sinistra dell'applicazione registrata selezionare **Autenticazione**.
1. Nelle **Impostazioni avanzate**, in **Concessione implicita**, selezionare entrambe le caselle di controllo **Token ID** e **Token di accesso**. I token ID e di accesso sono obbligatori perché l'app deve consentire l'accesso agli utenti e chiamare un'API.
1. Selezionare **Salva**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passaggio 2:  Configurare il server web o di un progetto

- [Scaricare i file di progetto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) per un server web locale, ad esempio nodo.

  oppure

- [Scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

E quindi procedere al ['Di configurare la SPA di JavaScript'](#step-4-configure-your-javascript-spa) per configurare l'esempio di codice prima dell'esecuzione.

### <a name="step-3-use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Passaggio 3: Usare Microsoft Authentication Library (MSAL) per l'accesso degli utenti

Seguire i passaggi [Javascript esercitazione](tutorial-v2-javascript-spa.md#create-your-project) per creare il progetto e integrare con Microsoft Authentication Library (MSAL) per l'accesso dell'utente.

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

Dove:

- `Enter_the_Application_Id_here` è l'**ID applicazione (client)** per l'applicazione registrata.
- `Enter_the_Tenant_Info_Here` è impostato su una delle opzioni seguenti:
    - Se l'applicazione supporta **gli account in questa directory dell'organizzazione**, sostituire questo valore con il **ID Tenant** oppure **nome Tenant** (ad esempio, Contoso.microsoft.com)
    - Se l'applicazione supporta **Account in qualsiasi directory organizzativa**, sostituire questo valore con `organizations`
    -  Per trovare gli endpoint di autenticazione per tutti i cloud nazionali, vedere [endpoint di autenticazione di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

     > [!NOTE]
     > Scenari basati su account (MSA) personale Microsoft non sono supportati nei cloud nazionali.
  
-   `graphEndpoint` -è l'endpoint di Microsoft Graph per Microsoft cloud per enti pubblici degli Stati Uniti.
    -  Per trovare gli endpoint di Microsoft Graph per tutti i cloud nazionali, vedere [endpoint di Microsoft Graph nel cloud nazionali](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="net"></a>.NET

MSAL .NET consente agli utenti l'accesso, acquisire token e chiamare l'API Graph di Microsoft nei cloud nazionali.

L'esercitazione seguente viene illustrato come compilare un'app Web .NET Core 2.2 MVC che usa OpenID Connect per consentire agli utenti con i propri account "lavoro e dell'istituto di istruzione" all'interno dell'organizzazione che appartengono a cloud nazionali.

- Per acquisire token di accesso degli utenti, seguire [in questa esercitazione](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Per chiamare l'API Graph Microsoft, seguire [in questa esercitazione](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet per Azure Cina](https://docs.microsoft.com/azure/china/)
- [Azure Germania](https://docs.microsoft.com/azure/germany/)