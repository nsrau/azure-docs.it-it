---
title: Configurare app a pagina singola-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'applicazione a singola pagina (configurazione del codice dell'app)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882336"
---
# <a name="single-page-application-code-configuration"></a>Applicazione a pagina singola: configurazione del codice

Informazioni su come configurare il codice per l'applicazione a singola pagina (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Librerie MSAL che supportano il flusso implicito

La piattaforma Microsoft Identity fornisce le librerie Microsoft Authentication Library (MSAL) seguenti per supportare il flusso implicito usando le procedure di sicurezza consigliate per il settore:

| Libreria MSAL | Descrizione |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Libreria JavaScript semplice da usare in qualsiasi app Web sul lato client compilata tramite Framework JavaScript o SPA come angolare, VME. js e React. js. |
| ![MSAL angolare](media/sample-v2-code/logo_angular.png) <br/> [MSAL angolare](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper della libreria MSAL. js di base per semplificare l'uso nelle app a singola pagina compilate tramite il Framework angolare. |

## <a name="application-code-configuration"></a>Configurazione del codice dell'applicazione

In una libreria MSAL le informazioni di registrazione dell'applicazione vengono passate come configurazione durante l'inizializzazione della libreria.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Per altre informazioni sulle opzioni configurabili, vedere [inizializzazione dell'applicazione con MSAL. js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Accesso e disconnessione](scenario-spa-sign-in.md)
