<properties urlDisplayName="Shared Access Signature Part 1" pageTitle="Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft" metaKeywords="" description="Informazioni su come registrare l'app di Windows Store per l'autenticazione Microsoft nell'applicazione Servizi mobili di Azure" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft

Servizi mobili di Azure supporta metodi di autenticazione sia basati su client che basati su server. L'autenticazione basata su server utilizza i provider di identità, tra cui l'account Microsoft. Quando si utilizza un account Microsoft con l'autenticazione basata su server senza registrare l'app con Servizi mobili, agli utenti viene chiesto di fornire le credenziali ogni volta che è richiesta l'autenticazione. Quando si registra l'app, le credenziali di accesso dell'account Microsoft vengono memorizzate nella cache e possono essere usate per l'autenticazione senza richiesta all'utente di specificarle nuovamente. Questo argomento descrive come registrare il pacchetto dell'app di Windows Store per migliorare l'esperienza di accesso tramite account Microsoft quando si utilizza Servizi mobili di Azure per l'autenticazione. 

>[WACOM.NOTE]Visual Studio 2013 semplifica la registrazione del pacchetto dell'app di Windows Store con Servizi mobili. Per altre informazioni, vedere <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">Guida introduttiva: Aggiunta di notifiche push per un servizio mobile</a> in Windows Dev Center.

L'autenticazione gestita dal client può essere usata per fornire un'esperienza di accesso Single Sign-On su un dispositivo Windows usando Live Connect. Se si utilizzano le API di Live Connect, non è necessario completare la procedura descritta in questo articolo. Per altre informazioni, vedere [Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect].   

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Dopo avere registrato il pacchetto dell'app, specificare un valore <strong>true</strong> per the <em>useSingleSignOn</em> quando si chiama il metodo <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>. In questo modo gli utenti ottengono l'esperienza di accesso ottimizzata quando utilizzano un account Microsoft Account.

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introduzione agli utenti C#]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet/
[Introduzione agli utenti JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=35.1-->
