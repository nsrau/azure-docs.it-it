<properties urlDisplayName="Get Started with authentication in Mobile Services for Xamarin Android apps" pageTitle="Introduzione all'autenticazione in Servizi mobili per app per Xamarin Android - Servizi mobili di Azure" metaKeywords="" description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per Xamarin Android tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Introduzione all'autenticazione in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un simulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

1. Aggiungere la seguente proprietà alla classe **TodoActivity**:

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

    <div class="dev-callout"><b>Nota</b>
	<p>Se si usa un provider di identità diverso da un account Facebook, sostituire il valore passato al metodo <strong>LoginAsync</strong> riportato in precedenza con uno dei seguenti: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> o <i>WindowsAzureActiveDirectory</i>.</p>
    </div>

3. Nel metodo **OnCreate** aggiungere la riga di codice seguente dopo il codice che crea l'istanza dell'oggetto `MobileServiceClient`.

		// Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	Questa chiamata avvia il processo di autenticazione e ne attende il completamento in modalità asincrona.


4. Nel menu **Esegui** fare clic su **Esegui** per avviare l'app e accedere con il provider di identità scelto. 

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.


<!-- ## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzare gli utenti con gli script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. 
 -->
 
<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Passaggi successivi]:#next-steps


<!-- URLs. -->
[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push/
[Autorizzare gli utenti con gli script]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript e HTML]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
