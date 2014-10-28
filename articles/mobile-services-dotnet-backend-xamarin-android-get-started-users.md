<properties linkid="mobile-services-dotnet-backend-xamarin-android-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin Android apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin Android apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Introduzione all'autenticazione in Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="iOS">iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android" class="current">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/"  title="JavaScript backend">Back-end JavaScript</a></div>

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app][Aggiunta dell'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

## <a name="register"></a>Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a>Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

1.  In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un simulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

    L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a>Aggiunta dell'autenticazione all'app

1.  Aggiungere la seguente proprietà alla classe **TodoActivity**:

            private MobileServiceUser user;

2.  Aggiungere il metodo seguente alla classe **TodoActivity**:

            private async Task Authenticate()
            {
                try
                {
                    user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
            }

    In questo modo viene creato un nuovo metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Facebook. Viene visualizzata una finestra di dialogo che riporta l'ID dell'utente autenticato.

    <div class="dev-callout"><b>Nota</b>
<p>Se si usa un provider di identit&agrave; diverso da un account Facebook, sostituire il valore passato al metodo <strong>LoginAsync</strong> riportato in precedenza con uno dei seguenti: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> o <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Nel metodo **OnCreate** aggiungere la riga di codice seguente dopo il codice che crea l'istanza dell'oggetto `MobileServiceClient`.

        // Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

    Questa chiamata avvia il processo di autenticazione e ne attende il completamento in modalità asincrona.

4.  Nel menu **Run** fare clic su **Run** per avviare l'app e accedere con il provider di identità scelto.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

<!-- ## <a name="next-steps"> </a>Next steps 
In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.   -->
<!-- Anchors. -->
<!-- URLs. -->

  [Windows Store C#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "iOS"
  [Android]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "Android"
  [Xamarin.iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users "Xamarin.iOS"
  [Xamarin.Android]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users "Xamarin.Android"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/ ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/ "JavaScript backend"
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
