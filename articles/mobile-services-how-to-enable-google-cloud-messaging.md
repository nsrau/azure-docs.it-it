<properties 
	pageTitle="Come abilitare Google Cloud Messaging" 
	description="Questa esercitazione consente di creare un nuovo servizio usando Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Come abilitare Google Cloud Messaging

Questo argomento descrive come abilitare un'app per Android per le notifiche push usando Google Cloud Messaging (GCM). La chiave API ottenuta viene usata per registrare l'app per Android per le notifiche push nel [portale di gestione di Azure][portale di gestione]. Per l'esercitazione completa, con informazioni sull'aggiornamento dell'app, vedere [Introduzione alle notifiche push]. 

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

A questo punto è possibile usare questo valore della chiave API per abilitare servizi per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Introduzione alle notifiche push]: /documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Visual Studio 2012 Express per Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[portale di gestione]: https://manage.windowsazure.com/
[Versione back-end .NET]: /documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started

<!--HONumber=47-->
