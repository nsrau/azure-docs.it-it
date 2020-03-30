---
title: Configurare l'autenticazione di Google
description: Scopri come configurare l'autenticazione Google come provider di identità per la tua app del servizio app.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74670817"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Configurare l'app del servizio app per l'utilizzo dell'accesso Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario avere un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registrare l'applicazione con Google

1. Segui la documentazione di Google in [Accesso Google per le app sul lato server](https://developers.google.com/identity/sign-in/web/server-side-flow) per creare un ID client e un segreto client. Non è necessario apportare modifiche al codice. Basta utilizzare le seguenti informazioni:
    - Per **Origini JavaScript autorizzate**, utilizzare `https://<app-name>.azurewebsites.net` con il nome dell'app in * \<nome dell'app>*.
    - Per **l'URI di reindirizzamento autorizzato**, utilizzare `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Copiare l'ID app e i valori dei segreti dell'app.

    > [!IMPORTANT]
    > Il segreto dell'app è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Aggiungere le informazioni di Google all'applicazione

1. Nel [portale di Azure]passare all'app del servizio app.
1. Selezionare **Autenticazione** > **impostazioni/Autorizzazione**e assicurarsi che **l'autenticazione** del servizio app sia **attivata.**
1. Seleziona **Google**, quindi incolla i valori ID app e Segreto app ottenuti in precedenza. Abilitare tutti gli ambiti necessari per l'applicazione.
1. Selezionare **OK**.

   Il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto e alle API del sito. Per ulteriori informazioni, consultate [Autorizzare o negare agli utenti.](app-service-authentication-how-to.md#authorize-or-deny-users)

1. (Facoltativo) Per limitare l'accesso al sito solo agli utenti autenticati da Google, imposta **Azione da intraprendere quando la richiesta non è autenticata** su **Google**. Quando imposti questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza inoltre tutte le richieste non autenticate a Google per l'autenticazione.

    > [!CAUTION]
    > Limitare l'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderabili per le app che dispongono di una home page disponibile pubblicamente, come in molte applicazioni a pagina singola. Per tali applicazioni, **consenti richieste anonime (nessuna azione)** potrebbe essere preferibile in modo che l'applicazione avvia manualmente l'autenticazione stessa. Per ulteriori informazioni, vedere [Flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Selezionare **Salva**.

È ora possibile usare un account Google per l'autenticazione nell'app.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portale di Azure]: https://portal.azure.com/

