<properties 
	pageTitle="Introduzione all'autenticazione per app per dispositivi mobili in Xamarin Android" 
	description="Informazioni su come usare le app per dispositivi mobili per autenticare gli utenti dell'app per Xamarin Android tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/23/2015" 
	ms.author="mahender"/>

# Aggiungere l'autenticazione all'app per Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Questo argomento descrive come autenticare gli utenti di un'app mobile del servizio app dall'applicazione client. In questa esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato dal servizio app. In seguito all'autenticazione e all'autorizzazione da parte dell'app mobile, viene visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva dell'app mobile. È anche necessario completare prima l'esercitazione [Creare un'app per Xamarin.Android].

##<a name="review"></a>Verificare la configurazione del progetto server (facoltativo)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare i servizi app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="5">
<li><p>In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un emulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere al codice dell'app per dispositivi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

1. Aggiungere la proprietà seguente alla classe **TodoActivity**:

			private MobileServiceUser user;

2. Aggiungere il metodo seguente alla classe **TodoActivity**:

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

    > [AZURE.NOTE]Se si usa un provider di identità diverso da Google, sostituire il valore passato a **LoginAsync** riportato in precedenza con uno dei seguenti: _MicrosoftAccount_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. Nel metodo **OnCreate** aggiungere la riga di codice seguente dopo il codice che crea l'istanza dell'oggetto `MobileServiceClient`.

		// Get the Mobile App Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	Questa chiamata avvia il processo di autenticazione e ne attende il completamento in modalità asincrona.


4. Nel menu **Run** fare clic su **Run** per avviare l'app e accedere con il provider di identità scelto.

   	Dopo aver correttamente eseguito l'accesso, verrà visualizzato l'elenco di elementi ToDo e sarà possibile aggiornare i dati nell'app.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Creare un'app per Xamarin.Android]: app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=August15_HO6-->