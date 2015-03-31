<properties 
	pageTitle="Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft" 
	description="Informazioni su come registrare l'app di Windows Store per l'autenticazione Microsoft nell'applicazione Servizi mobili di Azure" 
	services="mobile-services" 
	documentationCenter="" 
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

# Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft

Servizi mobili di Azure supporta metodi di autenticazione sia basati su client che basati su server. L'autenticazione basata su server usa i provider di identità, tra cui l'account Microsoft. Quando si usa un account Microsoft con l'autenticazione basata su server senza registrare l'app con Servizi mobili, agli utenti viene chiesto di fornire le credenziali ogni volta che è richiesta l'autenticazione. Quando si registra l'app, le credenziali di accesso dell'account Microsoft vengono memorizzate nella cache e possono essere usate per l'autenticazione senza richiesta all'utente di specificarle nuovamente. Questo argomento descrive come registrare il pacchetto dell'app di Windows Store per migliorare l'esperienza di accesso tramite account Microsoft quando si usa Servizi mobili di Azure per l'autenticazione. 

>[AZURE.NOTE]Visual Studio 2013 semplifica la registrazione del pacchetto dell'app di Windows Store con Servizi mobili. Per altre informazioni, vedere <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">Guida introduttiva: aggiunta di notifiche push per un servizio mobile</a> in Windows Dev Center.

L'autenticazione gestita dal client può essere usata per fornire un'esperienza di accesso Single Sign-On su un dispositivo Windows usando Live Connect. Se si usano le API di Live Connect, non è necessario completare la procedura descritta in questo articolo. Per altre informazioni, vedere [Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect].   

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Dopo avere registrato il pacchetto dell'app, è necessario fornire un valore <strong>true</strong> per <em>useSingleSignOn</em> quando si chiama <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> . In questo modo gli utenti ottengono l'esperienza di accesso ottimizzata quando usano un account Microsoft Account.

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Introduzione alle notifiche push]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introduzione agli utenti C#]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Introduzione agli utenti JavaScript]: /develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=47-->
