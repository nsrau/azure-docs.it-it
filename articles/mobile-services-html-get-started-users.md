<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"></tags>

# Introduzione all'autenticazione in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app HTML o PhoneGap. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][]
3.  [Aggiunta dell'autenticazione all'app][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][].

## <a name="register"></a> Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  Nella directory dell'app avviare uno dei file di comando seguenti dalla sottocartella **server**.

    -   **launch-windows** (computer Windows)
    -   **launch-mac.command** (computer Mac OS X)
    -   **launch-linux.sh** (computer Linux)

    > [WACOM.NOTE]In un computer Windows digitare `R` quando PowerShell chiede di confermare che si desidera eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.

    Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

2.  Aprire l'URL <http://localhost:8000/> in un Web browser per avviare l'app.

    Il caricamento dei dati ha esito negativo. L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

3.  (Facoltativo) Aprire il debugger di script per il Web browser in uso e ricaricare la pagina. Verificare che venga restituito un errore di accesso negato.

A questo punto, si aggiornerà l'app in modo da consentire l'autenticazione prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a> Aggiunta dell'autenticazione all'app

> [WACOM.NOTE]Poiché l'accesso viene eseguito in una finestra popup, richiamare il metodo **login** da un evento clic del pulsante. In caso contrario, in molti browser non sarà visualizzata la finestra di accesso.

1.  Aprire il file di progetto, individuare l'elemento H1 e sotto di esso aggiungere il frammento di codice seguente:

        <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

    Sarà quindi possibile accedere a Servizi mobili dalla pagina.

2.  Nel file app.js individuare la riga di codice alla fine del file in cui viene chiamata la funzione refreshTodoItems e sostituirla con il codice seguente:

        function refreshAuthDisplay() {
            var isLoggedIn = client.currentUser !== null;
            $("#logged-in").toggle(isLoggedIn);
            $("#logged-out").toggle(!isLoggedIn);

            if (isLoggedIn) {
                $("#login-name").text(client.currentUser.userId);
                refreshTodoItems();
            }
        }

        function logIn() {
            client.login("facebook").then(refreshAuthDisplay, function(error){
                alert(error);
            });
        }

        function logOut() {
            client.logout();
            refreshAuthDisplay();
            $('#summary').html('<strong>You must login to access data.</strong>');
        }

        // On page init, fetch the data and set up event handlers
        $(function () {
            refreshAuthDisplay();
            $('#summary').html('<strong>You must login to access data.</strong>');          
            $("#logged-out button").click(logIn);
            $("#logged-in button").click(logOut);
        });

    In questo modo viene creato set di funzioni per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Facebook. Se si utilizza un provider di identità diverso da Facebook, sostituire il valore passato al metodo **login** riportato in precedenza con uno dei seguenti: *microsoftaccount*, *facebook*, *twitter*, *google* o *aad*.

    > [WACOM.NOTE]In un'app PhoneGap è inoltre necessario aggiungere al progetto i plug-in seguenti:
    >
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git`
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git`
    >
    > </p>

3.  Tornare al browser in cui è in esecuzione l'app e aggiornare la pagina.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

    > [WACOM.NOTE]Se si usa Internet Explorer, dopo l'accesso può essere visualizzato un errore: `Cannot reach window opener. It may be on a different Internet Explorer zone`. Questo errore si verifica perché la finestra popup viene eseguita in un'area di sicurezza (Internet) diversa da localhost (Intranet). Questo errore interessa le app solo durante lo sviluppo tramite localhost. Per risolvere il problema, in **Opzioni Internet** aprire la scheda **Sicurezza**, fare clic su **Intranet locale**, quindi su **Siti** e disabilitare **Rileva automaticamente rete Intranet**. Ricordare di tornare a modificare questa opzione al termine del test.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script][], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per ulteriori informazioni su come utilizzare Servizi mobili con HTML/JavaScript, vedere [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript][]

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-html-get-started
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [Autorizzazione di utenti con script]: /it-it/documentation/articles/mobile-services-html-authorize-users-in-scripts
  [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library
