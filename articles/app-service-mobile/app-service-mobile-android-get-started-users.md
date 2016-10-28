<properties
	pageTitle="Aggiungere l'autenticazione ad Android con App per dispositivi mobili | Servizio app di Azure"
	description="Informazioni su come usare app per dispositivi mobili del servizio app di Azure per autenticare gli utenti dell'app Android tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="ricksal"/>

# Aggiungere l'autenticazione all'app Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Riepilogo

Questa esercitazione consente di aggiungere l'autenticazione al progetto introduttivo TodoList in Android tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione relativa alla [creazione di un'app per dispositivi mobili], che deve essere completata per prima.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ In Android Studio aprire il progetto creato dopo aver completato l'esercitazione relativa alla [creazione di un'app per dispositivi mobili], scegliere **Run app** dal menu **Run** e verificare che, dopo l'avvio dell'app, venga generata un'eccezione non gestita con codice di stato di 401 (non autorizzato).

	 L'eccezione non gestita viene generata perché l'app tenta di accedere al backend come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al back-end dell'app per dispositivi mobili.

## Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Memorizzare nella cache i token di autenticazione sul client

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##Passaggi successivi

Dopo aver completato questa esercitazione sull'autenticazione di base, provare a continuare fino a una delle esercitazioni seguenti:

+ [Aggiungere notifiche push all'app Android](app-service-mobile-android-get-started-push.md) Informazioni su come aggiungere il supporto per le notifiche push all'app e configurare il back-end dell'app per dispositivi mobili per l'utilizzo di Hub di notifica di Azure per l'invio di notifiche push.

+ [Abilitare la sincronizzazione offline per l'app Android](app-service-mobile-android-get-started-offline-data.md) Informazioni su come aggiungere il supporto offline all'app tramite il back-end di un'app per dispositivi mobili. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[creazione di un'app per dispositivi mobili]: app-service-mobile-android-get-started.md

<!---HONumber=AcomDC_0720_2016-->