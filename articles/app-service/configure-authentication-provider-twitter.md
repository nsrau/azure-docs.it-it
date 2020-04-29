---
title: Configurare l'autenticazione di Twitter
description: Informazioni su come configurare l'autenticazione di Twitter come provider di identità per il servizio app o l'app funzioni di Azure.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519900"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Configurare il servizio app o l'app funzioni di Azure per usare l'account di accesso di Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare app Azure servizio o funzioni di Azure per usare Twitter come provider di autenticazione.

Per completare la procedura descritta in questo articolo, è necessario un account Twitter con un indirizzo di posta elettronica verificato e un numero di telefono. Per creare un nuovo account Twitter, visitare il sito Web all'indirizzo [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registrare l'applicazione con Twitter

1. Accedere al [portale di Azure] e passare all'applicazione. Copiare l' **URL**. Verrà usato per configurare l'app Twitter.
1. Visitare il sito Web degli [sviluppatori di Twitter] , accedere con le credenziali dell'account Twitter e selezionare **Crea un'app**.
1. Immettere il **nome dell'app** e la **Descrizione dell'applicazione** per la nuova app. Incollare l' **URL** dell'applicazione nel campo **URL del sito Web** . Nella sezione **URL di callback** immettere l'URL HTTPS dell'app del servizio app e aggiungere il percorso `/.auth/login/twitter/callback`. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Nella parte inferiore della pagina digitare almeno 100 caratteri in **indicare come verrà usata questa app**, quindi selezionare **Crea**. Fare di nuovo clic su **Crea** nella finestra popup. Verranno visualizzati i dettagli dell'applicazione.
1. Selezionare la scheda **Keys and Access Tokens** .

   Prendere nota di questi valori:
   - Chiave API
   - Chiave privata API

   > [!NOTE]
   > La chiave privata API è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Aggiungere informazioni di Twitter all'applicazione

1. Passare all'applicazione nella [portale di Azure].
1. Selezionare **Impostazioni** > **autenticazione/autorizzazione**e assicurarsi che **l'autenticazione del servizio app** sia **attiva**.
1. Selezionare **Twitter**.
1. Incollare i `API key` valori e `API secret key` ottenuti in precedenza.
1. Selezionare **OK**.

   ![Screenshot delle impostazioni di Twitter per app per dispositivi mobili][1]

   Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto del sito e alle API. È necessario autorizzare gli utenti nel codice dell'app.

1. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Twitter, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Twitter**. Quando si imposta questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutte le richieste non autenticate a Twitter per l'autenticazione.

   > [!CAUTION]
   > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere utili per le app con un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** potrebbe essere preferibile, in modo che l'app avvii manualmente l'autenticazione. Per altre informazioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Selezionare **Salva**.

È ora possibile usare un account Twitter per l'autenticazione nell'app.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[sviluppatori Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
