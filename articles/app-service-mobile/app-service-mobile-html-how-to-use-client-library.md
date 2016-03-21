<properties
	pageTitle="Come usare JavaScript SDK per le app per dispositivi mobili di Azure"
	description="Come usare v per le app per dispositivi mobili di Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="adrianha"/>

# Come usare la libreria client JavaScript per le app per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida descrive come eseguire scenari comuni usando il più recente [JavaScript SDK per le app per dispositivi mobili di Azure]. Se si ha familiarità con le app per dispositivi mobili di Azure, prima è necessario completare l'[Avvio rapido alle app per dispositivi mobili di Azure] per creare un back-end e una tabella. In questa Guida, l'attenzione è posta sull'uso di un back-end mobile nelle applicazioni Web HTML/JavaScript.

##<a name="Setup"></a>Installazione e prerequisiti

In questa guida si presuppone che siano stati creati un backend e una tabella. In questa guida si presuppone che la tabella abbia lo stesso schema delle tabelle presenti in tali esercitazioni.

L'installazione di JavaScript SDK per le app per dispositivi mobili di Azure può essere eseguito tramite il comando `npm`:

```
npm install azure-mobile-apps-client --save
```

Dopo l'installazione, la libreria si trova in `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.js`. Copiare questo file nella propria area Web.

```
<script src="path/to/MobileServices.Web.js"></script>
```

La libreria può anche essere utilizzata come modulo ES2015, all'interno di ambienti CommonJS come ad esempio Browserify e Webpack, e come libreria AMD. Ad esempio:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Procedura: Autenticare gli utenti

Il servizio app di Azure supporta l'autenticazione e l'autorizzazione degli utenti di app usando diversi provider di identità esterni, a esempio Facebook, Google, account Microsoft e Twitter. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per ulteriori informazioni, vedere l'esercitazione [Introduzione all'autenticazione].

Sono supportati due flussi di autenticazione, ovvero un flusso server e un flusso client. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, poiché si basa su SDK specifici del provider.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>Procedura: Registrarsi per le notifiche push

Installare [phonegap-plugin-push] per gestire le notifiche push. Può essere facilmente aggiunto usando il comando `cordova plugin add` nella riga di comando o tramite il programma di installazione del plug-in Git in Visual Studio. Il codice seguente nell'app Apache Cordova registrerà il dispositivo per le notifiche push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Usare Notification Hubs SDK per inviare notifiche push dal server. Non inviare mai le notifiche push direttamente dai client, perché potrebbero essere usate per attivare un attacco Denial of Service nei confronti di Hub di notifica o PNS.

<!-- URLs. -->
[Avvio rapido alle app per dispositivi mobili di Azure]: app-service-mobile-cordova-get-started.md
[Introduzione all'autenticazione]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin for Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[your first Apache Cordova app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/it-IT/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0309_2016-->