<properties 
	pageTitle="Introduzione all'autenticazione (Windows Phone) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per Windows Phone tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft." 
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
	ms.date="02/25/2015" 
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app di Windows Phone. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

>[AZURE.NOTE] Questo argomento supporta solo app di Windows Phone 8.0 e Windows Phone 8.1 Silverlight. Se si dispone di un'app di Windows Phone Store 8.1 o di un'app di Windows universale, seguire invece le informazioni relativa alle [versione di questo argomento per le app di Windows universali](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]
4. [Archiviare i token di autenticazione sul client]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 


##<a name="register"></a> Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>In Visual Studio aprire il progetto di app client e assicurarsi che l'istanza di <strong>MobileServiceClient</strong> in App.xaml.cs sia configurata per l'uso dell'URL del servizio mobile nel cloud.</li> 
<li><p>Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Archiviare i token di autorizzazione nel client

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. Per altre informazioni su come usare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili]

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Archiviare i token di autenticazione sul client]: #tokens
[Passaggi successivi]: #next-steps


<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single Sign-On per app di Windows Phone tramite Live Connect]: mobile-services-windows-phone-single-sign-on.md
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-windows-phone-get-started.md
[Introduzione ai dati]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Introduzione all'autenticazione]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Introduzione alle notifiche push]: mobile-services-dotnet-backend-windows-phone-get-started-push.md
[Autorizzazione di utenti con script]: mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts.md
[JavaScript e HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /develop/mobile/how-to-guides/work-with-net-client-library
[Registrare il pacchetto dell'app di Windows Store per l'autenticazione Microsoft]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->