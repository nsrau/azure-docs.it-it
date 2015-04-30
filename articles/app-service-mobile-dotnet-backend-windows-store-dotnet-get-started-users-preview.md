<properties 
	pageTitle="Introduzione all'autenticazione per app mobili in Windows" 
	description="Informazioni su come usare le app mobili per autenticare gli utenti dell'app per iOS tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="mahender"/>

# Aggiungere l'autenticazione all'app Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

Questo argomento descrive come autenticare gli utenti di un'app mobile del servizio app dall'applicazione client. In questa esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato dal servizio app. In seguito all'autenticazione e all'autorizzazione da parte dell'app mobile, viene visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva dell'app mobile. È inoltre necessario completare prima l'esercitazione [Introduzione al servizio per app mobili]. 

## <a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

## <a name="permissions"></a>Limitazione delle autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li>In Visual Studio aprire il progetto di app client e assicurarsi che l'istanza di <b>MobileServiceClient</b> in App.xaml.cs sia configurata per l'uso dell'URL della risorsa dell'app mobile nel cloud.</li> 
<li><p>Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere al codice dell'app mobile come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)] 


[AZURE.NOTE] Se le informazioni del pacchetto dell'app di Windows Store sono state registrate con i servizi app, è necessario chiamare il metodo <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> specificando il valore <strong>true</strong> per il parametro <em>useSingleSignOn</em>. In caso contrario, gli utenti dovranno comunque specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso.


## <a name="tokens"></a>Archiviare il token di autenticazione sul client

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)] 


<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare il servizio app]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Archiviare i token di autenticazione sul client]: #tokens
[Passaggi successivi]:#next-steps


<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione al servizio per app mobili]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md


[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
[Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md
[Autenticazione Single Sign-On per app di Windows Store tramite Live Connect]: mobile-services-windows-store-dotnet-single-sign-on.md

<!--HONumber=49-->