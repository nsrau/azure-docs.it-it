---
title: Aggiungere l&quot;autenticazione in Android con App per dispositivi mobili| Documentazione Microsoft
description: "Informazioni su come usare la funzione App per dispositivi mobili di Servizio app di Azure per autenticare gli utenti dell&quot;app Android tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: adrianha
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
ms.sourcegitcommit: 817626dd3fc87db61280075b80cedf8b9ed77684
ms.openlocfilehash: e638495c10742388804e75f3277c50cf1e20c6a6
ms.lasthandoff: 12/13/2016


---
# <a name="add-authentication-to-your-android-app"></a>Aggiungere l'autenticazione all'app Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Riepilogo
Questa esercitazione spiega come aggiungere l'autenticazione al progetto introduttivo TodoList in Android tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione [Introduzione ad App per dispositivi mobili], che deve essere completata per prima.

## <a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizio app di Azure
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* In Android Studio aprire il progetto completato con l'esercitazione [Introduzione ad App per dispositivi mobili]. Dal menu **Run** fare clic su **Run app** e verificare che, dopo l'avvio dell'app, venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzata).

     Questa eccezione viene generata perché l'app tenta di accedere al back-end come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiorna l'app in modo che autentichi gli utenti prima di richiedere risorse al back-end di App per dispositivi mobili. 

## <a name="add-authentication-to-the-app"></a>Aggiungere l'autenticazione all'app
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Memorizzare nella cache i token di autenticazione sul client
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato questa esercitazione sull'autenticazione di base, provare a continuare fino a una delle esercitazioni seguenti:

* [Aggiungere notifiche push all'app Android](app-service-mobile-android-get-started-push.md).
  Informazioni su come configurare App per dispositivi mobili in modo da utilizzare l'Hub di notifica di Azure per inviare notifiche push.
* [Attivare la sincronizzazione offline per l'app Android](app-service-mobile-android-get-started-offline-data.md).
  Informazioni su come aggiungere il supporto offline all'app usando il back-end di App per dispositivi mobili. Con la sincronizzazione offline è possibile interagire con un'app per dispositivi mobili &mdash;visualizzando, aggiungendo e modificando i dati&mdash; anche quando non è disponibile una connessione di rete.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introduzione ad App per dispositivi mobili]: app-service-mobile-android-get-started.md

