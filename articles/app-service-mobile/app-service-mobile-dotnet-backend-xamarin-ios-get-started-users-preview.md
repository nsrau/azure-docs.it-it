<properties 
	pageTitle="Introduzione all'autenticazione per app per dispositivi mobili in Xamarin iOS" 
	description="Informazioni su come usare le app per dispositivi mobili per autenticare gli utenti dell'app Xamarin iOS tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="mahender"/>

# Aggiungere l'autenticazione all'app per Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Questo argomento descrive come autenticare gli utenti di un'app mobile del servizio app dall'applicazione client. In questa esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato dal servizio app. In seguito all'autenticazione e all'autorizzazione da parte dell'app mobile, viene visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva dell'app mobile. È anche necessario completare prima l'esercitazione [Creare un'app per Xamarin iOS].

##<a name="review"></a>Verificare la configurazione del progetto server (facoltativo)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare i servizi app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="5">
<li><p>In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un simulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere al codice dell'app per dispositivi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

In questa sezione si procederà alla modifica dell'app in modo da visualizzare una schermata di accesso prima dei dati. All'avvio, l'app non si connetterà al servizio app e non sarà visualizzato alcun dato. Dopo la prima volta che l'utente esegue il movimento di aggiornamento verrà visualizzata la schermata di accesso. Dopo aver eseguito correttamente l'accesso, verrà visualizzato un elenco di elementi ToDo.

1. Nel progetto client, aprire il file **QSTodoService.cs** e aggiungere le seguenti dichiarazioni a QSTodoService:

		// Logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Aggiungere un nuovo metodo **Autenticazione** a **QSTodoService** con la definizione seguente:

        public async Task Authenticate(UIViewController view)
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

> [AZURE.NOTE]Se si usa un provider di identità diverso da Facebook, sostituire il valore passato a **LoginAsync** riportato in precedenza con uno dei seguenti: _MicrosoftAccount_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. Aprire **QSTodoListViewController.cs**. Modificare la definizione del metodo **ViewDidLoad** per rimuovere la chiamata a **RefreshAsync()** verso la fine:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();
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

 
<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Creare un'app per Xamarin iOS]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=July15_HO4-->