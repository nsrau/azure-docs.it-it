<properties 
	pageTitle="Autenticare l'app di Windows Store con Live Connect" 
	description="Informazioni su come usare l'accesso Single Sign-On di Live Connect in Servizi mobili di Azure da un'applicazione di Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

# Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	

Questo argomento descrive come usare l'accesso Single Sign-On di Live Connect per autenticare gli utenti in Servizi mobili di Azure da un'app di Windows Store o per Windows Phone 8.1.  Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Live Connect. Dopo essere stato autenticato da Live Connect e avere eseguito l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.  

>[AZURE.NOTE] In questa esercitazione vengono descritti i vantaggi offerti dall'ambiente di accesso Single Sign-On per le app di Windows Store, che semplifica l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. Per un ambiente di autenticazione più generalizzato che supporta più provider di autenticazione, vedere l'argomento <a href="mobile-services-windows-store-dotnet-get-started-users.md/">Introduzione all'autenticazione</a>. 

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione Live Connect:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]

Per completare questa esercitazione, è necessario disporre di:

+ [Live SDK per Windows]
+ Microsoft Visual Studio 2012 Express per Windows 8 RC o versione successiva
+ Prima è necessario completare anche l'esercitazione [Aggiungere Servizi mobili a un'app esistente].

##<a name="register"></a>Registrare l'app di Windows Store

Per poter autenticare gli utenti, è necessario inviare l'app a Windows Store. È quindi necessario registrare il segreto client per l'integrazione di Live Connect con Servizi mobili.

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione <a href="/documentation/articles/mobile-services-windows-store-get-started">Introduzione a Servizi mobili</a>.</p></li> 
<li><p>Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

1. Scaricare e installare [Live SDK per Windows].

2. Nel menu **Progetto** in Visual Studio fare clic su **Aggiungi riferimento**, espandere **Windows**, fare clic su **Estensioni**, selezionare **Live SDK**, quindi fare clic su **OK**. 

  	![][16]

  	Verrà aggiunto un riferimento a Live SDK al progetto.

5. Aprire il file del progetto mainpage.xaml.cs e aggiungere l'istruzione using seguente:

        using Microsoft.Live;        

6. Aggiungere il seguente frammento di codice alla classe MainPage:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");

            while (session == null)
            {
                // Force a logout to make it easier to test with multiple Microsoft Accounts
                if (liveIdClient.CanLogout)
                    liveIdClient.Logout();
	
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);
	
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
         }

    Verranno creati una variabile membro per archiviare la sessione Live Connect corrente e un metodo per gestire il processo di autenticazione. Questo codice forza una disconnessione, ove possibile, per garantire che all'utente vengano richieste le credenziali ogni volta che l'applicazione viene eseguita, al fine di semplificare il test dell'applicazione con account Microsoft diversi e verificare che l'autenticazione funzioni correttamente. Questo meccanismo funziona solo se l'utente che ha eseguito l'accesso non dispone di un account Microsoft connesso. 

	>[AZURE.NOTE] Evitare di richiedere token di autenticazione di Live Connection o Servizi mobili ogni volta che viene eseguita l'app. Oltre a essere inefficace, questo metodo può comportare problemi di uso nel caso in cui molti clienti provino ad avviare simultaneamente l'app. Un metodo migliore è quello di memorizzare i token nella cache e provare a usare il token di Servizi mobili nella cache prima di chiamare **LoginWithMicrosoftAccountAsync**. Per un esempio relativo a come memorizzare questo token nella cache, vedere [Introduzione all'autenticazione](mobile-services-windows-store-dotnet-get-started-users.md#tokens)
	

7. Aggiornare la stringa _<< INSERT REDIRECT DOMAIN HERE >>_ del passaggio precedente con il dominio di reindirizzamento specificato durante la configurazione dell'app in Live Connect, nel formato **https://_service-name_.azure-mobile.net/**.

    > [AZURE.NOTE] In un'app di Windows Store viene creata un'istanza della classe <strong>LiveAuthClient</strong> passando il valore dell'URI del dominio di reindirizzamento al costruttore di classe. In un'[app di Windows Phone 8](/develop/mobile/tutorials/single-sign-on-wp8/)viene creata un'istanza della stessa classe passando l'ID client.

8. Sostituire il gestore eventi **OnNavigatedTo** esistente con il gestore che chiama il nuovo metodo **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
9. Premere F5 per eseguire l'app e accedere a Live Connect con l'account Microsoft. 

   Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzare gli utenti con gli script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come usare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione]. Per altre informazioni su come usare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili]

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png

<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Aggiungere Servizi mobili a un'app esistente]: mobile-services-windows-store-dotnet-get-started-data.md
[Introduzione all'autenticazione]: mobile-services-windows-store-dotnet-get-started-users.md
[Autorizzare gli utenti con gli script]: mobile-services-windows-store-dotnet-authorize-users-in-scripts.md

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=49-->