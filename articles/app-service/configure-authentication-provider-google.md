---
title: Configurare l'autenticazione di Google
description: Informazioni su come configurare l'autenticazione di Google come provider di identità per il servizio app o l'app funzioni di Azure.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519937"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Configurare il servizio app o l'app funzioni di Azure per usare l'account di accesso di Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento illustra come configurare app Azure servizio o funzioni di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario avere un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registrare l'applicazione con Google

1. Per creare un ID client e un segreto client, seguire la documentazione di Google relativa all' [accesso a Google per le app sul lato server](https://developers.google.com/identity/sign-in/web/server-side-flow) . Non è necessario apportare modifiche al codice. Usare semplicemente le seguenti informazioni:
    - Per le **origini JavaScript autorizzate**, usare `https://<app-name>.azurewebsites.net` con il nome dell'app in * \<nome app>*.
    - Per l' **URI di reindirizzamento autorizzato**, usare `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Copiare i valori di ID app e segreto app.

    > [!IMPORTANT]
    > Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Aggiungere le informazioni di Google all'applicazione

1. Nel [portale di Azure]passare all'app del servizio app.
1. Selezionare **Impostazioni** > **autenticazione/autorizzazione**e assicurarsi che **l'autenticazione del servizio app** sia **attiva**.
1. Selezionare **Google**, quindi incollare i valori di ID app e segreto app ottenuti in precedenza. Abilitare gli ambiti necessari per l'applicazione.
1. Selezionare **OK**.

   Il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto del sito e alle API. Per altre informazioni, vedere [autorizzare o negare utenti](app-service-authentication-how-to.md#authorize-or-deny-users).

1. Opzionale Per limitare l'accesso al sito solo agli utenti autenticati da Google, impostare **azione da eseguire quando la richiesta non è autenticata** su **Google**. Quando si imposta questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutte le richieste non autenticate a Google per l'autenticazione.

    > [!CAUTION]
    > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere utili per le app con un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** potrebbe essere preferibile, in modo che l'app avvii manualmente l'autenticazione. Per altre informazioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

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

[Azure portal]: https://portal.azure.com/

