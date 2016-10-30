<properties
    pageTitle="Come usare il plug-in Apache Cordova per le app per dispositivi mobili di Azure"
    description="Come usare il plug-in Apache Cordova per le app per dispositivi mobili di Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Come usare la libreria client Apache Cordova per le app per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida descrive come eseguire scenari comuni usando il più recente [plug-in Apache Cordova per le app per dispositivi mobili di Azure]. Se non si ha familiarità con le app per dispositivi mobili di Azure, completare prima di tutto l' [esercitazione introduttiva sulle app per dispositivi mobili di Azure] per creare un back-end e una tabella e per scaricare un progetto Apache Cordova predefinito. In questa guida si esaminerà il plug-in Apache Cordova.

## <a name="supported-platforms"></a>Piattaforme supportate

Questo SDK supporta Apache Cordova v6.0.0 e versioni successive sui dispositivi iOS, Android e Windows.  Il supporto della piattaforma è il seguente:

* API Android 19-24 (KitKat tramite Nougat)
* iOS versioni 8.0 e successive.
* Windows Phone 8.0
* Windows Phone 8.1
* Piattaforma UWP (Universal Windows Platform)

##<a name="<a-name="setup"></a>setup-and-prerequisites"></a><a name="Setup"></a>Installazione e prerequisiti

In questa guida si presuppone che siano stati creati un backend e una tabella. In questa guida si presuppone che la tabella abbia lo stesso schema delle tabelle presenti in tali esercitazioni. Si presuppone anche che il plug-in Apache Cordova sia stato aggiunto al codice.  In caso contrario, è possibile aggiungere il plug-in Apache Cordova al progetto nella riga di comando:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Per altre informazioni sulla creazione della [prima app Apache Cordova], vedere la relativa documentazione.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]


##<a name="<a-name="auth"></a>how-to:-authenticate-users"></a><a name="auth"></a>Procedura: Autenticare gli utenti

Il servizio app di Azure supporta l'autenticazione e l'autorizzazione degli utenti di app usando diversi provider di identità esterni, a esempio Facebook, Google, account Microsoft e Twitter. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per ulteriori informazioni, vedere l'esercitazione [Introduzione all'autenticazione] .

Quando si usa l'autenticazione in un'app Apache Cordova, devono essere disponibili i plug-in Cordova seguenti:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Sono supportati due flussi di autenticazione, ovvero un flusso server e un flusso client.  Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, poiché si basa su SDK specifici del provider e del dispositivo.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="<a-name="configure-external-redirect-urls"></a>how-to:-configure-your-mobile-app-service-for-external-redirect-urls."></a><a name="configure-external-redirect-urls"></a>Procedura: Configurare il servizio App per dispositivi mobili per URL di reindirizzamento esterni.

Molti tipi di applicazioni Apache Cordova usano una funzionalità di loopback per gestire i flussi dell’interfaccia utente di OAuth.  I flussi dell'interfaccia utente di OAuth sull'host locale causa problemi in quanto il servizio di autenticazione sa usare il servizio solo con le impostazioni predefinite.  Alcuni esempi di flussi dell'interfaccia utente di OAuth problematici sono:

- L'emulatore Ripple.
- Live Reload con Ionic.
- L'esecuzione del back-end per dispositivi mobili in locale
- L'esecuzione del back-end per dispositivi mobili in un Servizio app di Azure diverso rispetto a quello che fornisce l'autenticazione.

Per aggiungere le proprie impostazioni locali alla configurazione seguire questa procedura:

1. Accedere al [portale di Azure]
2. Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome dell'app per dispositivi mobili.
3. Fare clic su **Strumenti**
4. Fare clic su **Esplora risorse** nel menu OSSERVAZIONE, quindi fare clic su **Vai**.  Si apre una nuova finestra o una nuova scheda.
5. Espandere i nodi **config**, **authsettings** del sito nel riquadro di spostamento a sinistra.
6. Fare clic su **Modifica**
7. Cercare l’elemento "allowedExternalRedirectUrls".  Può essere impostato su null o su una matrice di valori.  Modificare il valore nel valore seguente:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Sostituire gli URL con quelli del servizio.  Ad esempio includere "http://localhost:3000" (per il servizio di esempio Node.js) o "http://localhost:4400" (per il servizio Ripple).  Questi URL sono solo esempi e la situazione reale per i servizi dell'esempio potrebbe essere diversa.
8. Fare clic sul pulsante **Lettura/scrittura** nell'angolo in alto a destra della schermata.
9. Fare clic sul pulsante verde **PUT** .

A questo punto le impostazioni vengono salvate.  Non chiudere la finestra del browser finché non è terminato il salvataggio delle impostazioni.
Aggiungere gli URL di loopback anche alle impostazioni CORS del servizio app:

1. Accedere al [portale di Azure]
2. Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome dell'app per dispositivi mobili.
3. Il pannello Impostazioni si apre automaticamente.  In caso contrario fare clic su **Tutte le impostazioni**.
4. Fare clic su **CORS** nel menu API.
5. Immettere l'URL che si desidera aggiungere nella casella apposita e prema INVIO.
6. Immettere gli altri URL in base alle esigenze.
7. Fare clic su **Salva** per salvare le impostazioni.

Le nuove impostazioni saranno operative in 10-15 secondi.

##<a name="<a-name="register-for-push"></a>how-to:-register-for-push-notifications"></a><a name="register-for-push"></a>Procedura: Registrarsi per le notifiche push

Installare [phonegap-plugin-push] per gestire le notifiche push.  Questo plug-in può essere facilmente aggiunto usando il comando `cordova plugin add` nella riga di comando o tramite il programma di installazione del plug-in Git in Visual Studio.  Il codice seguente nell'app Apache Cordova registra il dispositivo per le notifiche push:

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

Usare Notification Hubs SDK per inviare notifiche push dal server.  Non inviare mai le notifiche push direttamente dai client. Questa operazione può essere usata per attivare un attacco denial-of-service agli Hub di notifica o al servizio PNS.  Il servizio PNS potrebbe escludere il traffico come conseguenza di questi attacchi.

<!-- URLs. -->
[portale di Azure]: https://portal.azure.com
[esercitazione introduttiva sulle app per dispositivi mobili di Azure]: app-service-mobile-cordova-get-started.md
[Introduzione all'autenticazione]: app-service-mobile-cordova-get-started-users.md
[Aggiungere l'autenticazione all'app]: app-service-mobile-cordova-get-started-users.md

[plug-in Apache Cordova per le app per dispositivi mobili di Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[prima app Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Documentazione dell'oggetto query]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx



<!--HONumber=Oct16_HO2-->


