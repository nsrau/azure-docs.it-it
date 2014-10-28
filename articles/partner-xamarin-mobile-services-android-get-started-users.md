<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-android" urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="Get started with authentication (Xamarin.Android) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.Android" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" services="mobile-services" documentationCenter="Mobile" manager="dwrede" authors="donnam"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam"></tags>

# Introduzione all'autenticazione in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app di Xamarin.Android. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][]
3.  [Aggiunta dell'autenticazione all'app][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][].

Per completare questa esercitazione, è necessario disporre di Xamarin.Android e di Android SDK 4.2 o versione successiva.

## <a name="register"></a><span class="short-header">Registrazione dell'app</span>Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a><span class="short-header">Limitazione delle autorizzazioni</span>Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  In Eclipse aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili][].

2.  Nel menu **Run** fare clic su **Run** per eseguire l'app e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a><span class="short-header">Aggiunta dell'autenticazione</span>Aggiunta dell'autenticazione all'app

1.  Aggiungere la proprietà seguente alla classe **TodoActivity**:

            private MobileServiceUser user;

2.  Aggiungere il metodo seguente alla classe **TodoActivity**:

            private async Task Authenticate()
            {
                try
                {
                    user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
            }

    In questo modo viene creato un nuovo metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso Microsoft. Viene visualizzata una finestra di dialogo che riporta l'ID dell'utente autenticato. Senza un'autenticazione positiva non è possibile procedere.

    <div class="dev-callout"><b>Nota</b>
<p>Se si utilizza un provider di identit&agrave; diverso da Microsoft, sostituire il valore passato al metodo <strong>login</strong> riportato in precedenza con uno dei seguenti: <i>Facebook</i>, <i>Google</i>, <i>Twitter</i> o <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Nel metodo **OnCreate** aggiungere la riga di codice seguente dopo il codice che crea l'istanza dell'oggetto `MobileServiceClient`.

        await Authenticate();

    Questa chiamata avvia il processo di autenticazione e ne attende il completamento in modalità asincrona.

4.  Spostare il codice rimanente dopo `await Authenticate();` nel metodo **OnCreate** in un nuovo metodo **CreateTable**, simile al seguente:

            private async Task CreateTable()
            {
                // Get the Mobile Service Table instance to use
                todoTable = client.GetTable<TodoItem>();

                textNewTodo = FindViewById<EditText>(Resource.Id.textNewTodo);

                // Create an adapter to bind the items with the view
                adapter = new TodoItemAdapter(this, Resource.Layout.Row_List_To_Do);
                var listViewTodo = FindViewById<ListView>(Resource.Id.listViewTodo);
                listViewTodo.Adapter = adapter;

                // Load the items from the Mobile Service
                await RefreshItemsFromTableAsync();
            }

5.  Chiamare quindi il nuovo metodo **CreateTable** in **OnCreate** dopo la chiamata **Authenticate** aggiunta nel passaggio 2:

        await CreateTable();

6.  Nel menu **Run** fare clic su **Run** per avviare l'app e accedere con il provider di identità scelto.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## Download dell'esempio completato

Scaricare il [progetto di esempio completato][]. Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script][], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-android
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [progetto di esempio completato]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
