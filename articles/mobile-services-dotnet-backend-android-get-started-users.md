<properties pageTitle="Introduzione all'autenticazione (Android) | Mobile Developer Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="09/27/2014" ms.author="mahender" />

# Aggiungere l'autenticazione all'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]
4. [Archiviare i token di autenticazione sul client]
5. [Aggiornare i token scaduti]


Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

3. In Eclipse aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili]. 

4. Nel menu **Run** fare clic su **Run** per eseguire l'app e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato). 

	 L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[WACOM.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Memorizzare nella cache i token di autenticazione sul client

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Aggiornare la cache dei token

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 

##<a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzare gli utenti con gli script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. 


<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella ai soli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Archiviare i token di autenticazione sul client]: #cache-tokens
[Aggiornare i token scaduti]: #refresh-tokens
[Passaggi successivi]:#next-steps

<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/
[Autorizzare gli utenti con gli script]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-authorize-users-in-scripts

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
[Registrare il pacchetto dell'app di Windows Store per l'autenticazione Microsoft]: /it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
