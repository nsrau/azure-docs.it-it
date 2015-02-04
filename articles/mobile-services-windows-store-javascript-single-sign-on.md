<properties urlDisplayName="Authenticate with single sign-on" pageTitle="Autenticare l'app con Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure JavaScript SSO" description="Informazioni su come usare l'accesso Single Sign-On di Live Connect in Servizi mobili di Azure da un'applicazione di Windows Store." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	


In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure da un'app di Windows Store.  Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Live Connect. Dopo essere stato autenticato da Live Connect e avere eseguito l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.  

>[WACOM.NOTE]In questa esercitazione vengono descritti i vantaggi offerti dall'ambiente di accesso Single Sign-On per le app di Windows Store, che semplifica l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. Per un ambiente di autenticazione più generalizzato che supporta più provider di autenticazione, vedere l'argomento <a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/">Introduzione all'autenticazione</a>.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione Live Connect:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]

Per completare questa esercitazione, è necessario disporre di:

+ [Live SDK per Windows]
+ Microsoft Visual Studio 2012 Express per Windows 8 RC o versione successiva
+ Prima è necessario completare anche l'esercitazione [Aggiungere Servizi mobili a un'app esistente].

## <a name="register"></a>Registrare l'app di Windows Store

Per poter autenticare gli utenti, è necessario inviare l'app a Windows Store. È quindi necessario registrare il segreto client per l'integrazione di Live Connect con Servizi mobili.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

## <a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione <a href="/it-it/documentation/articles/mobile-services-windows-store-get-started">Introduzione a Servizi mobili</a>.</p></li> 
<li><p>Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a>Aggiungere l'autenticazione all'app

1. Scaricare e installare [Live SDK per Windows].

2. Nel menu **Progetto** in Visual Studio fare clic su **Aggiungi riferimento**, espandere **Windows**, fare clic su **Estensioni**, selezionare **Live SDK**, quindi fare clic su **OK**. 

  	![][16]

  	Verrà aggiunto un riferimento a Live SDK al progetto.

3. Aprire il file di progetto default.html e aggiungere l'elemento &lt;script&gt; seguente nell'elemento &lt;head&gt;. 

        <script src="///LiveSDKHTML/js/wl.js"></script>

   	Microsoft IntelliSense verrà abilitato nel file default.html.


4. Aprire il file di progetto default.js e aggiungere il commento seguente nella parte superiore del file. 

        /// <reference path="///LiveSDKHTML/js/wl.js" />

   	Microsoft IntelliSense verrà abilitato nel file default.js.

5. Nell'overload del metodo **app.OnActivated** sostituire la chiamata al metodo **refreshTodoItems** con il codice seguente: 
	
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

	>[WACOM.NOTE]Evitare di richiedere token di autenticazione di Live Connection o Servizi mobili ogni volta che viene eseguita l'app. Oltre a essere inefficace, questo metodo può comportare problemi di uso nel caso in cui molti clienti provino ad avviare simultaneamente l'app. Un metodo migliore è quello di memorizzare i token nella cache e provare a usare il token di Servizi mobili nella cache prima di chiamare **LoginWithMicrosoftAccountAsync**. Per un esempio di come memorizzare nella cache questo token, vedere [Introduzione all'autenticazione](/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/#tokens).
	
7. Aggiornare la stringa _<< INSERT REDIRECT DOMAIN HERE >>_ del passaggio precedente con il dominio di reindirizzamento specificato durante la configurazione dell'app in Live Connect, nel formato **https://_nome-servizio_.azure-mobile.net/**.
		
8. Premere F5 per eseguire l'app e accedere a Live Connect con l'account Microsoft. 

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzare gli utenti con gli script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come usare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione].

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png

<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Aggiungere Servizi mobili a un'app esistente]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users
[Autorizzare gli utenti con gli script]: /it-it/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/

[Portale di gestione di Azure]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
