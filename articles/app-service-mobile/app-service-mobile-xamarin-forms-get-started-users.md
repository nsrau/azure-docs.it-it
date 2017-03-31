---
title: Introduzione all&quot;autenticazione per app per dispositivi mobili nell&quot;app Xamarin.Forms | Documentazione Microsoft
description: "Informazioni su come usare le app per dispositivi mobili per autenticare gli utenti dell&quot;app Xamarin Forms tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 30fac48cbacb26b03ce430987997c38c68368385
ms.lasthandoff: 03/01/2017


---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Aggiungere l'autenticazione all'app Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Overview
Questo argomento descrive come autenticare gli utenti di un'app mobile del servizio app dall'applicazione client. In questa esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva Xamarin.Forms tramite un provider di identità supportato dal servizio app. Dopo l'autenticazione e l'autorizzazione da parte dell'app per dispositivi mobili, viene visualizzato il valore dell'ID utente e si potrà accedere ai dati della tabella con restrizioni.

## <a name="prerequisites"></a>Prerequisiti
Per ottenere risultati ottimali con questa esercitazione, è consigliabile completare prima di tutto l'esercitazione [Creare un'app Xamarin.Forms][1]. Al termine di questa esercitazione, sarà disponibile un progetto Xamarin.Forms che corrisponde a un'app TodoList multipiattaforma.

Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di autenticazione al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrare l'app per l'autenticazione e configurare i servizi app
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="restrict-permissions-to-authenticated-users"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Aggiungere l'autenticazione alla libreria di classi portabile
L'app per dispositivi mobili usa il metodo di estensione [LoginAsync][3] in [MobileServiceClient][4] per eseguire l'accesso di un utente con l'autenticazione del servizio app. Questo esempio usa un flusso di autenticazione gestito dal server che mostra l'interfaccia di accesso del provider nell'app. Per altre informazioni, vedere [Autenticazione gestita dal server][5]. Per offrire un'esperienza utente migliore nell'app di produzione, è consigliabile prendere invece in considerazione l'uso dell'[autenticazione gestita dal client][6].

Per eseguire l'autenticazione con un progetto Xamarin.Forms, definire un'interfaccia **IAuthenticate** nella libreria di classi portabile per l'app. Aggiungere quindi un pulsante di **accesso** all'interfaccia utente definita nella libreria di classi portabile, su cui fare clic per avviare l'autenticazione. Al completamento dell'autenticazione, i dati vengono caricati dal back-end dell'app per dispositivi mobili.

Implementare l'interfaccia **IAuthenticate** per ogni piattaforma supportata dall'app.

1. In Visual Studio o Xamarin Studio aprire il file App.cs dal progetto che include **Portable** nel nome, che corrisponde al progetto di libreria di classi portabile, quindi aggiungere l'istruzione `using` seguente:
   
        using System.Threading.Tasks;
2. In App.cs aggiungere la definizione dell'interfaccia `IAuthenticate` seguente immediatamente prima della definizione della classe `App`.
   
        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Per inizializzare l'interfaccia con un'implementazione specifica per la piattaforma, aggiungere i membri statici seguenti alla classe **App**.
   
        public static IAuthenticate Authenticator { get; private set; }
   
        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Aprire il file TodoList.xaml dal progetto della libreria di classi portabile, aggiungere l'elemento **Button** seguente nell'elemento di layout *buttonsPanel* , dopo il pulsante esistente:
   
          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>
   
    Questo pulsante attiva l'autenticazione gestita dal server con il back-end dell'app per dispositivi mobili.
5. Aprire il file TodoList.xaml.cs dal progetto della libreria di classi portabile, quindi aggiungere il campo seguente alla classe `TodoList` :
   
        // Track whether the user has authenticated.
        bool authenticated = false;
6. Sostituire il metodo **OnAppearing** con il codice seguente:
   
        protected override async void OnAppearing()
        {
            base.OnAppearing();
   
            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);
   
                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }
   
    Questo codice fa in modo che i dati vengano aggiornati dal servizio solo dopo l'autenticazione dell'utente.
