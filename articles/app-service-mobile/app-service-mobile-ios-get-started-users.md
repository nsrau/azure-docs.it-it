<properties
	pageTitle="Aggiungere l'autenticazione in iOS con le app per dispositivi mobili di Azure"
	description="Informazioni su come usare le app per dispositivi mobili di Azure per autenticare gli utenti dell'app iOS tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="krisragh"/>

# Aggiungere l'autenticazione all'app iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Questa esercitazione illustra come aggiungere l'autenticazione al progetto di [avvio rapido di iOS] tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione relativa all'[avvio rapido di iOS], che deve essere completata per prima.

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Xcode fare clic su **Esegui** per avviare l'app. Viene generata un'eccezione non gestita perché l'app tenta di accedere al back-end come utente non autenticato, mentre ora la tabella _TodoItem_ richiede l'autenticazione.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[avvio rapido di iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com

<!---HONumber=AcomDC_0706_2016-->