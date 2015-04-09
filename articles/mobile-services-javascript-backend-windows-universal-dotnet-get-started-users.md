<properties 
	pageTitle="Introduzione all'autenticazione (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per Windows Store tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft." 
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
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

Questo argomento descrive come autenticare gli utenti in Servizi mobili di Azure dalla propria app Windows universale. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]
5. [Archiviare i token di autenticazione sul client]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 

>[AZURE.NOTE] Questa esercitazione descrive come autenticare gli utenti nelle app di Windows Store e Windows Phone Store 8.1. Per le app per Windows Phone 8.0 o Windows Phone Silverlight 8.1, consultare la sezione [Introduzione all'autenticazione in Servizi mobili](mobile-services-windows-phone-get-started-users.md).

>In questa esercitazione viene illustrato il flusso di autenticazione gestito da Servizi mobili usando vari provider di identità. Questo metodo è semplice da configurare e supporta più provider. Per usare in alternativa Live Connect con l'autenticazione gestita dal client e per offrire un ambiente Single Sign-On nella propria app per Windows Phone, vedere l'argomento [Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect]. L'uso dell'autenticazione gestita dal client consente all'app di accedere a dati utente aggiuntivi mantenuti dal provider di identità. È possibile ottenere gli stessi dati utente nel proprio servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per altre informazioni, vedere [questo post](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Facoltativo) Completare la procedura descritta in <a href="mobile-services-how-to-register-store-app-package-microsoft-authentication.md">Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft</a>.</p>
<p>Questo passaggio è facoltativo perché si applica solo al provider di accesso con account Microsoft. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client è in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Completare questo passaggio se si prevede di usare il provider di identità per account Microsoft.</p>
</li>
</ol>

##<a name="permissions"></a> Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>In Visual Studio fare clic con il pulsante destro del mouse sul progetto Windows Store per l'app TodoList, quindi selezionare <strong>Imposta come progetto di avvio</strong>.</p></li>
<li><p>Nel progetto condiviso, aprire il file di progetto App.xaml.cs, individuare la definizione di <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> e assicurarsi che sia configurata in modo tale da effettuare la connessione al servizio mobile in esecuzione su Azure.</p>
<p>Si noti che quando si usa uno strumento di Visual Studio per connettere l'app a un servizio mobile, lo strumento genera due set di definizioni <strong>MobileServiceClient</strong>, uno per ciascuna piattaforma client. Si consiglia di semplificare il codice generato unendo le definizioni <code>#if...#endif</code> di <strong>MobileServiceClient</strong> con wrapping in un'unica definizione senza wrapping usata da entrambe le versioni dell'app. Non è necessario eseguire questa operazione se l'app di guida introduttiva è stata scaricata dal portale di gestione di Azure.</p>
</li> 
<li><p>Premere F5 per eseguire l'app di Windows Store. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a> Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>Archiviare i token di autorizzazione nel client

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili](mobile-services-javascript-backend-service-side-authorization.md), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. Per altre informazioni su come usare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili]

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Archiviare i token di autenticazione sul client]: #tokens
[Passaggi successivi]:#next-steps


<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single Sign-On per app di Windows Store tramite Live Connect]: mobile-services-windows-store-dotnet-single-sign-on.md
[Introduzione a Servizi mobili]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Introduzione ai dati]: mobile-services-javascript-backend-windows-store-dotnet-get-started-data.md
[Introduzione all'autenticazione]: mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Introduzione alle notifiche push]: mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Autorizzare gli utenti con gli script]: mobile-services-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript e HTML]: mobile-services-windows-store-javascript-get-started-users.md

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Registrare il pacchetto dell'app di Windows Store per l'autenticazione Microsoft]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->