<properties
	pageTitle="Come usare JavaScript SDK per le app per dispositivi mobili di Azure"
	description="Come usare v per le app per dispositivi mobili di Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="adrianha;ricksal"/>

# Come usare la libreria client JavaScript per le app per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida descrive come eseguire scenari comuni usando il più recente [JavaScript SDK per le app per dispositivi mobili di Azure]. Se si ha familiarità con le app per dispositivi mobili di Azure, prima è necessario completare l'[Avvio rapido alle app per dispositivi mobili di Azure] per creare un back-end e una tabella. In questa Guida, l'attenzione è posta sull'uso di un back-end mobile nelle applicazioni Web HTML/JavaScript.

##<a name="Setup"></a>Installazione e prerequisiti

In questa guida si presuppone che siano stati creati un backend e una tabella. In questa guida si presuppone che la tabella abbia lo stesso schema delle tabelle presenti in tali esercitazioni.

L'installazione di JavaScript SDK per le app per dispositivi mobili di Azure può essere eseguito tramite il comando `npm`:

```
npm install azure-mobile-apps-client --save
```

Dopo l'installazione, la libreria sarà disponibile in `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`. Copiare questo file nella propria area Web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
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

###<a name="configure-external-redirect-urls"></a>Procedura: Configurare il servizio app per dispositivi mobili per URL di reindirizzamento esterni.

Diversi tipi di applicazioni JavaScript usano una funzionalità di loopback per gestire i flussi dell'interfaccia utente di OAuth, ad esempio quando si esegue il servizio in locale, si usa il ricaricamento live nel framework Ionic o si reindirizza al servizio app per l'autenticazione. Ciò può causare problemi perché, per impostazione predefinita, l'autenticazione del servizio app viene configurata solo per consentire l'accesso dal back-end dell'app per dispositivi mobili.

Usare i passaggi seguenti per modificare le impostazioni del servizio app per abilitare l'autenticazione dall'host locale:

1. Accedere al [portale di Azure], passare al back-end dell'app per dispositivi mobili, quindi fare clic su **Strumenti** > **Esplora risorse** > **Vai** per aprire una nuova finestra di Esplora risorse per il back-end (sito) dell'app per dispositivi mobili.

2. Espandere il nodo **config** per l'app, quindi fare clic su **authsettings** > **Modifica**, trovare l'elemento **allowedExternalRedirectUrls**, che dovrà essere null, e modificarlo come segue:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Sostituire gli URL nella matrice con gli URL del servizio, che in questo esempio è `http://localhost:3000` per il servizio di esempio Node.js locale. È anche possibile usare `http://localhost:4400` per il servizio Ripple o un altro URL, a seconda della configurazione dell'app.
    
3. Nella parte superiore della pagina fare clic su **Lettura/Scrittura**, quindi su **PUT** per salvare gli aggiornamenti.

    È tuttavia necessario aggiungere gli stessi URL di loopback alle impostazioni dell'elenco elementi consentiti CORS:

4. Nel back-end dell'app per dispositivi mobili del [portale di Azure] fare clic su **Tutte le impostazioni** > **CORS**, aggiungere gli URL di loopback all'elenco elementi consentiti, quindi fare clic su **Salva**.

Dopo l'aggiornamento del backend, sarà possibile usare i nuovi URL di loopback nell'app.

<!-- URLs. -->
[Avvio rapido alle app per dispositivi mobili di Azure]: app-service-mobile-cordova-get-started.md
[Introduzione all'autenticazione]: app-service-mobile-cordova-get-started-users.md
[Aggiungere l'autenticazione all'app]: app-service-mobile-cordova-get-started-users.md

[JavaScript SDK per le app per dispositivi mobili di Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[documentazione relativa all'oggetto Query]: https://msdn.microsoft.com/it-IT/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0629_2016-->