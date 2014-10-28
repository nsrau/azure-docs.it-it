<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, FAcebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/18/2014" ms.author="glenga"></tags>

# Introduzione all'autenticazione in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app di Windows Store. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

> [WACOM.NOTE]L'autenticazione non è attualmente supportata per le app di Windows Phone Store 8.1 quando si usa la libreria client JavaScript di Servizi mobili. Se si ha un progetto di app Windows universale che usa il client JavaScript, non è possibile autenticare contemporaneamente gli utenti su Windows Phone.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][]
3.  [Aggiunta dell'autenticazione all'app][]
4.  [Archiviazione dei token di autenticazione sul client][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][].

## <a name="register"></a> Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication][]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][]]

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][]]

1.  In Visual Studio 2013 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili][1].

2.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a> Aggiunta dell'autenticazione all'app

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app][]]

## <a name="tokens"></a>Archiviazione dei token di autenticazione sul client

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token][]]

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per ulteriori informazioni su come utilizzare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili][]

<!-- Anchors. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Archiviazione dei token di autenticazione sul client]: #tokens
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [1]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [mobile-services-windows-store-javascript-authenticate-app]: ../includes/mobile-services-windows-store-javascript-authenticate-app.md
  [mobile-services-windows-store-javascript-authenticate-app-with-token]: ../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md
  [Autorizzazione sul lato servizio degli utenti di Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts
  [Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
