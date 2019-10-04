---
title: Configurare l'autenticazione di Google - Servizio app di Azure
description: Informazioni su come configurare l'autenticazione di Google per l'app del servizio app.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232144"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Come configurare l'applicazione del servizio app per usare l'account di accesso di Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario avere un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"></a>Registrare l'applicazione con Google
1. Per creare un ID client e un segreto client, vedere la documentazione di Google relativa all' [accesso a Google per le app sul lato server](https://developers.google.com/identity/sign-in/web/server-side-flow) , con le informazioni seguenti (non è necessario apportare modifiche al codice):
    - Per le **origini JavaScript**autorizzate `https://<app-name>.azurewebsites.net` , usare con il nome dell'app in  *\<nome app >* .
    - Per l' **URI di reindirizzamento autorizzato**, usare `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Una volta creati l'ID client e i segreti client, copiarne i valori.

    > [!IMPORTANT]
    > Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.


## <a name="secrets"> </a>Aggiungere le informazioni di Google all'applicazione
1. Nel [portale di Azure] passare alla pagina dell'applicazione dei servizi app. Nel menu a sinistra selezionare **autenticazione/autorizzazione**.
2. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.
3. Fare clic su **Google**. Incollare i valori di ID app e segreto app ottenuti in precedenza e, facoltativamente, abilitare gli ambiti richiesti dall'applicazione. Fare quindi clic su **OK**.

   Il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto del sito e alle API. Per altre informazioni, vedere [autorizzare o negare utenti](app-service-authentication-how-to.md#authorize-or-deny-users).
4. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Google, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Google**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate a Google per l'autenticazione.

    > [!NOTE]
    > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderate per le app che vogliono un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** può essere preferibile, con l'app che avvia manualmente l'accesso, come descritto [qui](overview-authentication-authorization.md#authentication-flow).
    
5. Fare clic su **Save**.

È ora possibile usare un account Google per l'autenticazione nell'app.

## <a name="related-content"></a>Contenuti correlati
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portale di Azure]: https://portal.azure.com/

