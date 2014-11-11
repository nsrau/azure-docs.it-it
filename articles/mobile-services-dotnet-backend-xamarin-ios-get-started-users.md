<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin iOS apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Introduzione all'autenticazione in Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
  <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS">iOS</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/"  title="JavaScript backend">Back-end JavaScript</a></div>

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app][Aggiunta dell'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

## <a name="register"></a>Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un simulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

    L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a>Aggiunta dell'autenticazione all'app

In questa sezione si procederà alla modifica dell'app in modo da visualizzare una schermata di accesso prima dei dati. All'avvio, l'app non si connetterà al servizio mobile e non sarà visualizzato alcun dato. Dopo la prima volta che l'utente esegue il movimento di aggiornamento verrà visualizzata la schermata di accesso. Dopo aver eseguito correttamente l'accesso, verrà visualizzato un elenco di elementi ToDo.

1.  Nel progetto client, aprire il file **QSTodoService.cs** e aggiungere le seguenti dichiarazioni a QSTodoService:

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  Aggiungere un nuovo metodo **Autenticazione** a **QSTodoService** con la definizione seguente:

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
<p>Se si usa un provider di identit&agrave; diverso da un account Facebook, sostituire il valore passato al metodo <strong>LoginAsync</strong> riportato in precedenza con uno dei seguenti: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> o <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Aprire **QSTodoListViewController.cs**. Modificare la definizione del metodo **ViewDidLoad** per rimuovere la chiamata a **RefreshAsync()** verso la fine:

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

4.  Modificare il metodo **RefreshAsync** per autenticare e visualizzare una schermata di accesso se la proprietà **User** è Null. In corrispondenza del codice seguente nella parte superiore della definizione del metodo:

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5.  Fare clic su **Esegui** per compilare il progetto e avviare l'app nel simulatore iPhone. Verificare che nell'app non siano visualizzati dati.

    Eseguire il movimento di aggiornamento spostando verso il basso l'elenco di elementi, in modo da visualizzare la schermata di accesso. Dopo aver correttamente immesso le credenziali valide, verrà visualizzato l'elenco di elementi ToDo e sarà possibile aggiornare i dati nell'app.

<!-- ## <a name="next-steps"> </a>Next steps 
In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.   -->
<!-- Anchors. -->
<!-- URLs. -->

  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
