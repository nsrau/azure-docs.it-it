---
title: Configurare l'autenticazione di Facebook
description: Informazioni su come configurare l'autenticazione di Facebook come provider di identità per il servizio app o l'app Funzioni di Azure.Learn how to configure Facebook authentication as an identity provider for your App Service or Azure Functions app.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9a2d390a5647ed90284730e9186e981b8e699d10
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438010"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Configurare il servizio app o l'app Funzioni di Azure per usare l'account di accesso di FacebookConfigure your App Service or Azure Functions app to use Facebook login

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare il servizio app di Azure o funzioni di Azure per usare Facebook come provider di autenticazione.

Per completare la procedura descritta in questo articolo, è necessario un account Facebook con un indirizzo di posta elettronica verificato e un numero di cellulare. Per creare un nuovo account di Facebook, visitare il sito [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registrare l'applicazione con Facebook

1. Accedi al sito Web [degli sviluppatori] di Facebook con le credenziali del tuo account Facebook.

   Se non hai un account Facebook for Developers, seleziona **Inizia** e segui i passaggi di registrazione.
1. Selezionare **Le mie app** > **Aggiungi nuova app**.
1. Nel campo **Nome visualizzato:**
   1. Digitare un nome univoco per l'app.
   1. Fornire **l'e-mail**di contatto .
   1. Selezionare **Crea ID app**.
   1. Completare il controllo di sicurezza.

   Viene aperta la dashboard per sviluppatori per la nuova app Facebook.
1. Selezionare **Dashboard** > **Facebook Login** > **Set up** > **Web**.
1. Nel riquadro di spostamento a sinistra in **Facebook Login**selezionare Settings .Select **Settings**.
1. Nel campo URI di **reindirizzamento OAuth validi** immettere `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. Ricordarsi `<app-name>` di sostituire con il nome dell'app del servizio app di Azure.Remember to replace with the name of your Azure App Service app.
1. Selezionare **Save changes** (Salva modifiche).
1. Nel riquadro sinistro selezionare **Impostazioni** > **di base**. 
1. Nel campo **Segreto app** selezionare **Mostra**. Copiare i valori di **ID app** e **Segreto app**. È possibile usarli in un secondo momento per configurare l'app del servizio app in Azure.You use them later to configure your App Service app in Azure.

   > [!IMPORTANT]
   > Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.
   >

1. L'account Facebook utilizzato per registrare l'applicazione è un amministratore dell'app. A questo punto, solo gli amministratori possono accedere a questa applicazione.

   Per autenticare altri account Facebook, seleziona **Revisione app** e abilita **Rendi \<pubblico il tuo nome app> per** consentire al pubblico generale di accedere all'app tramite l'autenticazione di Facebook.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Aggiungere le informazioni di Facebook all'applicazione

1. Accedere al [portale] di Azure e passare all'app del servizio app.
1. Selezionare **Autenticazione** > **impostazioni/Autorizzazione**e assicurarsi che **l'autenticazione** del servizio app sia **attivata.**
1. Selezionare **Facebook**, quindi incollare i valori ID app e Segreto app ottenuti in precedenza. Abilitare tutti gli ambiti necessari per l'applicazione.
1. Selezionare **OK**.

   ![Screenshot delle impostazioni di Facebook dell'app per dispositivi mobili][0]

    Per impostazione predefinita, il servizio app fornisce l'autenticazione, ma non limita l'accesso autorizzato al contenuto e alle API del sito. Devi autorizzare gli utenti nel codice dell'app.
1. (Facoltativo) Per limitare l'accesso solo agli utenti autenticati da Facebook, imposta **Azione da intraprendere quando la richiesta non è autenticata** su **Facebook**. Quando imposti questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutte le richieste non autenticate a Facebook per l'autenticazione.

   > [!CAUTION]
   > Limitare l'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderabili per le app che dispongono di una home page disponibile pubblicamente, come in molte applicazioni a pagina singola. Per tali applicazioni, **consenti richieste anonime (nessuna azione)** potrebbe essere preferibile in modo che l'applicazione avvia manualmente l'autenticazione stessa. Per ulteriori informazioni, vedere [Flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Selezionare **Salva**.

Ora sei pronto per usare Facebook per l'autenticazione nella tua app.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portale di Azure]: https://portal.azure.com/
