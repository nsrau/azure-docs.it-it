<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"></tags>

# Introduzione all'autenticazione in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verr&agrave; aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identit&agrave; supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">10:50:00</span></div>

</div>

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app][Aggiunta dell'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

> [WACOM.NOTE]Questa esercitazione illustra il flusso di autenticazione gestito da Servizi mobili tramite diversi provider di identità. Questo metodo è semplice da configurare e supporta più provider, Per usare invece Live Connect con l'autenticazione gestita dal client e offrire un ambiente Single Sign-On nella propria app per Windows Phone, vedere l'argomento [Autenticazione dell'app per Windows Phone 8 con l'accesso Single Sign-On di Live Connect][Autenticazione dell'app per Windows Phone 8 con l'accesso Single Sign-On di Live Connect]. Usando l'autenticazione gestita da client, l'app accede a dati utente aggiuntivi gestiti dal provider di identità. È possibile ottenere gli stessi dati utente nel servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per altre informazioni, vedere [questo post][questo post].

## <a name="register"></a> Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  In Visual Studio 2012 Express per Windows Phone aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

2.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a> Aggiunta dell'autenticazione all'app

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app][mobile-services-windows-phone-authenticate-app]]

## <a name="tokens"></a>Archiviazione dei token di autenticazione sul client

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token][mobile-services-windows-phone-authenticate-app-with-token]]

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzazione sul lato servizio degli utenti di Servizi mobili], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [video di esercitazione]: http://go.microsoft.com/fwlink/?LinkId=298631
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-get-started
  [Autenticazione dell'app per Windows Phone 8 con l'accesso Single Sign-On di Live Connect]: /it-it/documentation/articles/mobile-services-windows-phone-single-sign-on
  [questo post]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [mobile-services-windows-phone-authenticate-app]: ../includes/mobile-services-windows-phone-authenticate-app.md
  [mobile-services-windows-phone-authenticate-app-with-token]: ../includes/mobile-services-windows-phone-authenticate-app-with-token.md
  [Autorizzazione sul lato servizio degli utenti di Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts
