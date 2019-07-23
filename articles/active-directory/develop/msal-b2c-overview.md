---
title: Informazioni sull'interoperabilità tra le applicazioni e Azure AD B2C tramite Microsoft Authentication Library
description: Microsoft Authentication Library (MSAL) rende possibile l'interoperabilità delle applicazioni con Azure AD B2C e l'acquisizione di token per chiamare API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web di terze parti o API Web proprie.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
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
ms.openlocfilehash: db05f59faf945e425761fe7a20bad3e263246a39
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849348"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Usare Microsoft Authentication Library (MSAL) per l'interoperabilità con Azure Active Directory B2C

Microsoft Authentication Library (MSAL) permette agli sviluppatori di applicazioni di autenticare gli utenti con identità di social networking e locali usando [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C è un servizio di gestione delle identità, che consente di personalizzare e controllare il modo in cui i clienti effettuano l'iscrizione, l'accesso e la gestione dei profili quando usano le applicazioni fornite.

Azure AD B2C permette anche di personalizzare e ottimizzare l'interfaccia utente delle applicazioni per offrire un'esperienza uniforme ai clienti.

Questa esercitazione illustra come usare MSAL per l'interoperabilità con Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

Se non è ancora stato creato un [tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), crearlo ora. È anche possibile usare un tenant Azure AD B2C esistente.

## <a name="javascript"></a>JavaScript

I passaggi seguenti descrivono in che modo un'applicazione a pagina singola può usare Azure AD B2C per l'iscrizione e l'accesso e per chiamare un'API Web protetta.

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

Per implementare l'autenticazione, è prima di tutto necessario registrare l'applicazione. Per istruzioni dettagliate, vedere [Registrare l'applicazione](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c).

### <a name="step-2-download-the-sample-application"></a>Passaggio 2: Scaricare l'applicazione di esempio

Scaricare l'esempio come file ZIP o clonarlo da GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passaggio 3: Configurare l'autenticazione

1. Aprire il file **index.html** nell'esempio.

1. Configurare l'esempio con l'ID applicazione e la chiave acquisiti in precedenza durante la registrazione dell'applicazione. Modificare le righe di codice seguenti sostituendo i valori con i nomi della directory e delle API:

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

Il nome del [flusso utente](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) in questa esercitazione è **B2C_1_signupsignin1**. Se si usa un nome di flusso utente diverso, impostare il valore dell'**autorità** su tale nome.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Passaggio 4: Configurare l'applicazione per l'uso di `b2clogin.com`

È possibile usare `b2clogin.com` al posto di `login.microsoftonline.com` come URL di reindirizzamento. Questa operazione viene eseguita nell'applicazione Azure AD B2C quando si configura un provider di identità per l'iscrizione e l'accesso.

L'uso di `b2clogin.com` nel contesto di `https://your-tenant-name.b2clogin.com/your-tenant-guid` ha gli effetti seguenti:

- I servizi Microsoft utilizzano meno spazio nell'intestazione del cookie.
- Gli URL non includono più un riferimento a Microsoft. Ad esempio, l'applicazione Azure AD B2C probabilmente fa riferimento a `login.microsoftonline.com`.

 Per usare `b2clogin.com`, è necessario aggiornare la configurazione dell'applicazione.  

- Impostare la proprietà **validateAuthority** su `false`, in modo che possano essere eseguiti reindirizzamenti con `b2clogin.com`.

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
> L'applicazione Azure AD B2C fa probabilmente riferimento a `login.microsoftonline.com` in diverse posizioni, ad esempio nei riferimenti dei flussi utente e negli endpoint di token. Verificare che l'endpoint di autorizzazione, l'endpoint di token e l'autorità di certificazione siano stati aggiornati in modo da usare `your-tenant-name.b2clogin.com`.

Seguire [questo esempio JavaScript MSAL](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) per indicazioni su come usare l'anteprima di MSAL per JavaScript (MSAL.js). L'esempio ottiene un token di accesso e chiama un'API protetta da Azure AD B2C.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personalizzazione dell'interfaccia utente](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)