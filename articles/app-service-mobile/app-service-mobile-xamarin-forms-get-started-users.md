<properties 
	pageTitle="Introduzione all'autenticazione per app per dispositivi mobili nell’app Xamarin.Forms" 
	description="Informazioni su come usare le app per dispositivi mobili per autenticare gli utenti dell'app Xamarin Forms tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="11/30/2015" 
	ms.author="wesmc"/>

# Aggiungere l'autenticazione all'app Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Panoramica

Questo argomento descrive come autenticare gli utenti di un'app mobile del servizio app dall'applicazione client. In questa esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva Xamarin.Forms tramite un provider di identità supportato dal servizio app. In seguito all'autenticazione e all'autorizzazione da parte dell'app per dispositivi mobili, viene visualizzato il valore dell'ID utente e si sarà in grado di accedere ai dati della tabella con restrizioni.

Prima è necessario aver completato l'[esercitazione della guida introduttiva per Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di autenticazione al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).


##Registrare l'app per l'autenticazione e configurare i servizi app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Aggiungere l'autenticazione alla libreria di classi portabile 

Le app per dispositivi mobili usano un metodo `MobileServiceClient.LoginAsync` specifico della piattaforma per visualizzare i dati di interfaccia e di memorizzazione nella cache di accesso. Per eseguire l'autenticazione con un progetto Xamarin Forms si definirà un’interfaccia `IAuthenticate` nella libreria di classi portabile. Ogni piattaforma che si desidera supportare può implementare questa interfaccia nel progetto specifico della piattaforma. Si aggiungerà un codice per l'autenticazione prima di effettuare chiamate in base alla tabella con restrizioni dalla libreria di classi portabile.

1. In Visual Studio o Xamarin Studio, aprire App.cs dal progetto **portabile**. Aggiungere l'istruzione seguente `using` al file.

		using System.Threading.Tasks;

2. In App.cs, aggiungere la seguente definizione dell'interfaccia `IAuthenticate` immediatamente prima della definizione della classe `App`.

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    };

3. In App.cs, aggiungere i seguenti membri static per inizializzare l'interfaccia con un'implementazione specifica della piattaforma.

		public class App : Application
		{
	
	        public static IAuthenticate Authenticator { get; private set; }
	
	        public static void Init(IAuthenticate authenticator)
	        {
	            Authenticator = authenticator;
	        }
	
			...

4. Aprire TodoList.xaml.cs dal progetto **portabile** e aggiornare il metodo `OnAppearing` per l'autenticazione prima di tentare di aggiornare gli elementi dalla tabella.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            if (App.Authenticator != null)
                await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            await RefreshItems(true, syncItems: false);
        }


5. Salvare le modifiche e compilare il progetto del portale senza verificare alcun errore.


##Aggiungere l'autenticazione all'app Android

In questa sezione si aggiungerà l'autenticazione per il progetto droid. Se non si usano dispositivi Android, è possibile ignorare questa sezione.

1. In Visual Studio o Xamarin Studio fare clic con il pulsante destro del mouse sul progetto **droid** e scegliere **Imposta come progetto di avvio**.

2. Andare avanti ed eseguire il progetto nel debugger per verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato). Ciò si verifica perché l'accesso sul back-end è stato limitato ai soli utenti autorizzati.

3. In seguito, aprire MainActivity.cs nel progetto droid e aggiungere l'istruzione `using` seguente.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Aggiornare la classe `MainActivity` per implementare l'interfaccia `IAuthenticate`.

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Aggiornare la classe `MainActivity` aggiungendo un campo `MobileServiceUser` e il metodo `Authenticate` illustrato di seguito per supportare l’interfaccia `IAuthenticate`.
 
	Se si desidera usare un `MobileServiceAuthenticationProvider` diverso al posto di Facebook, apportare anche questa modifica.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                CreateAndShowDialog(string.Format("you are now logged in - {0}",
                    user.UserId), "Logged in!");

                success = true;
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex.Message, "Authentication failed");
            }
            return success;
        }

