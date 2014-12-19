<properties pageTitle="Introduzione all'autenticazione (Windows Store) | Mobile Developer Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey" />

# Aggiungere l'autenticazione all'app di Servizi mobili 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.</p>
<p>È inoltre possibile visualizzare una versione video dell'esercitazione facendo clic sul fotogramma a destra.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">10:04</span></div>
</div> 

>[WACOM.NOTE]L'autenticazione non è attualmente supportata per le app di Windows Phone Store 8.1 quando si usa la libreria client JavaScript di Servizi mobili. Se si ha un progetto di app di Windows universale che usa il client JavaScript, non è possibile autenticare contemporaneamente gli utenti su Windows Phone.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]
5. [Archiviare i token di autenticazione sul client]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 

>[WACOM.NOTE]Questa esercitazione illustra il flusso di autenticazione gestito da Servizi mobili con vari provider di identità. Questo metodo è semplice da configurare e supporta più provider, Per usare invece Live Connect con l'autenticazione gestita dal client e per offrire un ambiente Single Sign-On nella propria app di Windows Phone, vedere l'argomento [Single Sign-On per le app di Windows Phone tramite Live Connect]. L'uso dell'autenticazione gestita dal client consente all'app di accedere a dati utente aggiuntivi mantenuti dal provider di identità. È possibile ottenere gli stessi dati utente nel proprio servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per altre informazioni, vedere [questo post](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Registrare l'app per l'autenticazione e configurare Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Facoltativo) Completare la procedura descritta in <a href="/it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft</a>.</p>

    <div class="dev-callout"><b>Nota</b>
	<p>Questo passaggio è facoltativo perché si applica solo al provider di accesso con account Microsoft. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client è in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Completare questo passaggio se si prevede di usare il provider di identità per account Microsoft.</p>
    </div>
</li>
</ol>

##<a name="permissions"></a> Limitare le autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione <a href="/it-it/documentation/articles/mobile-services-windows-store-get-started">Introduzione a Servizi mobili</a>.</p></li> 
<li><p>Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a> Aggiungere l'autenticazione all'app

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>Archiviare il token di autorizzazione sul client

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Authorize users with scripts], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. Per altre informazioni su come usare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili]

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Archiviare i token di autenticazione sul client]: #tokens
[Passaggi successivi]:#next-steps


<!-- URLs. -->
[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single Sign-On per app di Windows Store tramite Live Connect]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/
[Autorizzare gli utenti con gli script]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
[JavaScript e HTML]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
[Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft]: /it-it/develop/mobile/how-to-guides/register-windows-store-app-package
