<properties urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Introduzione all'autenticazione (iOS) | Mobile Dev Center" metaKeywords="registrazione applicazione Azure, autenticazione Azure, autenticazione applicazione, autenticazione servizi mobili, Servizi mobili iOS" description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app iOS tramite numerosi provider di identità, tra cui Microsoft, Facebook, Twitter e Google." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Aggiunta dell'autenticazione nell'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dall'app per iOS.  Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.  

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2. [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiunta dell'autenticazione all'app]
4. [Archiviazione dei token di autenticazione nell'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili].

Per completare questa esercitazione, è necessario disporre di XCode 4.5 e iOS 5.0 o versioni successive.

##<a name="register"></a>Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>In Xcode aprire il progetto creato dopo avere completato l'esercitazione <a href="/it-it/documentation/articles/mobile-services-ios-get-started">Introduzione a Servizi mobili</a>.</p></li>
<li><p>Scegliere il pulsante <strong>Run</strong> per compilare il progetto e avviare l'app nell'emulatore iPhone e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).<p>

   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiunta dell'autenticazione all'app

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Archiviazione dei token di autenticazione nell'app

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione lato servizio degli utenti di Servizi mobili][Authorize users with scripts], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. -->
[Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
[Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiunta dell'autenticazione all'app]: #add-authentication
[Passaggi successivi]:#next-steps
[Archiviazione dei token di autenticazione nell'app]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Invio di una pagina dell'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios
[Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-ios

[Portale di gestione di Azure]: https://manage.windowsazure.com/

<!--HONumber=35.2-->
