<properties
	pageTitle="Aggiungere l'autenticazione all'app universale di Windows Runtime 8.1 | App per dispositivi mobili di Azure"
	description="Informazioni su come usare le app per dispositivi mobili del servizio app di Azure per autenticare gli utenti dell'app di Windows tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/23/2015"
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In questo argomento viene illustrato come aggiungere l'autenticazione basata su cloud all’app per dispositivi mobili. In questa esercitazione si aggiungerà l'autenticazione al progetto di guida introduttiva di App per dispositivi mobili tramite un provider di identità supportato dal Servizio app di Azure. In seguito all'autenticazione e all'autorizzazione da parte dell'app per dispositivi mobili, viene visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva di App per dispositivi mobili. È necessario completare prima l'esercitazione relativa alla [creazione di un'app per dispositivi mobili](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Dopo aver impostato uno dei progetti di app di Windows come progetto di avvio, premere F5 per eseguire l'app. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato). L'eccezione non gestita viene generata perché l'app prova ad accedere al codice dell'app per dispositivi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Archiviare il token di autenticazione sul client

Nell'esempio precedente è stato illustrato un accesso standard, che richiede al client di contattare sia il provider di identità sia il servizio app ogni volta che l'app viene avviata. Non solo questo metodo è inefficiente, ma si potrebbero riscontrare problemi relativi all'uso qualora molti clienti provassero ad avviare l'app contemporaneamente. Un miglior approccio consiste nel memorizzare nella cache il token di autorizzazione restituito dal servizio app e provare a usare questo prima di usare un accesso basato su provider.

>[AZURE.NOTE]È possibile memorizzare nella cache il token rilasciato dai Servizi app indipendentemente dal fatto che si usi l'autenticazione gestita dal client o gestita dal servizio. In questa esercitazione viene usata l'autenticazione gestita dal servizio.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##Passaggi successivi

Dopo aver completato questa esercitazione sull'autenticazione di base, provare a continuare fino a una delle esercitazioni seguenti:

+ [Aggiungere notifiche push all'app di Windows](app-service-mobile-windows-store-dotnet-get-started-push.md) informazioni su come aggiungere il supporto per le notifiche push all'app e configurare il back-end di App per dispositivi mobili per l'utilizzo di hub di notifica di Azure per inviare notifiche push.

+ [Abilitare la sincronizzazione offline per l’app di Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) informazioni su come aggiungere il supporto offline all’app mediante un back-end di App per dispositivi mobili. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
 

<!---HONumber=AcomDC_1125_2015--->