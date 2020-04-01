---
title: Configurare l'autenticazione di Twitter
description: Informazioni su come configurare l'autenticazione di Twitter come provider di identità per il servizio app o l'app Funzioni di Azure.Learn how to configure Twitter authentication as an identity provider for your App Service or Azure Functions app.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 3f85f30e0a64b6e39b905fc05503a445aa5876ba
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438005"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Configurare il servizio app o l'app Funzioni di Azure per usare l'account di accesso di TwitterConfigure your App Service or Azure Functions app to use Twitter login

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare il servizio app di Azure o funzioni di Azure per usare Twitter come provider di autenticazione.

Per completare la procedura descritta in questo articolo, è necessario un account Twitter con un indirizzo di posta elettronica e un numero di telefono verificati. Per creare un nuovo account Twitter, visitare il sito Web all'indirizzo [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registrare l'applicazione con Twitter

1. Accedere al [portale] di Azure e passare all'applicazione. Copiare l' **URL**. Lo userai per configurare la tua app Twitter.
1. Accedi al sito Web [degli sviluppatori di Twitter,] accedi con le credenziali del tuo account Twitter e seleziona **Crea un'app.**
1. Immettere il **nome dell'app** e la **descrizione dell'applicazione** per la nuova app. Incollare **l'URL** dell'applicazione nel campo **URL sito Web.** Nella sezione URL di **callback** immettere l'URL HTTPS dell'app del servizio app e aggiungere il percorso `/.auth/login/twitter/callback`. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Nella parte inferiore della pagina digitare almeno 100 caratteri in **Informazioni su come verrà utilizzata questa app,** quindi selezionare **Crea**. Fare di nuovo clic su **Crea** nel popup. Vengono visualizzati i dettagli dell'applicazione.
1. Selezionare la scheda **Keys and Access Tokens** .

   Prendere nota di questi valori:
   - Chiave API
   - Chiave segreta API

   > [!NOTE]
   > La chiave del segreto API è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Aggiungere informazioni di Twitter all'applicazione

1. Passare all'applicazione nel portale di [Azure].
1. Selezionare **Autenticazione** > **impostazioni/Autorizzazione**e assicurarsi che **l'autenticazione** del servizio app sia **attivata.**
1. Selezionare **Twitter**.
1. Incollare `API key` i `API secret key` valori e ottenuti in precedenza.
1. Selezionare **OK**.

   ![Screenshot delle impostazioni di Twitter dell'app per dispositivi mobili][1]

   Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto e alle API del sito. È necessario autorizzare gli utenti nel codice dell'app.

1. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Twitter, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Twitter**. Quando imposti questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza inoltre tutte le richieste non autenticate a Twitter per l'autenticazione.

   > [!CAUTION]
   > Limitare l'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderabili per le app che dispongono di una home page disponibile pubblicamente, come in molte applicazioni a pagina singola. Per tali applicazioni, **consenti richieste anonime (nessuna azione)** potrebbe essere preferibile in modo che l'applicazione avvia manualmente l'autenticazione stessa. Per ulteriori informazioni, vedere [Flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

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
[Portale di Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
