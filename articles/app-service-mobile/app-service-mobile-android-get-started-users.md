---
title: Aggiungere l'autenticazione su AndroidAdd authentication on Android
description: Scopri come usare il servizio app di Azure per autenticare gli utenti della tua app Android con provider di identità come Google, Facebook, Twitter e Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461641"
---
# <a name="add-authentication-to-your-android-app"></a>Aggiungere l'autenticazione all'app Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Riepilogo
Questa esercitazione spiega come aggiungere l'autenticazione al progetto introduttivo TodoList in Android tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione relativa alla [creazione di un'app per dispositivi mobili] , che deve essere completata per prima.

## <a name="register-your-app-for-authentication-and-configure-azure-app-service"></a><a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizio app di Azure
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Aggiungere l'app agli URL di reindirizzamento esterni consentiti

L'autenticazione sicura richiede la definizione di un nuovo schema URL per l'app. In questo modo il sistema di autenticazione reindirizza all'app al termine del processo di autenticazione. In questa esercitazione si usa lo schema URL _appname_. È tuttavia possibile usare QUALSIASI schema URL. Lo schema deve essere univoco per l'applicazione per dispositivi mobili. Per abilitare il reindirizzamento sul lato server:

1. Nel [portale di Azure] selezionare il servizio app.

2. Fare clic sull'opzione di menu **Autenticazione/Autorizzazione**.

3. In **URL di reindirizzamento esterni consentiti** specificare `appname://easyauth.callback`.  Il valore _appname_ in questa stringa è lo schema URL per l'applicazione per dispositivi mobili.  Deve seguire le normale specifica URL per un protocollo, ovvero usare solo lettere e numeri e iniziare con una lettera.  È opportuno prendere nota della stringa scelta perché sarà necessario modificare il codice dell'applicazione per dispositivi mobili con lo schema URL in diverse posizioni.

4. Fare clic su **OK**.

5. Fare clic su **Salva**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* In Android Studio aprire il progetto completato con l'esercitazione [Introduzione ad App per dispositivi mobili]. Dal menu **Esegui** , fare clic su **Esegui app**e verificare che un'eccezione non gestita con un codice di stato 401 (Non autorizzato) viene generato dopo l'avvio dell'app.

     Questa eccezione viene generata perché l'app tenta di accedere al back-end come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiorna l'app in modo che autentichi gli utenti prima di richiedere risorse al back-end di App per dispositivi mobili.

## <a name="add-authentication-to-the-app"></a>Aggiungere l'autenticazione all'app
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Memorizzare nella cache i token di autenticazione sul client
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato questa esercitazione sull'autenticazione di base, provare a continuare fino a una delle esercitazioni seguenti:

* [Aggiungi notifiche push alla tua app Android](app-service-mobile-android-get-started-push.md).
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
[Introduzione alle app per dispositivi mobili]: app-service-mobile-android-get-started.md
[Portale di Azure]: https://portal.azure.com/
