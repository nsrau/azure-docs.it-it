<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn="" Gailey" />

# Introduzione all'autenticazione in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">

<div class="dev-onpage-left-content">

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

È inoltre possibile visualizzare una versione video dell'esercitazione facendo clic sul fotogramma a destra.

</div>

<div class="dev-onpage-video-wrapper">

[video di esercitazione][video di esercitazione] [<span class="icon">Riproduci video</span>][<span class="icon">Riproduci video</span>] <span class="time">10:04:00</span>

</div>

</div>

> [WACOM.NOTE]L'autenticazione non è attualmente supportata per le app di Windows Phone Store 8.1 quando si usa la libreria client JavaScript di Servizi mobili. Se si ha un progetto di app Windows universale che usa il client JavaScript, non è possibile autenticare contemporaneamente gli utenti su Windows Phone.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app][Aggiunta dell'autenticazione all'app]
4.  [Archiviazione dei token di autenticazione sul client][Archiviazione dei token di autenticazione sul client]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

> [WACOM.NOTE]Questa esercitazione illustra il flusso di autenticazione gestito da Servizi mobili tramite diversi provider di identità. Questo metodo è semplice da configurare e supporta più provider, Per usare invece Live Connect con l'autenticazione gestita da client e offrire un ambiente di accesso Single Sign-On nella propria app per Windows Phone, vedere l'argomento [Single Sign-On per app di Windows Store tramite Live Connect][Single Sign-On per app di Windows Store tramite Live Connect]. Usando l'autenticazione gestita da client, l'app accede a dati utente aggiuntivi gestiti dal provider di identità. È possibile ottenere gli stessi dati utente nel servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per altre informazioni, vedere [questo post][questo post].

## <a name="register"></a> Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Facoltativo) Completare la procedura descritta in [Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft][Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft].

    <div class="dev-callout">

    **Nota**
    Questo passaggio è facoltativo perché si applica solo al provider di accesso con account Microsoft. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client è in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Completare questo passaggio se si prevede di utilizzare il provider di identità per account Microsoft.

    </div>

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili][1].

2.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a> Aggiunta dell'autenticazione all'app

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)]

## <a name="tokens"></a>Archiviazione dei token di autorizzazione sul client

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzazione sul lato servizio degli utenti di Servizi mobili], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per ulteriori informazioni su come utilizzare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili][Riferimento per i concetti e le procedure di .NET per Servizi mobili]

<!-- Anchors. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [video di esercitazione]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services
  [<span class="icon">Riproduci video</span>]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Archiviazione dei token di autenticazione sul client]: #tokens
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
  [Single Sign-On per app di Windows Store tramite Live Connect]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [questo post]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft]: /it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /it-it/documentation/articles/mobile-services-windows-store-get-started
  [mobile-services-windows-dotnet-authenticate-app]: ../includes/mobile-services-windows-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [Autorizzazione sul lato servizio degli utenti di Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
