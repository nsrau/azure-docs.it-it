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
	ms.date="08/27/2015" 
	ms.author="mahender"/>

# Aggiungere l'autenticazione all'app per Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Questo argomento descrive come autenticare gli utenti di un'app per dispositivi mobili dall'applicazione client. In questa esercitazione si aggiungerà l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato dal servizio App per dispositivi mobili di Azure. Una volta completate l'autenticazione e l'autorizzazione nell'app per dispositivi mobili, verrà visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva dell'app mobile. È anche necessario completare prima l'esercitazione [Creare un'app per Xamarin.Android]. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di autenticazione al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="create-gateway"></a>Creare un gateway del servizio app

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare i servizi app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="4">
<li><p>In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un emulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere al back-end dell'app per dispositivi mobili come utente non autenticato. La tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

Si aggiornerà quindi l'app client per richiedere le risorse dal back-end dell'app per dispositivi mobili con un utente autenticato.

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

    Viene così creato un nuovo metodo per autenticare l'utente. Nell'esempio di codice precedente, l'utente viene autenticato tramite un account di accesso di Facebook. Viene usata una finestra di dialogo per visualizzare l'ID utente una volta completata l'autenticazione.

    > [AZURE.NOTE]Se si usa un provider di identità diverso da Facebook, sostituire il valore passato a **LoginAsync** riportato in precedenza con uno dei seguenti: _MicrosoftAccount_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. Nel metodo **OnCreate** aggiungere la riga di codice seguente dopo il codice che crea l'istanza dell'oggetto `MobileServiceClient`.

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL, Gateway URL and key
		client = new MobileServiceClient (applicationURL, gatewayURL, applicationKey);
		
		await Authenticate(); // Added for authentication

	Questa chiamata avvia il processo di autenticazione e ne attende il completamento in modalità asincrona.


4. In Visual Studio o Xamarin Studio eseguire il progetto client in un dispositivo o un emulatore ed eseguire l'accesso tramite il provider di identità scelto.

   	Dopo aver eseguito l'accesso, verranno visualizzati l'ID di accesso l'elenco di elementi ToDo e sarà possibile aggiornare i dati nell'app.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Creare un'app per Xamarin.Android]: app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=Oct15_HO3-->