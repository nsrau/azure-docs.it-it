---
title: Configurare l'app a pagina singola - Piattaforma di identità Microsoft Azure
description: Scopri come creare un'applicazione a pagina singola (configurazione del codice dell'app)
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
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f1e0bf44515aab18019b19b4f0a6f84183e5aac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160084"
---
# <a name="single-page-application-code-configuration"></a>Applicazione a pagina singola: configurazione del codice

Informazioni su come configurare il codice per l'applicazione a pagina singola (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Librerie MSAL che supportano il flusso implicito

La piattaforma di identità Microsoft fornisce le seguenti librerie Microsoft Authentication Library (MSAL) per supportare il flusso implicito utilizzando le procedure di sicurezza consigliate dal settore:  

| Libreria MSAL | Descrizione |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (informazioni in base alle malattie in trassi](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Libreria JavaScript semplice per l'utilizzo in qualsiasi applicazione Web lato client che viene compilata tramite framework JavaScript o SPA come Angular, Vue.js e React.js. |
| ![MSAL Angolare](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angolare](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper della libreria MSAL.js di base per semplificare l'uso nelle app a pagina singola compilate tramite il framework angolare. Questa libreria è in anteprima e presenta [problemi noti](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) con determinate versioni e browser angolari. |

## <a name="application-code-configuration"></a>Configurazione del codice dell'applicazione

In una libreria MSAL, le informazioni di registrazione dell'applicazione vengono passate come configurazione durante l'inizializzazione della libreria.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Per ulteriori informazioni sulle opzioni configurabili, vedere [Inizializzazione dell'applicazione con MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Accesso e disconnessione](scenario-spa-sign-in.md)
