<properties 
	pageTitle="Inviare notifiche push agli utenti autenticati" 
	description="Informazioni su come inviare notifiche push a specifici" 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# Inviare notifiche push agli utenti autenticati

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

Questo argomento descrive come inviare notifiche push a un utente autenticato su un dispositivo registrato. A differenza della precedente esercitazione sulle [notifiche push][Introduzione alle notifiche push] in questa esercitazione i servizi mobili vengono modificati in modo da richiedere che un utente venga autenticato prima che il client possa effettuare la registrazione con l'hub di notifica per le notifiche push. Anche la registrazione viene modificata per poter aggiungere un tag in base all'ID utente assegnato. Infine il codice del server viene aggiornato per inviare la notifica solo all'utente autenticato invece che a tutte le registrazioni.

In questa esercitazione viene descritto il processo seguente:

+ [Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]
+ [Aggiornamento dell'app per accedere prima della registrazione]
+ [Testare l'app]
 
Questa esercitazione supporta app sia di Windows Store che di Windows Phone Store.

##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Introduzione all'autenticazione]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Introduzione alle notifiche push]<br/>Configura l'app di esempio TodoList per le notifiche push con Hub di notifica. 

Dopo aver completato entrambe le esercitazioni, è possibile impedire agli utenti non autenticati di effettuare la registrazione per le notifiche push dal servizio mobile.

##<a name="register"></a>Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

##<a name="update-app"></a>Aggiornare l'app per accedere prima della registrazione

[AZURE.INCLUDE [mobile-services-windows-store-javascript-push-notifications-app-users](../includes/mobile-services-windows-store-javascript-push-notifications-app-users.md)] 

##<a name="test"></a>Testare l'app

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. Per altre informazioni su come usare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili]-->

<!-- Anchors. -->
[Aggiornamento del servizio per richiedere l'autenticazione per la registrazione]: #register
[Aggiornamento dell'app per accedere prima della registrazione]: #update-app
[Testare l'app]: #test
[Passaggi successivi]:#next-steps


<!-- URLs. -->
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
