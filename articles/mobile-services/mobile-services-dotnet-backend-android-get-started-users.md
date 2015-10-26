<properties
	pageTitle="Introduzione all'autenticazione (Android) | Microsoft Azure"
	description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per Windows Store tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/05/2015" 
	ms.author="ricksal"/>

# Aggiungere l'autenticazione all'app Android per Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## Riepilogo

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

Questa esercitazione illustra le operazioni di base per abilitare l'autenticazione in un'app.


## Prerequisiti

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

3. Aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili].

4. Nel menu **Run** fare clic su **Run app** per eseguire l'app e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

	 L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Memorizzare nella cache i token di autenticazione sul client

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../../includes/mobile-services-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>Aggiornare la cache dei token

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../../includes/mobile-services-android-authenticate-app-refresh-token.md)]

##<a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Authorize users with scripts], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps

<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-android-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-android-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=Oct15_HO3-->