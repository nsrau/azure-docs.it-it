<properties linkid="develop-mobile-tutorials-get-started-with-users-dotnet" urlDisplayName="Get Started with Users" pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Introduzione all'autenticazione in Servizi mobili
=================================================

[Windows Store C\#](/it-it/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/it-it/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/it-it/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/it-it/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/it-it/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/it-it/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/it-it/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

È inoltre possibile visualizzare una versione video dell'esercitazione facendo clic sul fotogramma a destra.

[guarda l'esercitazione](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services) [Riproduci video](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) 10:04

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili](#register)
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati](#permissions)
3.  [Aggiunta dell'autenticazione all'app](#add-authentication)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili](/it-it/develop/mobile/tutorials/get-started).

**Nota**

In questa esercitazione viene illustrato il metodo di base disponibile in Servizi mobili per autenticare gli utenti tramite diversi provider di identità. Questo metodo è semplice da configurare e supporta più provider, tuttavia richiede l'accesso da parte degli utenti a ogni avvio dell'app. Per utilizzare invece Live Connect per offrire un ambiente Single Sign-On nella propria app di Windows Store, vedere l'argomento [Single Sign-On per le app di Windows Store tramite Live Connect](/it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet).

Registrazione dell'appRegistrazione dell'app per l'autenticazione e configurazione di Servizi mobili
----------------------------------------------------------------------------------------------------

Per poter autenticare gli utenti, è necessario registrare la propria app presso un provider di identità. È quindi necessario registrare il segreto client generato dal provider con Servizi mobili.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sul servizio mobile.

	![][4]

2.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Mobile Service URL**.

	![][5]

    Potrebbe essere necessario fornire questo valore al provider di identità durante la registrazione dell'app.

3.  Scegliere un provider di identità supportato nell'elenco riportato di seguito e seguire la procedura per registrare l'app con tale provider:
	-   [Account Microsoft](/it-it/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Account di accesso di Facebook](/it-it/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Account di accesso di Twitter](/it-it/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Account di accesso di Google](/it-it/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    	Non dimenticare di prendere nota dei valori dell'identità e del segreto client generati dal provider.

		**Nota sulla sicurezza**

		Il segreto generato dal provider è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app.

1.  Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere l'identificatore dell'app e il segreto condiviso forniti dal provider di identità e fare clic su **Save**.

	![][13]

2.  (Facoltativo) Completare la procedura descritta in [Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft](/it-it/develop/mobile/how-to-guides/register-windows-store-app-package).

    **Nota**

    Questo passaggio è facoltativo perché si applica solo al provider di accesso con account Microsoft. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client è in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Completare questo passaggio se si prevede di utilizzare il provider di identità per account Microsoft.

Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.

Limitazione delle autorizzazioniLimitazione delle autorizzazioni agli utenti autenticati
----------------------------------------------------------------------------------------

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

	![][14]

2.  Fare clic sulla scheda **Permissions**, impostare tutte le autorizzazioni su **Only authenticated users** e quindi fare clic su **Save**. In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato. Questa impostazione semplifica inoltre gli script dell'esercitazione successiva, in quanto non sarà necessario considerare la possibilità di accesso da parte di utenti anonimi.

	![][15]

3.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili](/it-it/develop/mobile/tutorials/get-started).

4.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

	L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella TodoItem richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

Aggiunta dell'autenticazioneAggiunta dell'autenticazione all'app
----------------------------------------------------------------

1.  Aprire il file del progetto mainpage.xaml.cs e aggiungere l'istruzione using seguente:

         using Windows.UI.Popups;

2.  Aggiungere il seguente frammento di codice alla classe MainPage:

         private MobileServiceUser user;
         private async System.Threading.Tasks.Task Authenticate()
         {
             while (user == null)
             {
                 string message;
                 try
                 {
                     user = await App.MobileService
                         .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                     message = 
                         string.Format("Accesso eseguito - {0}", user.UserId);
                 }
                 catch (InvalidOperationException)
                 {
                     message = "Eseguire l'accesso. Operazione obbligatoria";
                 }
                            
                 var dialog = new MessageDialog(message);
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
             }
         }

    Verranno creati una variabile membro per archiviare l'utente corrente e un metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Facebook. Se si utilizza un provider di identità diverso da Facebook, sostituire il valore di **MobileServiceAuthenticationProvider** con il nome del provider.

    **Nota**

    Se le informazioni del pacchetto dell'app di Windows Store sono state registrate con Servizi mobili, è necessario chiamare il metodo [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594) specificando il valore **true** per il parametro *useSingleSignOn*. In caso contrario, gli utenti dovranno comunque specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso.

3.  Sostituire l'override del metodo **OnNavigatedTo** esistente con il seguente metodo che chiama il nuovo metodo **Authenticate**:

         protected override async void OnNavigatedTo(NavigationEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

4.  Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato.

        Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

Passaggi successivi
-------------------

Nella prossima esercitazione, [Autorizzazione di utenti con script](/it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per ulteriori informazioni su come utilizzare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili](/it-it/develop/mobile/how-to-guides/work-with-net-client-library)


[Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
[Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiunta dell'autenticazione all'app]: #add-authentication
[Passaggi successivi]:#next-steps






[4]: ./media/mobile-services-dotnet-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-dotnet-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-dotnet-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-change-table-perms.png



[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: ./mobile-services-single-sign-on-win8-dotnet.md
[Get started with Mobile Services]: /it-it/develop/mobile/tutorials/get-started
[Get started with data]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet
[Authorize users with scripts]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /it-it/develop/mobile/tutorials/get-started-with-users-js

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: /it-it/develop/mobile/how-to-guides/register-windows-store-app-package
