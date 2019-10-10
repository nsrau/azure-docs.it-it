---
title: Configurare l'autenticazione di Twitter - Servizio app di Azure
description: Informazioni su come configurare l'autenticazione di Twitter per l'app del servizio app.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 02008b7dc1609a5f28ac6ba2a582933a96428198
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176947"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Configurare l'app del servizio app per usare l'account di accesso di Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare app Azure servizio per usare Twitter come provider di autenticazione.

Per completare la procedura descritta in questo articolo, è necessario un account Twitter con un indirizzo di posta elettronica verificato e un numero di telefono. Per creare un nuovo account Twitter, visitare il sito Web all'indirizzo [twitter.com].

## <a name="register"></a>Registrare l'applicazione con Twitter

1. Accedere al [portale di Azure] e passare all'applicazione. Copiare l' **URL**. Verrà usato per configurare l'app Twitter.
1. Visitare il sito Web degli [sviluppatori Twitter] , accedere con le credenziali dell'account Twitter e selezionare **Crea nuova app**.
1. Immettere un **nome** e una **Descrizione** per la nuova app. Incollare l' **URL** dell'applicazione nel campo **sito Web** . Nel campo **URL callback** immettere l'URL dell'app del servizio app e aggiungere il percorso `/.auth/login/aad/callback`. Ad esempio `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Assicurarsi di usare lo schema HTTPS.
1. Nella parte inferiore della pagina leggere e accettare le condizioni. Selezionare **crea l'applicazione Twitter**. Verranno visualizzati i dettagli dell'applicazione.
1. Selezionare la scheda **Impostazioni** , selezionare **Consenti l'uso dell'applicazione per accedere con Twitter**e quindi selezionare **Aggiorna impostazioni**.
1. Selezionare la scheda **Keys and Access Tokens** .

   Prendere nota di questi valori:
   - Chiave utente (chiave API)
   - Segreto utente (segreto API)

   > [!NOTE]
   > Il segreto consumer è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

## <a name="secrets"></a>Aggiungere informazioni di Twitter all'applicazione

1. Passare all'applicazione nella [portale di Azure].
1. Selezionare **impostazioni** > **autenticazione/autorizzazione**e assicurarsi che **l'autenticazione del servizio app** sia **attiva**.
1. Selezionare **Twitter**.
1. Incollare i valori `API Key` e `API Secret` ottenuti in precedenza.
1. Scegliere **OK**.

   ![Screenshot delle impostazioni di Twitter per app per dispositivi mobili][1]

   Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto del sito e alle API. È necessario autorizzare gli utenti nel codice dell'app.

1. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Twitter, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Twitter**. Quando si imposta questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutte le richieste non autenticate a Twitter per l'autenticazione.

   > [!CAUTION]
   > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere utili per le app con un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** potrebbe essere preferibile, in modo che l'app avvii manualmente l'autenticazione. Per altre informazioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Selezionare **Salva**.

È ora possibile usare un account Twitter per l'autenticazione nell'app.

## <a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[sviluppatori Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Portale di Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