7. Aggiungere il gestore seguente per l'evento **Clicked** alla classe **TodoList**:
   
        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();
   
            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Salvare le modifiche e ricompilare il progetto della libreria di classi portabile, senza che si verifichino errori.

## <a name="add-authentication-to-the-android-app"></a>Aggiungere l'autenticazione all'app Android
Questa sezione illustra come implementare l'interfaccia **IAuthenticate** nel progetto di app Android. Se i dispositivi Android non sono supportati, ignorare questa sezione.

1. In Visual Studio o Xamarin Studio fare clic con il pulsante destro del mouse sul progetto **droid**, quindi scegliere **Imposta come progetto di avvio**.
2. Premere F5 per avviare il progetto nel debugger, quindi verificare che venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato) dopo l'avvio dell'app. La risposta 401 viene generata perché l'accesso al back-end è limitato solo agli utenti autorizzati.
3. Aprire il file MainActivity.cs nel progetto Android e aggiungere le istruzioni `using` seguenti:
   
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aggiornare la classe **MainActivity** per implementare l'interfaccia **IAuthenticate**, come illustrato di seguito:
   
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Aggiornare la classe **MainActivity** aggiungendo un campo **MobileServiceUser** e un metodo **Authenticate**, necessario per l'interfaccia **IAuthenticate**, come illustrato di seguito:
   
        // Define a authenticated user.
        private MobileServiceUser user;
   
        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }
   
            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();
   
            return success;
        }

    Se si usa un provider di identità diverso da Facebook, scegliere un valore diverso per [MobileServiceAuthenticationProvider][7].

1. Aggiungere il codice seguente al metodo **OnCreate** della classe **MainActivity** prima della chiamata a `LoadApplication()`:
   
        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);
   
    Questo codice fa in modo che l'autenticatore venga inizializzato prima del caricamento dell'app.
2. Ricompilare l'app, eseguirla, quindi accedere con il provider di autenticazione selezionato e verificare se è possibile accedere ai dati come utente autenticato.

## <a name="add-authentication-to-the-ios-app"></a>Aggiungere l'autenticazione all'app iOS
Questa sezione illustra come implementare l'interfaccia **IAuthenticate** nel progetto di app iOS. Se i dispositivi iOS non sono supportati, ignorare questa sezione.

1. In Visual Studio o Xamarin Studio fare clic con il pulsante destro del mouse sul progetto **iOS**, quindi scegliere **Imposta come progetto di avvio**.
2. Premere F5 per avviare il progetto nel debugger, quindi verificare che venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato) dopo l'avvio dell'app. La risposta 401 viene generata perché l'accesso al back-end è limitato solo agli utenti autorizzati.
3. Aprire il file AppDelegate.cs nel progetto iOS e aggiungere le istruzioni `using` seguenti:
   
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aggiornare la classe **AppDelegate** per implementare l'interfaccia **IAuthenticate**, come illustrato di seguito:
   
        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Aggiornare la classe **AppDelegate** aggiungendo un campo **MobileServiceUser** e un metodo **Authenticate**, necessario per l'interfaccia **IAuthenticate**, come illustrato di seguito:
   
        // Define a authenticated user.
        private MobileServiceUser user;
   
        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }
   
            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();
   
            return success;
        }
   
    Se si usa un provider di identità diverso da Facebook, scegliere un valore diverso per [MobileServiceAuthenticationProvider].
6. Aggiungere la riga di codice seguente al metodo **FinishedLaunching** prima della chiamata a `LoadApplication()`:
   
        App.Init(this);
   
    Questo codice fa in modo che l'autenticatore venga inizializzato prima del caricamento dell'app.
7. Ricompilare l'app, eseguirla, quindi accedere con il provider di autenticazione selezionato e verificare se è possibile accedere ai dati come utente autenticato.

