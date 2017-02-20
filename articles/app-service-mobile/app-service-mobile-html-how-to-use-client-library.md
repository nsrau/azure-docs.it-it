---
title: Come usare JavaScript SDK per le app per dispositivi mobili di Azure
description: Come usare v per le app per dispositivi mobili di Azure
services: app-service\mobile
documentationcenter: javascript
author: adrianhall
manager: erikre
editor: 
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 013956232d1fdfdc1f35741c25294a37b7e9bd92
ms.openlocfilehash: 3914d5bdc8feff7d7de5c6f7255396acade341b9


---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Come usare la libreria client JavaScript per App per dispositivi mobili di Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida descrive come eseguire scenari comuni usando il più recente [JavaScript SDK per le app per dispositivi mobili di Azure]. Se si ha familiarità con le app per dispositivi mobili di Azure, prima è necessario completare l' [Avvio rapido alle app per dispositivi mobili di Azure] per creare un back-end e una tabella. In questa Guida, l'attenzione è posta sull'uso di un back-end mobile nelle applicazioni Web HTML/JavaScript.

## <a name="supported-platforms"></a>Piattaforme supportate
Il supporto del browser è limitato alle versioni correnti e aggiornate dei browser principali: Google Chrome, Microsoft Edge, Microsoft Internet Explorer e Mozilla Firefox.  L'SDK dovrebbe funzionare con qualsiasi browser abbastanza aggiornato.

Il pacchetto viene distribuito come Universal JavaScript Module, in modo da supportare i formati globali, AMD e CommonJS.

## <a name="a-namesetupasetup-and-prerequisites"></a><a name="Setup"></a>Installazione e prerequisiti
In questa guida si presuppone che siano stati creati un backend e una tabella. In questa guida si presuppone che la tabella abbia lo stesso schema delle tabelle presenti in tali esercitazioni.

L'installazione di JavaScript SDK per le app per dispositivi mobili di Azure può essere eseguito tramite il comando `npm` :

```
npm install azure-mobile-apps-client --save
```

La libreria può anche essere utilizzata come modulo ES2015, all'interno di ambienti CommonJS come ad esempio Browserify e Webpack, e come libreria AMD.  Ad esempio:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

È anche possibile usare una versione predefinita dell'SDK scaricandolo direttamente dalla rete CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="a-nameauthahow-to-authenticate-users"></a><a name="auth"></a>Procedura: Autenticare gli utenti
Il servizio app di Azure supporta l'autenticazione e l'autorizzazione degli utenti di app usando diversi provider di identità esterni, a esempio Facebook, Google, account Microsoft e Twitter. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per ulteriori informazioni, vedere l'esercitazione [Introduzione all'autenticazione] .

Sono supportati due flussi di autenticazione, ovvero un flusso server e un flusso client.  Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, poiché si basa su SDK specifici del provider.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="a-nameconfigure-external-redirect-urlsahow-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Procedura: Configurare il servizio App per dispositivi mobili per URL di reindirizzamento esterni.
Molti tipi di applicazioni JavaScript usano una funzionalità di loopback per gestire i flussi dell'interfaccia utente di OAuth.  Queste funzionalità includono:

* Esecuzione del servizio in locale
* Uso di Live Reload con Ionic Framework
* Reindirizzamento al servizio app per l'autenticazione.

L'esecuzione in locale può causare problemi perché, per impostazione predefinita, l'autenticazione del servizio app viene configurata solo per consentire l'accesso dal back-end dell'app per dispositivi mobili. Usare i passaggi seguenti per modificare le impostazioni del servizio app per abilitare l'autenticazione quando si esegue il server in locale:

1. Accedere al [portale di Azure]
2. Passare al back-end dell'app per dispositivi mobili.
3. Selezionare **Esplora risorse** nel menu **STRUMENTI DI SVILUPPO**.
4. Fare clic su **Vai** per aprire Esplora risorse per il back-end dell'app per dispositivi mobili in una nuova scheda o finestra.
5. Espandere il nodo **config** > **authsettings** per l'app.
6. Fare clic sul pulsante **Modifica** per abilitare la modifica della risorsa.
7. Cercare l'elemento **allowedExternalRedirectUrls** che deve essere null. Aggiungere gli URL in una matrice:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Sostituire gli URL nella matrice con gli URL del servizio, che in questo esempio è `http://localhost:3000` per il servizio di esempio Node.js locale. È anche possibile usare `http://localhost:4400` per il servizio Ripple o un altro URL, a seconda della configurazione dell'app.
8. Nella parte superiore della pagina fare clic su **Lettura/Scrittura**, quindi su **PUT** per salvare gli aggiornamenti.

È necessario anche aggiungere gli stessi URL di loopback alle impostazioni dell'elenco elementi consentiti CORS:

1. Ritornare al [portale di Azure].
2. Passare al back-end dell'app per dispositivi mobili.
3. Fare clic su **CORS** nel menu dell'**API**.
4. Immettere ogni URL nella casella di testo vuota **Origini consentite** .  Viene creata una nuova casella di testo.
5. Fare clic su **SALVA**

Dopo l'aggiornamento del backend, sarà possibile usare i nuovi URL di loopback nell'app.

<!-- URLs. -->
[Avvio rapido alle app per dispositivi mobili di Azure]: app-service-mobile-cordova-get-started.md
[Introduzione all'autenticazione]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[portale di Azure]: https://portal.azure.com/
[JavaScript SDK per le app per dispositivi mobili di Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx




<!--HONumber=Feb17_HO1-->


