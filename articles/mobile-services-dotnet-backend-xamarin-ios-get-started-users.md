<properties urlDisplayName="Get Started with authentication in Mobile Services for Xamarin iOS apps" pageTitle="Introduzione all'autenticazione in Servizi mobili per app per Xamarin iOS - Servizi mobili di Azure" metaKeywords="" description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per Xamarin iOS tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Aggiungere l'autenticazione all'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un simulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

In questa sezione si procederà alla modifica dell'app in modo da visualizzare una schermata di accesso prima dei dati. All'avvio, l'app non si connetterà al servizio mobile e non sarà visualizzato alcun dato. Dopo la prima volta che l'utente esegue il movimento di aggiornamento verrà visualizzata la schermata di accesso. Dopo aver eseguito correttamente l'accesso, verrà visualizzato un elenco di elementi ToDo.

1. Nel progetto client, aprire il file **QSTodoService.cs** e aggiungere le seguenti dichiarazioni a QSTodoService:

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Aggiungere un nuovo metodo **Authenticate** a **QSTodoService** con la definizione seguente:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout"><b>Nota</b>
	<p>Se si usa un provider di identità diverso da un account Facebook, sostituire il valore passato al metodo <strong>LoginAsync</strong> riportato in precedenza con uno dei seguenti: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> o <i>WindowsAzureActiveDirectory</i>.</p>
    </div>

3. Aprire **QSTodoListViewController.cs**. Modificare la definizione del metodo **ViewDidLoad** per rimuovere la chiamata a **RefreshAsync()** verso la fine:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;

			todoService.BusyUpdate += (bool busy) => {
				if (busy)
					activityIndicator.StartAnimating ();
				else 
					activityIndicator.StopAnimating ();
			};

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();

			AddRefreshControl ();
		}


4. Modificare il metodo **RefreshAsync** per autenticare e visualizzare una schermata di accesso se la proprietà **User** è Null. In corrispondenza del codice seguente nella parte superiore della definizione del metodo:

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. Fare clic su **Esegui** per compilare il progetto e avviare l'app nel simulatore iPhone. Verificare che nell'app non siano visualizzati dati. 

	Eseguire il movimento di aggiornamento spostando verso il basso l'elenco di elementi, in modo da visualizzare la schermata di accesso. Dopo aver correttamente immesso le credenziali valide, verrà visualizzato l'elenco di elementi ToDo e sarà possibile aggiornare i dati nell'app.

<!-- ## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzare gli utenti con gli script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. 
 -->
 
<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Passaggi successivi]:#next-steps


<!-- URLs. -->
[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push/
[Autorizzare gli utenti con gli script]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript e HTML]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
