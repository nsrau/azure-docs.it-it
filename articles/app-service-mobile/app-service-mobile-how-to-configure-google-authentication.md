---
title: Come configurare l'autenticazione Google per un'applicazione dei servizi app
description: Informazioni su come configurare l'autenticazione Google per un'applicazione dei servizi app.
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 09db38cfb5fb4277d4ca259eb10e388af33f1773
ms.contentlocale: it-it
ms.lasthandoff: 03/09/2017

---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Come configurare l'applicazione del servizio app per usare l'account di accesso di Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario avere un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrare l'applicazione con Google
1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l' **URL**, che verrà usato in seguito per configurare l'app Google.
2. Passare al sito Web delle [API di Google](http://go.microsoft.com/fwlink/p/?LinkId=268303)accedere con le credenziali dell'account Google, fare clic su **Crea progetto**, specificare un valore in **Nome progetto**, quindi fare clic su **Crea**.
3. In **API Social** fare clic **Google+ API** e quindi su **Abilita**.
4. Nel riquadro di spostamento a sinistra scegliere **Credenziali** > **Schermata consenso OAuth**, quindi selezionare il proprio **Indirizzo email**, immettere un **Nome del prodotto** e fare clic su **Salva**.
5. Nella scheda **Credenziali** fare clic su **Crea credenziali**  > **ID client OAuth**, quindi selezionare **Applicazione web**.
6. Incollare l'**URL** del servizio app copiato in precedenza in **Origini JavaScript autorizzate** e quindi incollare l'URI di reindirizzamento in **URI di reindirizzamento autorizzati**. L'URI di reindirizzamento corrisponde all'URL dell'applicazione con l'aggiunta del percorso */.auth/login/google/callback*. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Assicurarsi che sia in uso lo schema HTTPS. Fare quindi clic su **Crea**.
7. Fare clic sulla schermata successiva e annotare i valori di ID client e Segreto client.

    > [AZURE.IMPORTANT]
    Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.


## <a name="secrets"> </a>Aggiungere le informazioni di Google all'applicazione
1. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.
2. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.
3. Fare clic su **Google**. Incollare i valori ID App e Segreto app ottenuti in precedenza e facoltativamente abilitare tutti gli ambiti richiesti dall'applicazione. Fare quindi clic su **OK**.
   
   ![][1]
   
   Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.
4. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Google, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Google**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate a Google per l'autenticazione.
5. Fare clic su **Save**.

È ora possibile usare un account Google per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portale di Azure]: https://portal.azure.com/


