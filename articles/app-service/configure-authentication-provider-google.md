---
title: Configurare l'autenticazione di Google - Servizio app di Azure
description: Informazioni su come configurare l'autenticazione Google per un'applicazione dei servizi app.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 50905b86924e0f564eaf4867c2906ad8740ddbaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851180"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Come configurare l'applicazione del servizio app per usare l'account di accesso di Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario avere un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"></a>Registrare l'applicazione con Google
1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l' **URL**, che verrà usato in seguito per configurare l'app Google.
2. Passare al sito Web delle [API di Google](https://go.microsoft.com/fwlink/p/?LinkId=268303)accedere con le credenziali dell'account Google, fare clic su **Crea progetto**, specificare un valore in **Nome progetto**, quindi fare clic su **Crea**.
3. Una volta creato il progetto, selezionarlo. Dal dashboard del progetto, fare clic su **Go to APIs overview** (panoramica Vai a API).
4. Selezionare **Abilita API e servizi** (Abilita API e servizi). Cercare **Google + API**e selezionarlo. Fare quindi clic su **Attiva**.
5. Nel riquadro di spostamento a sinistra scegliere **Credenziali** > **Schermata consenso OAuth**, quindi selezionare il proprio **Indirizzo email**, immettere un **Nome del prodotto** e fare clic su **Salva**.
6. Nella scheda **Credenziali** fare clic su **Crea credenziali** > **ID client OAuth**.
7. Nella schermata "Create client ID" (Crea ID Cliente), selezionare **Applicazione Web**.
8. Incollare l'**URL** del servizio app copiato in precedenza in **Origini JavaScript autorizzate** e quindi incollare l'URI di reindirizzamento in **URI di reindirizzamento autorizzati**. L'URI di reindirizzamento corrisponde all'URL dell'applicazione con l'aggiunta del percorso */.auth/login/google/callback*. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Assicurarsi che sia in uso lo schema HTTPS. Fare quindi clic su **Crea**.
9. Fare clic sulla schermata successiva e annotare i valori di ID client e Segreto client.

    > [!IMPORTANT]
    > Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.


## <a name="secrets"> </a>Aggiungere le informazioni di Google all'applicazione
1. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.
2. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.
3. Fare clic su **Google**. Incollare i valori ID App e Segreto app ottenuti in precedenza e facoltativamente abilitare tutti gli ambiti richiesti dall'applicazione. Fare quindi clic su **OK**.
   
   ![][1]
   
   Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.
4. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Google, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Google**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate a Google per l'autenticazione.
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

