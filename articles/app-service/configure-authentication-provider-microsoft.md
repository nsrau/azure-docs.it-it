---
title: Configurare l'autenticazione tramite account Microsoft - Servizio app di Azure
description: Informazioni su come configurare l'autenticazione dell'account Microsoft per un'applicazione dei servizi app.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 5ef0cb2da26fcc00d1daf4b2dd0faf8bde8b743b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098531"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Come configurare l’applicazione del servizio app per usare l'account di accesso Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare l'account Microsoft come provider di autenticazione. 

## <a name="register-microsoft-account"></a>Registrare l'app con l'account Microsoft
1. Accedere al [portale di Azure]e passare all'applicazione. 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. Passare a [**registrazioni app**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)e accedere con il account Microsoft, se richiesto.

1. Fare clic su **nuova registrazione**, quindi digitare il nome di un'applicazione.

1. In **URI di reindirizzamento**selezionare **Web**, quindi digitare `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`. *Sostituire\<app-Domain-Name >* con il nome di dominio dell'app.  Ad esempio `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. 

   > [!NOTE]
   > Usare lo schema HTTPS nell'URL.

1. Selezionare **registra**. 

1. Copiare l' **ID applicazione (client)** . Sarà necessario in un secondo momento. 
   
7. Dal percorso di spostamento a sinistra della nuova registrazione dell'app, selezionare **certificati & segreti** > **nuovo client Secret**. Specificare una descrizione, selezionare la durata della validità e selezionare **Aggiungi**.

1. Copiare il valore visualizzato nella pagina **certificati & segreti** . Una volta chiusa la pagina, il valore non verrà più mostrato.

    > [!IMPORTANT]
    > La password è una credenziale di sicurezza importante. Non condividerla con altri e non distribuirla all'interno di un'applicazione client.

## <a name="secrets"></a>Aggiungere le informazioni dell'account Microsoft all'applicazione del servizio app
1. Nel [portale di Azure], passare all'applicazione. Nel percorso di spostamento a sinistra fare clic su **autenticazione/autorizzazione**.

2. Se la funzionalità autenticazione/autorizzazione non è abilitata, selezionare **on**.

3. In **provider di autenticazione**selezionare **account Microsoft**. Incollare l'ID dell'applicazione (client) e il segreto client ottenuti in precedenza e, facoltativamente, abilitare gli ambiti richiesti dall'applicazione. Fare quindi clic su **OK**.

    Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.

4. Opzionale Per limitare l'accesso agli utenti account Microsoft, impostare l' **azione da eseguire quando la richiesta non viene autenticata** per l'accesso **con l'account Microsoft**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate all’account Microsoft per l'autenticazione.

> [!CAUTION]
> La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderate per le app che vogliono un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** può essere preferibile, con l'app che avvia manualmente l'accesso, come descritto [qui](overview-authentication-authorization.md#authentication-flow).

5. Fare clic su **Save**.

È ora possibile usare l'account Microsoft per l'autenticazione nell'app.

## <a name="related-content"></a>Contenuti correlati
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portale di Azure]: https://portal.azure.com/
