<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft
===================================================

Servizi mobili di Azure supporta metodi di autenticazione sia basati su client che basati su server. L'autenticazione basata su server utilizza i provider di identità, tra cui l'account Microsoft. Quando si utilizza un account Microsoft con l'autenticazione basata su server senza registrare l'app con Servizi mobili, agli utenti viene chiesto di fornire le credenziali ogni volta che è richiesta l'autenticazione. Quando si registra l'app, le credenziali di accesso dell'account Microsoft vengono memorizzate nella cache e possono essere utilizzate per l'autenticazione senza richiesta all'utente di specificarle nuovamente. In questo argomento viene illustrato come registrare il pacchetto di app di Windows Store per migliorare l'esperienza di accesso tramite account Microsoft quando si utilizza Servizi mobili di Azure per l'autenticazione.

L'autenticazione basata su client può essere utilizzata per fornire un'esperienza di accesso singolo su un dispositivo Windows utilizzando Live Connect. Se si utilizzano le API di Live Connect, non è necessario completare la procedura descritta in questo articolo. Per ulteriori informazioni, veder e[Autenticazione dell'app di Windows Store per l'accesso Single Sign-On di Live Connect](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet).

La registrazione dell'app consente anche di inviare notifiche push all'app. Se è già stata completata l'esercitazione [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/) per l'app, la procedura descritta in questo argomento è già stata eseguita.

**Nota**

*Visual Studio 2013 Preview* include nuove funzionalità che semplificano la registrazione del pacchetto di app di Windows Store con Servizi mobili. Per ulteriori informazioni, vedere [Guida introduttiva: Aggiunta di notifiche push per un servizio mobile](http://go.microsoft.com/fwlink/p/?LinkId=309101) in Windows Dev Center.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Dopo avere registrato il pacchetto di app, specificare un valore **true** per *useSingleSignOn* quando si chiama il metodo [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594). In questo modo gli utenti ottengono l'esperienza di accesso ottimizzata quando utilizzano un account Microsoft Account.

