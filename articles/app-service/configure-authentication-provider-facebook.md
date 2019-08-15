---
title: Configurare l'autenticazione di Facebook - Servizio app di Azure
description: Informazioni su come configurare l'autenticazione Facebook per un'applicazione dei servizi app.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: c9767ff1e6f0b31270f37842cf99d71cab561505
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033848"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Come configurare un'applicazione del servizio App per usare l'account di accesso di Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare Facebook come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account di Facebook con un indirizzo di posta elettronica verificato e un numero di cellulare. Per creare un nuovo account di Facebook, visitare il sito [facebook.com].

## <a name="register"></a>Registrare l'applicazione con Facebook
1. Passare al sito Web [Facebook Developers] e accedere con le proprie credenziali dell'account di Facebook.
3. Opzionale Se non si dispone di un account Facebook per sviluppatori, fare clic su inizia e seguire la procedura di registrazione.
4. Fare clic su **app** > personali**Aggiungi nuova app**.
5. In **Nome visualizzato**, digitare un nome univoco per l'app. Inserire anche l'**Indirizzo di posta elettronica di contatto**, quindi fare clic su **Crea ID app** e completare il controllo di sicurezza. Si passerà al dashboard dello sviluppatore per la nuova app di Facebook.
6. Fare clic su **Dashboard** > **Facebook login** > **set up** > **Web**.
1. Nella finestra di spostamento a sinistra in **account di accesso di Facebook**fare clic su **Impostazioni**.
1. In **URI di reindirizzamento OAuth validi**Digitare `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` e sostituire  *\<nome app >* con il nome dell'app di servizio app Azure. Fare clic su **salvare le modifiche**.
8. Nel riquadro di spostamento a sinistra fare clic su **Impostazioni** > **Basic**. Nel campo **segreto dell'app** fare clic su **Mostra**. Copiare i valori di **ID app** e **segreto app**. Questi vengono usati in un secondo momento per configurare l'app del servizio app in Azure.
   
   > [!IMPORTANT]
   > Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.
   > 
   > 
9. L'account di Facebook usato per registrare l'applicazione sarà un account di amministratore dell'app. A questo punto, solo gli amministratori potranno effettuare l'accesso a questa applicazione. Per autenticare gli altri account Facebook, fare clic su **Verifica app** e abilitare **make \<your-app-name > Public** per abilitare l'accesso pubblico generale usando l'autenticazione di Facebook.

## <a name="secrets"></a>Aggiungere le informazioni di Facebook all'applicazione
1. Accedere al [portale di Azure] e passare all'app del servizio app. Fare clic su **Impostazioni** > **Autenticazione/Autorizzazione**, quindi assicurarsi che l'opzione **Autenticazione servizio app** sia impostata su **Sì**.
2. Fare clic su **Facebook**, incollare i valori di ID app e segreto app i valori ottenuti in precedenza e abilitare facoltativamente tutti gli ambiti richiesti dall'applicazione, quindi fare clic su **OK**.
   
    ![][0]
   
    Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.
3. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Facebook, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Facebook**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate a Facebook per l'autenticazione.
 
> [!CAUTION]
> La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderate per le app che vogliono un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** può essere preferibile, con l'app che avvia manualmente l'accesso, come descritto [qui](overview-authentication-authorization.md#authentication-flow).

4. Al termine della configurazione dell'autenticazione, fare clic su **Salva**.

È ora possibile usare un account di Facebook per l'autenticazione nell'app.

## <a name="related-content"></a>Contenuti correlati
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portale di Azure]: https://portal.azure.com/
