---
title: Aggiungere l&quot;autenticazione in Android con app per dispositivi mobili| Documentazione Microsoft
description: "Informazioni su come usare app per dispositivi mobili del servizio app di Azure per autenticare gli utenti dell&quot;app Android tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3caf6edb1e3a33a451ec8773b1e14fe0c95734a1


---
# <a name="add-authentication-to-your-android-app"></a>Aggiungere l'autenticazione all'app Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Riepilogo
Questa esercitazione consente di aggiungere l'autenticazione al progetto introduttivo TodoList in Android tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione relativa alla [Introduzione alle app per dispositivi mobili] , che deve essere completata per prima.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* In Android Studio aprire il progetto completato nell'esercitazione [Introduzione alle app per dispositivi mobili]. Scegliere **Run app** (Esegui app) dal menu **Run** (Esegui) e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).
  
     L'eccezione non gestita viene generata perché l'app tenta di accedere al back-end come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, viene aggiornata l'app in modo che autentichi gli utenti prima di richiedere risorse al back-end dell'app per dispositivi mobili.

## <a name="add-authentication-to-the-app"></a>Aggiungere l'autenticazione all'app
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="a-namecache-tokensacache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Memorizzare nella cache i token di autenticazione sul client
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato questa esercitazione sull'autenticazione di base, provare a continuare fino a una delle esercitazioni seguenti:

* [Aggiungere notifiche push all'app Android](app-service-mobile-android-get-started-push.md) Informazioni su come configurare il back-end dell'app per dispositivi mobili in modo che usi l'Hub di notifica di Azure per inviare notifiche push.
* [Abilitare la sincronizzazione offline per l'app Android](app-service-mobile-android-get-started-offline-data.md) Informazioni su come aggiungere il supporto offline all'app tramite il back-end di un'app per dispositivi mobili. La sincronizzazione offline consente agli utenti finali di interagire con un'app&mdash;visualizzando, aggiungendo e modificando i dati&mdash;anche se non è disponibile una connessione di rete.

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Archiviare i token di autenticazione nel client]: #cache-tokens
[Aggiornare i token scaduti]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introduzione alle app per dispositivi mobili]: app-service-mobile-android-get-started.md



<!--HONumber=Nov16_HO3-->


