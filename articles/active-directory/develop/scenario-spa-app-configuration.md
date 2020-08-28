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
ms.openlocfilehash: b42d720a425b92ec9002f7c2b9797a91f70dafe2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003026"
---
# <a name="single-page-application-code-configuration"></a>Applicazione a pagina singola: configurazione del codice

Informazioni su come configurare il codice per l'applicazione a singola pagina (SPA).

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>Librerie MSAL per Spa e flussi di autenticazione supportati

La piattaforma Microsoft Identity fornisce la seguente libreria di autenticazione Microsoft per JavaScript (MSAL.js) per supportare il flusso implicito del codice di autorizzazione e di flusso con PKCE usando le procedure di sicurezza consigliate per il settore:

| Libreria MSAL | Flusso | Descrizione |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Flusso del codice di autorizzazione (PKCE) | Libreria JavaScript semplice da usare in qualsiasi app Web sul lato client compilata tramite Framework JavaScript o SPA, ad esempio angolare, Vue.js e React.js. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Flusso implicito | Libreria JavaScript semplice da usare in qualsiasi app Web sul lato client compilata tramite Framework JavaScript o SPA, ad esempio angolare, Vue.js e React.js. |
| ![MSAL angolare](media/sample-v2-code/logo_angular.png) <br/> [MSAL angolare](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Flusso implicito | Wrapper della libreria di base MSAL.js per semplificare l'uso nelle app a singola pagina compilate tramite il Framework angolare. |

## <a name="application-code-configuration"></a>Configurazione del codice dell'applicazione

In una libreria MSAL le informazioni di registrazione dell'applicazione vengono passate come configurazione durante l'inizializzazione della libreria.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Per ulteriori informazioni sulle opzioni configurabili, vedere [inizializzazione di un'applicazione con MSAL.js](msal-js-initializing-client-applications.md).

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
