---
title: Applicazione a pagina singola (configurazione del codice dell'app)-piattaforma di identità Microsoft
description: Informazioni su come creare un'applicazione a singola pagina (configurazione del codice dell'app)
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
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891522"
---
# <a name="single-page-application---code-configuration"></a>Configurazione del codice dell'applicazione a pagina singola

Informazioni su come configurare il codice per l'applicazione a singola pagina (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Librerie MSAL che supportano il flusso implicito

Microsoft Identity Platform fornisce la libreria MSAL. js per supportare il flusso implicito usando le procedure di sicurezza consigliate del settore.  

Le librerie che supportano il flusso implicito sono:

| Libreria MSAL | Descrizione |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Libreria JavaScript semplice da usare in qualsiasi app Web lato client compilata con Framework JavaScript o SPA come angolare, VME. js, React. js e così via. |
| ![MSAL angolare](media/sample-v2-code/logo_angular.png) <br/> [MSAL angolare](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper della libreria MSAL. js di base per semplificare l'uso nelle app a pagina singola compilate con il Framework angolare. Questa libreria è in anteprima e presenta [problemi noti](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) con alcune versioni e browser angolari. |

## <a name="application-code-configuration"></a>Configurazione del codice dell'applicazione

Nella libreria MSAL le informazioni di registrazione dell'applicazione vengono passate come configurazione durante l'inizializzazione della libreria.

### <a name="javascript"></a>JavaScript

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
Per altri dettagli sulle opzioni configurabili disponibili, vedere [inizializzazione dell'applicazione con MSAL. js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Accesso e disconnessione](scenario-spa-sign-in.md)
