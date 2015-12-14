<properties
	pageTitle="Introduzione all'autenticazione (Xamarin.iOS) - Servizi mobili"
	description="Informazioni su come usare l'autenticazione nell'app Servizi mobili di Azure per Xamarin.iOS."
	documentationCenter="xamarin"
	services="mobile-services"
	manager="dwrede"
	authors="lindydonna"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015"
	ms.author="donnam"/>

# Aggiungere l'autenticazione all'app di Servizi mobili

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

Questa esercitazione descrive le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili].

Per completare questa esercitazione, è necessario disporre di [Xamarin.iOS], XCode 6.0 e iOS 7.0 o versioni successive.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. In Xcode aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili].

4. Premere il pulsante **Run** per compilare il progetto e avviare l'app nell'emulatore iPhone e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

   	L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

1. Aprire il file del progetto **ToDoService** e aggiungere le variabili seguenti:

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. Aggiungere quindi a **ToDoService** un nuovo metodo denominato **Authenticate** e definito come segue:

        private async Task Authenticate(MonoTouch.UIKit.UIViewController view)
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

	> [AZURE.NOTE]Se si usa un provider di identità diverso da un account Microsoft, sostituire il valore passato a **LoginAsync** riportato in precedenza con uno dei seguenti: _Facebook_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. Spostare la richiesta per la tabella **ToDoItem** dal costruttore **ToDoService** a un nuovo metodo denominato **CreateTable**:

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the ToDoItem table
            todoTable = client.GetSyncTable<ToDoItem>();
        }

4. Creare un nuovo metodo pubblico asincrono denominato **LoginAndGetData** definito come segue:

        public async Task LoginAndGetData(MonoTouch.UIKit.UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. In **TodoListViewController** eseguire l'override del metodo **ViewDidAppear** e definirlo come illustrato di seguito. Questo consente l'accesso dell'utente se **ToDoService** non dispone già di un handle per l'utente:

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (QSToDoService.DefaultService.User == null)
            {
                await QSToDoService.DefaultService.LoginAndGetData(this);
            }

            if (QSToDoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            }

            RefreshAsync();
        }
6. Rimuovere la chiamata originale a **RefreshAsync** da **TodoListViewController.ViewDidLoad**.

7. Premere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto.

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## Download dell'esempio completato
Scaricare il [progetto di esempio completato]. Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[Autorizzazione di utenti con script]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[progetto di esempio completato]: http://go.microsoft.com/fwlink/p/?LinkId=331328
[Xamarin.iOS]: http://xamarin.com/download

<!---HONumber=AcomDC_1203_2015-->