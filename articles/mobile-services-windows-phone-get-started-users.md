<properties urlDisplayName="Get Started with Authentication" pageTitle="Introduzione all'autenticazione (Windows Phone) | Mobile Developer Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Aggiungere l'autenticazione all'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">10:50</span></div>
</div>  

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 

>[WACOM.NOTE]Questa esercitazione illustra il flusso di autenticazione gestito da Servizi mobili con vari provider di identità. Questo metodo è semplice da configurare e supporta più provider, Per usare invece Live Connect con l'autenticazione gestita dal client e offrire un ambiente Single Sign-On nella propria app di Windows Phone, vedere l'argomento [Autenticazione dell'app di Windows Phone 8 con l'accesso Single Sign-On di Live Connect]. L'uso dell'autenticazione gestita dal client consente all'app di accedere a dati utente aggiuntivi mantenuti dal provider di identità. È possibile ottenere gli stessi dati utente nel proprio servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per altre informazioni, vedere [questo post](http://go.microsoft.com/fwlink/p/?LinkId=506605).

## <a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili


[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 


## <a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati


[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. In Visual Studio 2012 Express per Windows Phone aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili]. 

4. Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato). 
   
   	L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="tokens"></a>Archiviare i token di autorizzazione sul client

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzare gli utenti con gli script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. 

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Passaggi successivi]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-get-started
[Autorizzare gli utenti con gli script]: /it-it/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Single Sign-On per app di Windows Phone tramite Live Connect]: /it-it/documentation/articles/mobile-services-windows-phone-single-sign-on
