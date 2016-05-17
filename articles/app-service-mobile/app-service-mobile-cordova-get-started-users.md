<properties
	pageTitle="Aggiungere l'autenticazione in Apache Cordova con App per dispositivi mobili| Servizio app di Azure"
	description="Informazioni su come usare App per dispositivi mobili nel servizio app di Azure per autenticare gli utenti dell'app Apache Cordova tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Riepilogo

Questa esercitazione consente di aggiungere l'autenticazione al progetto introduttivo TodoList in Apache Cordova tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione relativa alla [creazione di un'app per dispositivi mobili], che deve essere completata per prima.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A questo punto, è possibile verificare che l'accesso anonimo al back-end è stato disabilitato. In Visual Studio aprire il progetto creato dopo aver completato l'esercitazione [introduttiva alle App per dispositivi mobili], quindi eseguire l'applicazione nell'**Emulatore Android di Google** e verificare che venga visualizzato un errore di connessione imprevisto dopo l'avvio dell'applicazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al back-end dell'app per dispositivi mobili.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

1. Aprire il progetto in **Visual Studio**, quindi aprire il file `www/index.html` per la modifica.

2. Individuare il tag META `Content-Security-Policy` nella sezione head. È necessario aggiungere l'host OAuth all'elenco delle origini consentite.

    | Provider | Nome del provider SDK | Host OAuth |
    | :--------------------- | :---------------- | :-------------------------- |
    | Azure Active Directory | aad | https://login.windows.net |
    | Facebook | facebook | https://www.facebook.com |
    | Google | google | https://accounts.google.com |
    | Macchina virtuale | microsoftaccount | https://login.live.com |
    | Twitter | twitter | https://api.twitter.com |

    Ecco un esempio di Content-Security-Policy implementato per Azure Active Directory:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
			data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    È necessario sostituire `https://login.windows.net` con l'host OAuth della tabella precedente. Per altre informazioni su questo tag META, vedere la [documentazione relativa ai criteri di sicurezza del contenuto].

    Si noti che alcuni provider di autenticazione non richiedono modifiche a Content-Security-Policy quando viene usato in dispositivi mobili appropriati. Ad esempio, non sono richieste modifiche a Content-Security-Policy quando si usa l'autenticazione di Google in un dispositivo Android.

3. Aprire il file `www/js/index.js` per la modifica, individuare il metodo `onDeviceReady()` e nel codice di creazione del client aggiungere quanto segue:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Si noti che questo codice sostituisce il codice esistente che crea il riferimento alla tabella e aggiorna l'interfaccia utente.

    Il metodo login() inizia l'autenticazione con il provider. Il metodo login() è una funzione asincrona che restituisce una promessa JavaScript. Il resto dell'inizializzazione viene inserito nella risposta della promessa in modo che non venga eseguita finché non viene completato il metodo login().

4. Nel codice aggiunto sostituire `SDK_Provider_Name` con il nome del provider di accesso. Ad esempio, per Azure Active Directory usare `client.login('aad')`.

4. Eseguire il progetto. Al termine dell'inizializzazione del progetto, l'applicazione verrà visualizzata la pagina di accesso di OAuth per il provider di autenticazione scelto.

##<a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, leggere la pagina relativa all'[autenticazione ] con il servizio app di Azure.
* Continuare l'esercitazione aggiungendo [notifiche push] all'app Apache Cordova.

Informazioni su come usare gli SDK.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[creazione di un'app per dispositivi mobili]: app-service-mobile-cordova-get-started.md
[introduttiva alle App per dispositivi mobili]: app-service-mobile-cordova-get-started.md
[documentazione relativa ai criteri di sicurezza del contenuto]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[notifiche push]: app-service-mobile-cordova-get-started-push.md
[autenticazione ]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-codova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0504_2016-->