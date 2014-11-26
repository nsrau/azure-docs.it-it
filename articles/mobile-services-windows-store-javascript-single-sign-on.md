<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure Javascript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/it-it/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/it-it/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure da un'app di Windows Store. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Live Connect. Dopo essere stato autenticato da Live Connect e avere eseguito l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.

> [WACOM.NOTE]Questa esercitazione illustra i vantaggi dell'ambiente di accesso Single Sign-On fornito da Live Connect per le app di Windows Store. che semplifica l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. Per un ambiente di autenticazione più generalizzato che supporta più provider di autenticazione, vedere l'argomento [Introduzione all'autenticazione][Introduzione all'autenticazione].

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione Live Connect:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app][Aggiunta dell'autenticazione all'app]

Per completare questa esercitazione, è necessario disporre di:

-   [Live SDK per Windows][Live SDK per Windows]
-   Microsoft Visual Studio 2012 Express per Windows 8 RC o versione successiva

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

> [WACOM.NOTE]Nell'esercitazione viene usato un servizio mobile back-end JavaScript. L'autenticazione gestita da client tramite Live Connect o altri client di autenticazione non è ancora supportata in un servizio mobile back-end .NET.

## <a name="register"></a>Registrazione dell'app di Windows Store

Per poter autenticare gli utenti, è necessario inviare l'app a Windows Store. È quindi necessario registrare il segreto client per l'integrazione di Live Connect con Servizi mobili.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili][1].

2.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a> Aggiunta dell'autenticazione all'app

1.  Scaricare e installare [Live SDK per Windows][Live SDK per Windows].

2.  Nel menu **Progetto** in Visual Studio fare clic su **Aggiungi riferimento**, espandere **Windows**, fare clic su **Estensioni**, selezionare **Live SDK**, quindi fare clic su **OK**.

    ![][0]

    Verrà aggiunto un riferimento a Live SDK al progetto.

3.  Aprire il file di progetto default.html e aggiungere l'elemento \<script\> seguente nell'elemento \<head\>.

        <script src="///LiveSDKHTML/js/wl.js"></script>

    Microsoft IntelliSense verrà abilitato nel file default.html.

4.  Aprire il file di progetto default.js e aggiungere il commento seguente nella parte superiore del file.

        /// <reference path="///LiveSDKHTML/js/wl.js" />

    Microsoft IntelliSense verrà abilitato nel file default.js.

5.  Nell'overload del metodo **app.OnActivated** sostituire la chiamata al metodo **refreshTodoItems** con il codice seguente:

        var session = null;   

        var logout = function () {
            return new WinJS.Promise(function (complete) {
                WL.getLoginStatus().then(function () {
                    if (WL.canLogout()) {
                        WL.logout(complete);                            
                    }
                    else {
                        complete();
                    }
                });
            });
        };                  

        var login = function () {
            return new WinJS.Promise(function (complete) {                    
                WL.login({ scope: "wl.basic"}).then(function (result) {
                    session = result.session;

                    WinJS.Promise.join([
                        WL.api({ path: "me", method: "GET" }),
                        client.login(result.session.authentication_token)
                    ]).done(function (results) {
                        var profile = results[0];
                        var mobileServicesUser = results[1];
                        refreshTodoItems();
                        var title = "Welcome " + profile.first_name + "!";
                        var message = "You are now logged in as: " + mobileServicesUser.userId;
                        var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                        dialog.showAsync().done(complete);                                
                    });                       
                }, function (error) {                        
                    session = null;
                    var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                    dialog.showAsync().done(complete);                        
                });
            });
        }

        var authenticate = function () {
            // Force a logout to make it easier to test with multiple Microsoft Accounts
            logout().then(login).then(function () {
                if (session === null) {
                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        WL.init({
            redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
        });           

        authenticate();

    Questo codice inizializza il client Live Connect, forza una disconnessione, invia una nuova richiesta di accesso a Live Connect, invia il token di autenticazione restituito a Servizi mobili e quindi visualizza informazioni sull'utente connesso. Questo codice forza una disconnessione, ove possibile, per garantire che all'utente vengano richieste le credenziali ogni volta che l'applicazione viene eseguita, al fine di semplificare il test dell'applicazione con account Microsoft diversi e verificare che l'autenticazione funzioni correttamente. Questo meccanismo funziona solo se l'utente che ha eseguito l'accesso non dispone di un account Microsoft connesso.

    > [WACOM.NOTE]Evitare di richiedere token di autenticazione di Live Connection o Servizi mobili ogni volta che viene eseguita l'app. Oltre a essere inefficace, questo metodo può comportare problemi di utilizzo nel caso in cui molti clienti provino ad avviare simultaneamente l'app. Un metodo migliore è quello di memorizzare i token nella cache e provare a usare il token di Servizi mobili nella cache prima di chiamare **LoginWithMicrosoftAccountAsync**. Per un esempio di come memorizzare nella cache questo token, vedere [Introduzione all'autenticazione][2].

6.  Aggiornare la stringa *\<\< INSERT REDIRECT DOMAIN HERE \>\>* del passaggio precedente con il dominio di reindirizzamento specificato durante la configurazione dell'app in Live Connect, nel formato **[https://\_nome-servizio\_.azure-mobile.net/][https://\_nome-servizio\_.azure-mobile.net/]**.

7.  Premere F5 per eseguire l'app e accedere a Live Connect con l'account Microsoft.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script][Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come utilizzare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione][3].



  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
  [1]: /it-it/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png
  [2]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/#tokens
  [Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-js
  [3]: /it-it/develop/mobile/tutorials/get-started-with-users-js
