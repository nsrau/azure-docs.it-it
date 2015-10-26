<properties
	pageTitle="Aggiungere l'autenticazione all'app universale di Windows Runtime 8.1 | App per dispositivi mobili di Azure"
	description="Informazioni su come usare le app per dispositivi mobili del servizio app di Azure per autenticare gli utenti dell'app di Windows tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]& nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Questo argomento descrive come autenticare gli utenti di un'app mobile del servizio app dall'applicazione client. In questa esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato dal servizio app. In seguito all'autenticazione e all'autorizzazione da parte dell'app mobile, viene visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva dell'app mobile. È necessario completare prima l'esercitazione relativa alla [creazione di un'app per dispositivi mobili]. http://acom-sandbox.azurewebsites.net/documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/?rnd=1

##<a name="create-gateway"></a>Creare un gateway del servizio app

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. In Visual Studio aprire il file di progetto condiviso App.xaml.cs nel progetto di app client e assicurarsi che l'istanza di **MobileServiceClient** sia configurata per usare sia l'URL del back-end dell'app per dispositivi mobili sia il gateway.

&nbsp;&nbsp;5. Dopo aver impostato uno dei progetti di app di Windows come progetto di avvio, premere F5 per eseguire l'app. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

&nbsp;&nbsp;L'eccezione non gestita viene generata perché l'app tenta di accedere al codice dell'app per dispositivi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Archiviare il token di autenticazione sul client

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]

##Passaggi successivi


<!-- URLs. -->
[creazione di un'app per dispositivi mobili]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
 

<!---HONumber=Oct15_HO3-->