<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="mobile-services" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect

<div class="dev-center-tutorial-selector sublanding">

[Windows Store C#][Windows Store C#][Windows Store JavaScript][Windows Store JavaScript][Windows Phone][Windows Phone]

</div>

Questo argomento illustra come usare l'accesso Single Sign-On di Live Connect per autenticare gli utenti in Servizi mobili di Azure da un'app di Windows Store o per Windows Phone 8.1. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Live Connect. Dopo essere stato autenticato da Live Connect e avere eseguito l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.

> [WACOM.NOTE]Questa esercitazione illustra i vantaggi dell'ambiente di accesso Single Sign-On fornito da Live Connect per le app di Windows Store. che semplifica l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. Per un ambiente di autenticazione più generalizzato che supporta più provider di autenticazione, vedere l'argomento [Introduzione all'autenticazione][Introduzione all'autenticazione].

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione Live Connect:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app][Aggiunta dell'autenticazione all'app]

Per completare questa esercitazione, è necessario disporre di:

-   [Live SDK per Windows][Live SDK per Windows]
-   Microsoft Visual Studio 2012 Express per Windows 8 RC o versione successiva

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

> [WACOM.NOTE]Nell'esercitazione viene usato un servizio mobile back-end JavaScript. L'autenticazione gestita da client tramite Live Connect o altri client di autenticazione non è ancora supportata in un servizio mobile back-end .NET.

## <a name="register"></a>Registrazione dell'app di Windows Store

Per poter autenticare gli utenti, è necessario inviare l'app a Windows Store. È quindi necessario registrare il segreto client per l'integrazione di Live Connect con Servizi mobili.

[WACOM.INCLUDE [mobile-services-register-windows-store-app][mobile-services-register-windows-store-app]]

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili][1].

2.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a> Aggiunta dell'autenticazione all'app

1.  Scaricare e installare [Live SDK per Windows][Live SDK per Windows].

2.  Nel menu **Progetto** in Visual Studio fare clic su **Aggiungi riferimento**, espandere **Windows**, fare clic su **Estensioni**, selezionare **Live SDK**, quindi fare clic su **OK**.

    ![][]

    Verrà aggiunto un riferimento a Live SDK al progetto.

3.  Aprire il file del progetto mainpage.xaml.cs e aggiungere l'istruzione using seguente:

        using Microsoft.Live;        

4.  Aggiungere il seguente frammento di codice alla classe MainPage:

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

    > [WACOM.NOTE]Evitare di richiedere token di autenticazione di Live Connection o Servizi mobili ogni volta che viene eseguita l'app. Oltre a essere inefficace, questo metodo può comportare problemi di utilizzo nel caso in cui molti clienti provino ad avviare simultaneamente l'app. Un metodo migliore è quello di memorizzare i token nella cache e provare a usare il token di Servizi mobili nella cache prima di chiamare **LoginWithMicrosoftAccountAsync**. Per un esempio di come memorizzare nella cache questo token, vedere [Introduzione all'autenticazione][2].

5.  Aggiornare la stringa *\<\< INSERT REDIRECT DOMAIN HERE \>\>* del passaggio precedente con il dominio di reindirizzamento specificato durante la configurazione dell'app in Live Connect, nel formato **[https://\_nome-servizio\_.azure-mobile.net/][https://\_nome-servizio\_.azure-mobile.net/]**.

    <div class="dev-callout">

    **Nota**
    In un'app di Windows Store viene creata un'istanza della classe **LiveAuthClient** passando il valore dell'URI del dominio di reindirizzamento al costruttore della classe. In un'[app per Windows Phone 8][app per Windows Phone 8] viene creata un'istanza della stessa classe passando l'ID client.

    </div>

6.  Sostituire il gestore di eventi **OnNavigatedTo** esistente con il gestore che chiama il nuovo metodo **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }

7.  Premere F5 per eseguire l'app e accedere a Live Connect con l'account Microsoft.

Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script][Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come utilizzare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione][3]. Per ulteriori informazioni su come utilizzare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili][Riferimento per i concetti e le procedure di .NET per Servizi mobili]

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows Store JavaScript"
  [Windows Phone]: /it-it/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone"
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /it-it/documentation/articles/mobile-services-windows-store-get-started
  []: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png
  [2]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/#tokens
  [https://\_nome-servizio\_.azure-mobile.net/]: https://_service-name_.azure-mobile.net/
  [app per Windows Phone 8]: /it-it/develop/mobile/tutorials/single-sign-on-wp8/
  [Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
  [3]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
  [Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
