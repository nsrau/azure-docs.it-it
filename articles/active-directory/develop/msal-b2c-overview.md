---
title: Come aggiungere l'integrazione con Azure AD B2C tramite Microsoft Authentication Library (MSAL)
description: Microsoft Authentication Library (MSAL) permette agli sviluppatori di applicazioni di aggiungere l'integrazione con Azure AD B2C e acquisire token per chiamare API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web di terze parti o un'API Web propria.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87bd8834f840c2246bf3adc1d1f9cd9b8f635915
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191010"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Integrare Microsoft Authentication Library (MSAL) con Azure Active Directory B2C

Microsoft Authentication Library (MSAL) permette agli sviluppatori di applicazioni di autenticare gli utenti con identità di social networking e locali usando [Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). Azure Active Directory (Azure AD) B2C è un servizio di gestione delle identità che consente di personalizzare e controllare il modo in cui i clienti si iscrivono, accedono e gestiscono i rispettivi profili quando usano le applicazioni,

Azure Active Directory (Azure AD) B2C permette anche di personalizzare e ottimizzare l'interfaccia utente delle applicazioni per offrire un'esperienza uniforme ai clienti.

Questa esercitazione descrive come usare Microsoft Authentication Library (MSAL) per l'integrazione con Azure Active Directory (Azure AD) B2C.


## <a name="prerequisites"></a>Prerequisiti

Se non è ancora stato creato un [tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), crearlo ora. Usare un tenant Azure AD B2C esistente. 

## <a name="javascript"></a>JavaScript

I passaggi seguenti descrivono in che modo un'applicazione a pagina singola può usare Azure AD B2C per l'iscrizione e l'accesso degli utenti e per chiamare un'API Web protetta.

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

Per implementare l'autenticazione, è necessario registrare l'applicazione. Per registrare l'app, vedere [questa pagina](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) per informazioni dettagliate sui passaggi.

### <a name="steps-2-download-applications"></a>Passaggio 2: Scaricare le applicazioni

Scaricare un file ZIP o clonare l'esempio da GitHub.
>Clonazione dell'esempio Git https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>Passaggio 3: Authentication

1. Aprire il file index.html nell'esempio.

2. Configurare l'esempio con l'ID applicazione e la chiave acquisiti in precedenza durante la registrazione dell'applicazione. Modificare le righe di codice seguenti sostituendo i valori con i nomi della directory e delle API:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Il nome del [flusso utente](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) usato in questa esercitazione è B2C_1_signupsignin1. Se si usa un nome di flusso utente diverso, immettere il nome usato nel valore dell'autorità.


### <a name="configure-application-to-use-b2clogincom"></a>Configurare l'applicazione in modo da usare `b2clogin.com`

Quando si configura un provider di identità per l'iscrizione e l'accesso nell'applicazione Azure Active Directory (Azure AD) B2C, è possibile usare `b2clogin.com` al posto di `login.microsoftonline.com` come URL di reindirizzamento.

**`b2clogin.com`**, ovvero 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` viene usato per:

- Lo spazio usato nell'intestazione cookie dei servizi Microsoft viene ridotto.
- Gli URL non includono più un riferimento a Microsoft. Ad esempio, l'applicazione Azure AD B2C probabilmente fa riferimento a login.microsoftonline.com


 Per usare "b2clogin.com", è necessario aggiornare la configurazione dell'applicazione.  

1. Aggiornare ValidateAuthority: impostare la proprietà **validateAuthority** su `false`. Quando la proprietà **validateAuthority** è impostata su false, sono consentiti i reindirizzamenti a b2clogin.com.

L'esempio seguente illustra come si può impostare la proprietà:
```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> L'applicazione Azure AD B2C fa probabilmente riferimento a login.microsoftonline.com in diverse posizioni, ad esempio nei riferimenti dei flussi utente e negli endpoint di token. Assicurarsi che l'endpoint di autorizzazione, l'endpoint di token e l'autorità di certificazione siano stati aggiornati in modo da usare nome-tenant.b2clogin.com.

Seguire questo [esempio MSAL JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) su come usare l'anteprima di Microsoft Authentication Library per JavaScript (msal) per ottenere un token di accesso e chiamare un'API protetta tramite Azure AD B2C.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personalizzazione dell'interfaccia utente](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)