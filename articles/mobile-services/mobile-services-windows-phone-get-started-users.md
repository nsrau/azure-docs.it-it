<properties 
	pageTitle="Introduzione all'autenticazione (Windows Phone) | Microsoft Azure" 
	description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per Windows Phone tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

##Panoramica

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

Questa esercitazione viene illustrata anche da Nick Harris nel video seguente:

> [AZURE.VIDEO mobile-authorize-users-in-scripts-windows-phone]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare l'esercitazione [Aggiungere Servizi mobili a un'app esistente].

>[AZURE.NOTE]Questa esercitazione illustra il flusso di autenticazione gestito da Servizi mobili mediante vari provider di identità. Questo metodo è semplice da configurare e supporta più provider, L'uso dell'autenticazione gestita dal client consente all'app di accedere a dati utente aggiuntivi mantenuti dal provider di identità. È possibile ottenere gli stessi dati utente nel servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per altre informazioni, vedere [questo post](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


&nbsp;&nbsp;3. In Visual Studio 2012 Express per Windows Phone aprire il progetto creato dopo avere completato l'esercitazione [Aggiungere Servizi mobili a un'app esistente](mobile-services-windows-phone-get-started-data.md).

&nbsp;&nbsp;4. Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato). L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Archiviare i token di autorizzazione sul client

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili](mobile-services-javascript-backend-service-side-authorization.md), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Aggiungere Servizi mobili a un'app esistente]: mobile-services-windows-phone-get-started-data.md
[Authorize users with scripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md
[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=Oct15_HO3-->