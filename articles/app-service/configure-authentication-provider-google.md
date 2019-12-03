---
title: Configurare l'autenticazione di Google
description: Informazioni su come configurare l'autenticazione di Google come provider di identità per l'app del servizio app.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670817"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Configurare l'app del servizio app per usare l'account di accesso di Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario avere un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"></a>Registrare l'applicazione con Google

1. Per creare un ID client e un segreto client, seguire la documentazione di Google relativa all' [accesso a Google per le app sul lato server](https://developers.google.com/identity/sign-in/web/server-side-flow) . Non è necessario apportare modifiche al codice. Usare semplicemente le seguenti informazioni:
    - Per le **origini JavaScript autorizzate**, usare `https://<app-name>.azurewebsites.net` con il nome dell'app in *\<> nome app*.
    - Per l' **URI di reindirizzamento autorizzato**, usare `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Copiare i valori di ID app e segreto app.

    > [!IMPORTANT]
    > Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.

## <a name="secrets"> </a>Aggiungere le informazioni di Google all'applicazione

1. Nel [Azure portal]passare all'app del servizio app.
1. Selezionare **impostazioni** > **autenticazione/autorizzazione**e assicurarsi che **l'autenticazione del servizio app** sia **attiva**.
1. Selezionare **Google**, quindi incollare i valori di ID app e segreto app ottenuti in precedenza. Abilitare gli ambiti necessari per l'applicazione.
1. Selezionare **OK**.

   Il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto del sito e alle API. Per altre informazioni, vedere [autorizzare o negare utenti](app-service-authentication-how-to.md#authorize-or-deny-users).

1. Opzionale Per limitare l'accesso al sito solo agli utenti autenticati da Google, impostare **azione da eseguire quando la richiesta non è autenticata** su **Google**. Quando si imposta questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutte le richieste non autenticate a Google per l'autenticazione.

    > [!CAUTION]
    > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere utili per le app con un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** potrebbe essere preferibile, in modo che l'app avvii manualmente l'autenticazione. Per altre informazioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Selezionare **Salva**.

È ora possibile usare un account Google per l'autenticazione nell'app.

## <a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure portal]: https://portal.azure.com/

