<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile"  services="mobile-services" title="Get started with authentication in Mobile Services" manager="dwrede" authors="donnam" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam"></tags>

# Introduzione all'autenticazione in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][]
3.  [Aggiunta dell'autenticazione all'app][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][].

Per completare questa esercitazione, è necessario disporre di [Xamarin.iOS], XCode 5.0 e iOS 5.0 o versioni successive.

## <a name="register"></a><span class="short-header">Registrazione dell'app</span>Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a><span class="short-header">Limitazione delle autorizzazioni</span>Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  In Xcode aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili][].

2.  Premere il pulsante **Run** per compilare il progetto e avviare l'app nell'emulatore iPhone e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a><span class="short-header">Aggiunta dell'autenticazione</span>Aggiunta dell'autenticazione all'app

1.  Aprire il file del progetto **TodoService** e aggiungere le variabili seguenti:

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  Aggiungere quindi a **TodoService** un nuovo metodo denominato **Authenticate** definito come segue:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout"><b>Nota</b>
<p>Se si utilizza un provider di identit&agrave; diverso da un account Microsoft, sostituire il valore passato al metodo <strong>LoginAsync</strong> riportato in precedenza con uno dei seguenti: <i>Facebook</i>, <i>Twitter</i>, <i>Google</i> o <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Spostare la richiesta per la tabella **TodoItem** dal costruttore **TodoService** a un nuovo metodo denominato **CreateTable**:

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }

4.  Creare un nuovo metodo pubblico asincrono denominato **LoginAndGetData** definito come segue:

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5.  In **TodoListViewController** eseguire l'override del metodo **ViewDidAppear** e definirlo come illustrato di seguito. Questo consente l'accesso dell'utente se **TodoService** non dispone già di un handle per l'utente:

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (TodoService.DefaultService.User == null)
            {
                await TodoService.DefaultService.LoginAndGetData(this);
            }

            if (TodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            } 

            RefreshAsync();
        }

6.  Rimuovere la chiamata originale a **RefreshAsync** da **TodoListViewController.ViewDidLoad**.

7.  Premere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## Download dell'esempio completato

Scaricare il [progetto di esempio completato][]. Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script][], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. TODO:: update completed example project link with project download -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-ios
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [progetto di esempio completato]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