## <a name="add-authentication-to-windows-81-including-phone-app-projects"></a>Aggiungere l'autenticazione a progetti di app Windows 8.1 e Windows Phone 8.1
Questa sezione illustra come implementare l'interfaccia **IAuthenticate** nei progetti di app per Windows 8.1 e Windows Phone 8.1. La stessa procedura si applica ai progetti piattaforma UWP (Universal Windows Platform), ma usando il progetto **UWP** con le modifiche indicate. Se i dispositivi Windows non sono supportati, ignorare questa sezione.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **WinApp** o **WinPhone81**, quindi scegliere **Imposta come progetto di avvio**.
2. Premere F5 per avviare il progetto nel debugger, quindi verificare che venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato) dopo l'avvio dell'app. La risposta 401 viene generata perché l'accesso al back-end è limitato solo agli utenti autorizzati.
3. Aprire il file MainPage.xaml.cs per il progetto di app Windows, quindi aggiungere le istruzioni `using` seguenti:
   
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;
   
    Sostituire `<your_Portable_Class_Library_namespace>` con lo spazio dei nomi per la libreria di classi portabile.
4. Aggiornare la classe **MainPage** per implementare l'interfaccia **IAuthenticate**, come illustrato di seguito:
   
        public sealed partial class MainPage : IAuthenticate
5. Aggiornare la classe **MainPage** aggiungendo un campo **MobileServiceUser** e un metodo **Authenticate**, necessario per l'interfaccia **IAuthenticate**, come illustrato di seguito:
   
        // Define a authenticated user.
        private MobileServiceUser user;
   
        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;
   
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
   
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }
   
            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();
   
            return success;
        }

    Se si usa un provider di identità diverso da Facebook, scegliere un valore diverso per [MobileServiceAuthenticationProvider].

1. Aggiungere la riga di codice seguente nel costruttore per la classe **MainPage** prima della chiamata a `LoadApplication()`:
   
        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
   
    Sostituire `<your_Portable_Class_Library_namespace>` con lo spazio dei nomi per la libreria di classi portabile.
   
    Se si modifica il progetto WinApp, andare direttamente al passaggio 8. Il passaggio successivo è applicabile solo al progetto WinPhone81, in cui è necessario completare il callback di accesso.
2. (Facoltativo) Nel progetto **WinPhone81** aprire il file App.xaml.cs e quindi aggiungere le istruzioni `using` seguenti:
   
        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;
   
    Sostituire `<your_Portable_Class_Library_namespace>` con lo spazio dei nomi per la libreria di classi portabile.
3. Se si usa **WinPhone81** o **WinApp**, aggiungere l'override del metodo **OnActivated** seguente alla classe **App**:
   
       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);
   
           // We just need to handle activation that occurs after web authentication.
           if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
           {
               // Get the client and call the LoginComplete method to complete authentication.
               var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
               client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
           }
       }
   
   Se l'override del metodo esiste già, aggiungere il codice condizionale del frammento di codice precedente.  Questo codice non è necessario per i progetti Universal Windows.
4. Ricompilare l'app, eseguirla, quindi accedere con il provider di autenticazione selezionato e verificare se è possibile accedere ai dati come utente autenticato.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato questa esercitazione sull'autenticazione di base, provare a continuare fino a una delle esercitazioni seguenti:

* [Aggiungere notifiche push all'app Xamarin.Forms](app-service-mobile-xamarin-forms-get-started-push.md)
  
  : informazioni su come aggiungere il supporto per le notifiche push all'app e configurare il back-end dell'app per dispositivi mobili per usare Hub di notifica di Azure per l'invio di notifiche push.
* [Abilitare la sincronizzazione offline per l'app per dispositivi mobili Xamarin.Forms](app-service-mobile-xamarin-forms-get-started-offline-data.md)
  
  Informazioni su come aggiungere il supporto offline all'app usando il back-end di un'app per dispositivi mobili. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

