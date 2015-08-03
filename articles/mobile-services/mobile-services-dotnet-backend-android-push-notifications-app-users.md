<properties 
	pageTitle="Inviare notifiche push agli utenti autenticati" 
	description="Informazioni su come inviare notifiche push a utenti specifici" 
	services="mobile-services,notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="notification-hubs" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="wesmc"/>

# Inviare notifiche push agli utenti autenticati

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Panoramica

Questo argomento descrive come inviare notifiche push a un utente autenticato su un dispositivo registrato. A differenza della precedente esercitazione sulle [notifiche push][Get started with push notifications], in questa esercitazione il servizio mobile viene modificato in modo da richiedere l'autenticazione di un utente prima che il client possa effettuare la registrazione con l'hub di notifica per le notifiche push. Anche la registrazione viene modificata per poter aggiungere un tag in base all'ID utente assegnato. Infine il codice del server viene aggiornato per inviare la notifica solo all'utente autenticato invece che a tutte le registrazioni.


Questa esercitazione supporta le app Android.

##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Aggiungere l'autenticazione all'app di Servizi mobili]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Introduzione alle notifiche push]<br/>Configura l'app di esempio TodoList per le notifiche push con Hub di notifica.

Dopo aver completato entrambe le esercitazioni, è possibile impedire agli utenti non autenticati di effettuare la registrazione per le notifiche push dal servizio mobile.

##Aggiornare il servizio per richiedere l'autenticazione per la registrazione

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##Aggiornare l'app per accedere prima della registrazione

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](../../includes/mobile-services-android-push-notifications-app-users.md)]

##Testare l'app

[AZURE.INCLUDE [mobile-services-android-test-push-users](../../includes/mobile-services-android-test-push-users.md)]


<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users](mobile-services-javascript-backend-service-side-authorization.md), you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Aggiungere l'autenticazione all'app di Servizi mobili]: mobile-services-dotnet-backend-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md
[Introduzione alle notifiche push]: mobile-services-dotnet-backend-android-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
 

<!---HONumber=July15_HO4-->