<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft

Servizi mobili di Azure supporta metodi di autenticazione sia basati su client che basati su server. L'autenticazione basata su server utilizza i provider di identità, tra cui l'account Microsoft. Quando si utilizza un account Microsoft con l'autenticazione basata su server senza registrare l'app con Servizi mobili, agli utenti viene chiesto di fornire le credenziali ogni volta che è richiesta l'autenticazione. Quando si registra l'app, le credenziali di accesso dell'account Microsoft vengono memorizzate nella cache e possono essere utilizzate per l'autenticazione senza richiesta all'utente di specificarle nuovamente. In questo argomento viene illustrato come registrare il pacchetto di app di Windows Store per migliorare l'esperienza di accesso tramite account Microsoft quando si utilizza Servizi mobili di Azure per l'autenticazione.

> [WACOM.NOTE]Visual Studio 2013 semplifica la registrazione del pacchetto dell'app di Windows Store con Servizi mobili. Per altre informazioni, vedere [Guida introduttiva: Aggiunta di notifiche push per un servizio mobile][Guida introduttiva: Aggiunta di notifiche push per un servizio mobile] in Windows Dev Center.

L'autenticazione gestita dal client può essere usata per fornire un'esperienza di accesso Single Sign-On su un dispositivo Windows usando Live Connect. Se si utilizzano le API di Live Connect, non è necessario completare la procedura descritta in questo articolo. Per ulteriori informazioni, vedere [Autenticazione dell'app di Windows Store per l'accesso Single Sign-On di Live Connect][Autenticazione dell'app di Windows Store per l'accesso Single Sign-On di Live Connect].

> [WACOM.NOTE]L'autenticazione gestita dal client tramite Live Connect non è ancora supportata in un servizio mobile back-end .NET.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Dopo avere registrato il pacchetto di app, specificare un valore **true** per *useSingleSignOn* quando si chiama il metodo [LoginAsync][LoginAsync]. In questo modo gli utenti ottengono l'esperienza di accesso ottimizzata quando utilizzano un account Microsoft Account.



  [Guida introduttiva: Aggiunta di notifiche push per un servizio mobile]: http://go.microsoft.com/fwlink/p/?LinkId=309101
  [Autenticazione dell'app di Windows Store per l'accesso Single Sign-On di Live Connect]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [LoginAsync]: http://go.microsoft.com/fwlink/p/?LinkId=311594
