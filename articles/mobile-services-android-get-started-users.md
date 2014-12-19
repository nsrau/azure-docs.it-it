<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Introduzione all'autenticazione (Android) | Mobile Developer Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal" />

# Aggiungere l'autenticazione all'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">guarda l'esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a><span class="time">10:42</span></div>
</div> 

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]
4. [Archiviare i token di autenticazione sul client]
5. [Aggiornare i token scaduti]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 

>[AZURE.NOTE] Per visualizzare il codice sorgente dell'app completata, fare clic <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithAuth/Android" target="_blank">qui</a>.

Per completare questa esercitazione, è necessario disporre di Eclipse e Android 4.2 o versione successiva. 

<h2><a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati</h2>

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

3. In Eclipse aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili]. 

4. Nel menu **Run** fare clic su **Run** per eseguire l'app e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato). 

	 L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

<h2><a name="add-authentication"></a>Aggiungere l'autenticazione all'app</h2>

[WACOM.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Memorizzare nella cache i token di autenticazione sul client

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Aggiornare la cache dei token

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 



## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzare gli utenti con gli script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. 

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella ai soli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Archiviare i token di autenticazione sul client]: #cache-tokens
[Aggiornare i token scaduti]: #refresh-tokens
[Passaggi successivi]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single Sign-On per app di Windows Store tramite Live Connect]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-android
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-android
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-android
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-android
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-android

[Portale di gestione di Azure]: https://manage.windowsazure.com/