6. Aggiornare il metodo `OnCreate`della classe `MainActivity` per inizializzare l'autenticatore prima del caricamento dell'app.

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. Ricompilare l'app ed eseguirla. Accedere con il provider di autenticazione selezionato e verificare la possibilità di accedere alla tabella come utente autenticato.



##Aggiungere l'autenticazione all'app iOS

In questa sezione si aggiungerà l'autenticazione per il progetto iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

1. In Visual Studio o Xamarin Studio fare clic con il pulsante destro del mouse sul progetto **iOS** e scegliere **Imposta come progetto di avvio**.

2. Andare avanti ed eseguire il progetto nel debugger per verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato). Ciò si verifica perché l'accesso sul back-end è stato limitato ai soli utenti autorizzati.

3. In seguito, aprire AppDelegate.cs nel progetto iOS e aggiungere l'istruzione `using` seguente.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Aggiornare la classe `AppDelegate` per implementare l'interfaccia `IAuthenticate`.

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. Aggiornare la classe `AppDelegate` aggiungendo un campo `MobileServiceUser` e il metodo `Authenticate` illustrato di seguito per supportare l’interfaccia `IAuthenticate`.
 
	Se si desidera usare un `MobileServiceAuthenticationProvider` diverso al posto di Facebook, apportare anche questa modifica.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                }

                success = true;
            }
            catch (Exception ex)
            {
                UIAlertView avAlert = new UIAlertView("Authentication failed", ex.Message, null, "OK", null);
                avAlert.Show();
            }
            return success;
        }

6. Aggiornare il metodo `FinishedLaunching`della classe `AppDelegate` per inizializzare l'autenticatore prima del caricamento dell'app.

        App.Init((IAuthenticate)this);

		LoadApplication (new App ());



7. Ricompilare l'app ed eseguirla. Accedere con il provider di autenticazione selezionato e verificare la possibilità di accedere alla tabella come utente autenticato.




##Aggiungere l'autenticazione all'app Windows

In questa sezione si aggiungerà l'autenticazione per il progetto WinApp. Se non si usano dispositivi Windows, è possibile ignorare questa sezione.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **WinApp** e quindi scegliere **Imposta come progetto di avvio**.

2. Andare avanti ed eseguire il progetto nel debugger per verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato). Ciò si verifica perché l'accesso sul back-end è stato limitato ai soli utenti autorizzati.

3. In seguito, aprire MainPage.xaml.cs nel progetto WinApp e aggiungere l'istruzione `using` seguente. Sostituire <*Spazio dei nomi della libreria di classi portabile*> con lo spazio dei nomi per la libreria di classi portabile.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Aggiornare la classe `MainPage` per implementare l'interfaccia `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Aggiornare la classe `MainPage` aggiungendo un campo `MobileServiceUser` e il metodo `Authenticate` illustrato di seguito per supportare l’interfaccia `IAuthenticate`.
 
	Se si desidera usare un `MobileServiceAuthenticationProvider` diverso al posto di Facebook, apportare anche questa modifica.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    var messageDialog = new Windows.UI.Popups.MessageDialog(
							string.Format("you are now logged in - {0}", user.UserId), "Authentication");
                    messageDialog.ShowAsync();
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. Aggiornare il costruttore per la classe `MainPage` per inizializzare l'autenticatore prima del caricamento dell'app. Sostituire <*Spazio dei nomi della libreria di classi portabile*> con lo spazio dei nomi per la libreria di classi portabile.

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init((IAuthenticate)this);
            
            LoadApplication(new WesmcMobileAppGaTest.App());
        }



7. Ricompilare l'app ed eseguirla. Accedere con il provider di autenticazione selezionato e verificare la possibilità di accedere alla tabella come utente autenticato.




<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1203_2015-->