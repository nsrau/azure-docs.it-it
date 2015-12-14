<properties
	pageTitle="Introduzione all’autenticazione su Android (back-end JavaScript) | Microsoft Azure"
	description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per Android tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft. (back-end JavaScript)"
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

# Aggiungere l'autenticazione all'app Android per Servizi mobili (back-end JavaScript)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## Riepilogo

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

> [AZURE.VIDEO android-getting-started-with-authentication-in-windows-azure-mobile-services]

Questa esercitazione illustra le operazioni di base per abilitare l'autenticazione in un'app.


##Prerequisiti

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1. In Android Studio aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili].

2. Nel menu **Run** fare clic su **Run app** e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

	 L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Memorizzare nella cache i token di autenticazione sul client

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../../includes/mobile-services-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>Aggiornare la cache dei token

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../../includes/mobile-services-android-authenticate-app-refresh-token.md)]



## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: mobile-services-android-get-started.md
[Autorizzazione di utenti con script]: mobile-services-javascript-backend-service-side-authorization.md

<!---HONumber=AcomDC_1203_2015-->