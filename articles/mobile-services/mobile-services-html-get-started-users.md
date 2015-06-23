<properties 
	pageTitle="Introduzione all'autenticazione (HTML 5) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app HTML tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app di Servizi mobili 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Questo argomento descrive come autenticare gli utenti in Servizi mobili di Azure dalla propria app HTML, incluse quelle PhoneGap o Cordova. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

Questa esercitazione descrive le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili].

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. Nella directory dell'app avviare uno dei file di comando seguenti dalla sottocartella **server**.

	+ **launch-windows** (computer Windows) 
	+ **launch-mac.command** (computer Mac OS X)
	+ **launch-linux.sh** (computer Linux)

	>[AZURE.NOTE]In un computer Windows digitare `R` quando PowerShell chiede di confermare l'esecuzione dello script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.

	Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

2. Aprire l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in un Web browser per avviare l'app.

	Il caricamento dei dati ha esito negativo. L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

3. (Facoltativo) Aprire il debugger di script per il Web browser in uso e ricaricare la pagina. Verificare che venga restituito un errore di accesso negato.

A questo punto, si aggiornerà l'app in modo da consentire l'autenticazione prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

>[AZURE.NOTE]Poiché l'accesso viene eseguito in una finestra popup, richiamare il metodo <strong>login</strong> da un evento clic del pulsante. In caso contrario, in molti browser non sarà visualizzata la finestra di accesso.

1. Aprire il file di progetto, individuare l'elemento H1 e sotto di esso aggiungere il frammento di codice seguente:

	    <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

	Sarà quindi possibile accedere a Servizi mobili dalla pagina.

2. Nel file app.js individuare la riga di codice alla fine del file in cui viene chiamata la funzione refreshTodoItems e sostituirla con il codice seguente:
	
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

    In questo modo viene creato un set di funzioni per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Facebook. Se si usa un provider di identità diverso da Facebook, sostituire il valore passato al metodo <strong>login</strong> riportato in precedenza con uno dei seguenti: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em> o <em>aad</em>.

	>[AZURE.IMPORTANT]In un'app PhoneGap è inoltre necessario aggiungere al progetto i plug-in seguenti: <ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li> <li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. Tornare al browser in cui è in esecuzione l'app e aggiornare la pagina.

	   Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

	>[AZURE.NOTE]Se si usa Internet Explorer, dopo l'accesso è possibile che venga visualizzato un errore che indica che <code>non è possibile accedere all'oggetto opener della finestra, in quanto potrebbe trovarsi in un'area di Internet Explorer diversa</code>. Questo errore si verifica perché la finestra popup viene eseguita in un'area di sicurezza (Internet) diversa da localhost (Intranet). Questo errore interessa le app solo durante lo sviluppo tramite localhost. Per risolvere il problema, in <strong>Opzioni Internet</strong> aprire la scheda <strong>Sicurezza</strong>, fare clic su <strong>Intranet locale</strong>, quindi su <strong>Siti</strong> e disabilitare <strong>Rileva automaticamente rete Intranet</strong>. Ricordare di tornare a modificare questa opzione al termine del test.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per altre informazioni su come usare Servizi mobili con HTML/JavaScript, vedere [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-html-get-started.md
[Get started with data]: mobile-services-html-get-started-data.md
[Autorizzazione di utenti con script]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /documentation/articles/mobile-services-html-how-to-use-client-library

<!--HONumber=54--> 