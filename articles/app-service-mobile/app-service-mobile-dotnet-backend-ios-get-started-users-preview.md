<properties
	pageTitle="Aggiungere l'autenticazione in iOS con le app per dispositivi mobili di Azure"
	description="Informazioni su come usare le app per dispositivi mobili di Azure per autenticare gli utenti dell'app iOS tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="krisragh"/>

# Autenticazione di iOS con le app per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]& nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Questa esercitazione illustra come aggiungere l'autenticazione al progetto di [avvio rapido di iOS] tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione relativa all'[avvio rapido di iOS], che deve essere completata per prima. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di autenticazione al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="create-gateway"></a>Creare un gateway del servizio app

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Xcode fare clic su **Run** per avviare l'app. L'eccezione non gestita viene generata perché l'app tenta di accedere al back-end come utente non autenticato, mentre ora la tabella _TodoItem_ richiede l'autenticazione.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[avvio rapido di iOS]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=Oct15_HO3-->