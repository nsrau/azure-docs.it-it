<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure Javascript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="" solutions="" manager="" editor="" />

Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect
=====================================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone")

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure da un'app di Windows Store. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Live Connect. Dopo essere stato autenticato da Live Connect e avere eseguito l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.

**Nota**

In questa esercitazione vengono descritti i vantaggi offerti dall'ambiente di accesso Single Sign-On per le app di Windows Store, che semplifica l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. Per un ambiente di autenticazione più generalizzato che supporta più provider di autenticazione, vedere l'argomento [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-js/).

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione Live Connect:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili](#register)
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati](#permissions)
3.  [Aggiunta dell'autenticazione all'app](#add-authentication)

Per completare questa esercitazione, è necessario disporre di:

-   [Live SDK per Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express per Windows 8 RC o versione successiva

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).

Registrazione dell'appRegistrazione dell'app di Windows Store
-------------------------------------------------------------

Per poter autenticare gli utenti, è necessario inviare l'app a Windows Store. È quindi necessario registrare il segreto client per l'integrazione di Live Connect con Servizi mobili.

1.  Se l'app non è ancora stata registrata, passare alla pagina relativa all'[invio di app](http://go.microsoft.com/fwlink/p/?LinkID=266582) nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **App name**.

   	![][0]

2.  Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome applicazione** e quindi su **Salva**.

   	![][1]

   	Verrà creata una nuova registrazione a Windows Store per l'app.

3.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

   	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png)

   	Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

1.  Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft.

2.  Selezionare l'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

   	![][3]

   	Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.    

3.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sul servizio mobile.

   	![][4]

4.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Site URL**.

   	![][5]

    Questo valore verrà utilizzato per definire il dominio di reindirizzamento.

5.  Passare alla pagina [Applicazioni personali](http://go.microsoft.com/fwlink/p/?LinkId=262039) nel Centro sviluppatori Live Connect e fare clic sull'app nell'elenco **Applicazioni personali**.

   	![][6] 

6.  Fare clic sulla pagina **Edit settings**, quindi su **API Settings** e prendere nota dei valori di **Client ID** e **Client secret**.

   	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
    <p>Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.</p>
    </div>

1.  In **Dominio di reindirizzamento** immettere l'URL del servizio mobile indicato nel passaggio 8 e quindi fare clic su **Salva**.

2.  Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere il segreto ottenuto da Windows Store in **Client secret** e fare clic su **Save**.

   	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png)

Il servizio mobile e l'app sono ora configurati per funzionare con Live Connect.

Limitazione delle autorizzazioniLimitazione delle autorizzazioni agli utenti autenticati
----------------------------------------------------------------------------------------

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

   	![][14]

2.  Fare clic sulla scheda **Permissions**, impostare tutte le autorizzazioni su **Only authenticated users** e quindi fare clic su **Save**. In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato. Questa impostazione semplifica inoltre gli script dell'esercitazione successiva, in quanto non sarà necessario considerare la possibilità di accesso da parte di utenti anonimi.

   	![][15]

3.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).

4.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che venga generata un'eccezione con codice di stato 401 (non autorizzato).

   	L'eccezione viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti con Live Connect prima di richiedere risorse al servizio mobile.

Aggiunta dell'autenticazioneAggiunta dell'autenticazione all'app
----------------------------------------------------------------

1.  Scaricare e installare [Live SDK per Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253).

2.  Nel menu **Progetto** in Visual Studio fare clic su **Aggiungi riferimento**, espandere **Windows**, fare clic su **Estensioni**, selezionare **Live SDK**, quindi fare clic su **OK**.

   	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png)

   	Verrà aggiunto un riferimento a Live SDK al progetto.

1.  Aprire il file di progetto default.html e aggiungere l'elemento &lt;script\> seguente nell'elemento &lt;head\>.

         <script src="///LiveSDKHTML/js/wl.js"></script>

   	Microsoft IntelliSense verrà abilitato nel file default.html.

2.  Aprire il file di progetto default.js e aggiungere il commento seguente nella parte superiore del file.

         /// <reference path="///LiveSDKHTML/js/wl.js" />

   	Microsoft IntelliSense verrà abilitato nel file default.js.

3.  Nell'overload del metodo **app.OnActivated** sostituire la chiamata al metodo **refreshTodoItems** con il codice seguente:

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

    Questo codice inizializza il client Live Connect, forza una disconnessione, invia una nuova richiesta di accesso a Live Connect, invia il token di autenticazione restituito a Servizi mobili e quindi visualizza informazioni sull'utente connesso.

    **Nota**

    Questo codice forza una disconnessione, ove possibile, per garantire che all'utente vengano richieste le credenziali ogni volta che l'applicazione viene eseguita, al fine di semplificare il test dell'applicazione con account Microsoft diversi e verificare che l'autenticazione funzioni correttamente. Questo meccanismo funziona solo se l'utente che ha eseguito l'accesso non dispone di un account Microsoft connesso.

4.  Aggiornare la stringa *&lt;&lt; INSERT REDIRECT DOMAIN HERE \>\>* del passaggio precedente con il dominio di reindirizzamento specificato durante la configurazione dell'app in Live Connect, nel formato **https://*nome-servizio*.azure-mobile.net/**.

5.  Premere F5 per eseguire l'app e accedere a Live Connect con l'account Microsoft.

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

Passaggi successivi
-------------------

Nella prossima esercitazione, [Autorizzazione di utenti con script](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-js), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come utilizzare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-js).



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