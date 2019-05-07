---
title: Applicazione a singola pagina (configurazione del codice dell'app) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione a singola pagina (configurazione del codice dell'App)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6236f0fa2400113225e04ffd4884cf743d1e250a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138279"
---
# <a name="single-page-application---code-configuration"></a>Applicazione a singola pagina - Configurazione del codice

Informazioni su come configurare il codice per l'applicazione a pagina singola (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Librerie MSAL che supportano flusso implicito

Piattaforma delle identità Microsoft fornisce consigliate sicuro della libreria msal. js per supportare il flusso implicito usando del settore.  

Le librerie che supportano flusso implicito sono:

| Libreria MSAL | DESCRIZIONE |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Libreria JavaScript normale per l'utilizzo in qualsiasi app web del lato client creato usando i framework JavaScript o applicazione a singola pagina, ad esempio Angular, VUE, React. js e così via. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper della libreria msal. js di base per semplificare l'utilizzo nelle App a singola pagina creata con il framework di Angular. Questa libreria è disponibile in anteprima e ha [problemi noti](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) con determinate versioni di Angular e i browser. |

## <a name="application-code-configuration"></a>Configurazione del codice dell'applicazione

Nella libreria MSAL, le informazioni di registrazione dell'applicazione viene passate come configurazione durante l'inizializzazione della libreria.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Per altre informazioni sulle opzioni configurabili disponibili, vedere [inizializzazione dell'applicazione con msal. js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Accesso e disconnessione](scenario-spa-sign-in.md)
