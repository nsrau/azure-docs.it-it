<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/23/2014" ms.author="krisragh"></tags>

# Introduzione all'autenticazione in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dall'app per iOS. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][]
3.  [Aggiunta dell'autenticazione all'app][]
4.  [Archiviazione dei token di autenticazione nell'app][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][].

Per completare questa esercitazione, è necessario disporre di XCode 4.5 e iOS 5.0 o versioni successive.

## <a name="register"></a> Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  In Xcode aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili][1].

2.  Premere il pulsante **Run** per compilare il progetto e avviare l'app nell'emulatore iPhone e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a> Aggiunta dell'autenticazione all'app

[WACOM.INCLUDE [mobile-services-ios-authenticate-app][]]

## <a name="store-authentication"></a>Archiviazione dei token di autenticazione nell'app

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token][]]

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Archiviazione dei token di autenticazione nell'app]: #store-authentication
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /it-it/documentation/articles/mobile-services-ios-get-started
  [mobile-services-ios-authenticate-app]: ../includes/mobile-services-ios-authenticate-app.md
  [mobile-services-ios-authenticate-app-with-token]: ../includes/mobile-services-ios-authenticate-app-with-token.md
  [Autorizzazione sul lato servizio degli utenti di Servizi mobili]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-ios
