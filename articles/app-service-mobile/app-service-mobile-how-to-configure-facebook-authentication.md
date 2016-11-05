---
title: Come configurare l'autenticazione Facebook per un'applicazione dei servizi app
description: Informazioni su come configurare l'autenticazione Facebook per un'applicazione dei servizi app.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/22/2016
ms.author: mahender

---
# Come configurare un'applicazione del servizio App per usare l'account di accesso di Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare Facebook come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account di Facebook con un indirizzo di posta elettronica verificato e un numero di cellulare. Per creare un nuovo account di Facebook, visitare il sito [facebook.com].

## <a name="register"> </a>Registrare l'applicazione con Facebook
1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l'**URL**. Verrà usato per configurare l'app Facebook.
2. In un'altra finestra del browser passare al sito Web [Facebook Developers] e accedere con le credenziali dell'account Facebook.
3. (Facoltativo) Se non è ancora stata effettuata la registrazione, fare clic su **App** > **Registrarsi come uno Sviluppatore**, quindi accettare le condizioni e seguire la procedura di registrazione.
4. Fare clic su **My Apps** > **Add a New App** > **Website** > **Skip and Create App ID**.
5. In **Display Name** (Nome visualizzato) digitare un nome univoco per l'app, digitare un indirizzo in **Contact Email** (Indirizzo di posta elettronica di contatto), scegliere una categoria per l'app in **Category** (Categoria), quindi fare clic su **Create App ID** (Crea ID app) e completare il controllo di sicurezza. Si passerà al dashboard dello sviluppatore per la nuova app di Facebook.
6. In "Facebook Login" (Accesso a Facebook) fare clic su **Get Started** (Informazioni di base). Aggiungere l'URI di **Redirect URI** (URI di reindirizzamento) dell'applicazione in **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth validi) e quindi fare clic su **Save Changes** (Salva modifiche).
   
   > [!NOTE]
   > L'URI di reindirizzamento corrisponde all'URL dell'applicazione con l'aggiunta del percorso */.auth/login/facebook/callback*. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Assicurarsi che sia in uso lo schema HTTPS.
   > 
   > 
7. Nel riquadro di spostamento a sinistra fare clic su **Settings** (Impostazioni). Nel campo **App Secret** fare clic su **Show**, fornire la password se richiesto, quindi prendere nota dei valori di **App ID** e **App Secret**. Verranno usati più avanti per configurare l'applicazione in Azure.
   
   > [!IMPORTANT]
   > Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.
   > 
   > 
8. L'account di Facebook usato per registrare l'applicazione sarà un account di amministratore dell'app. A questo punto, solo gli amministratori potranno effettuare l'accesso a questa applicazione. Per eseguire l'autenticazione di altri account di Facebook, fare clic su **App Review** (Controllo app) e abilitare l'opzione **Make <nome-app> public** (Rendi pubblica) e consentire così l'accesso pubblico generale con l'autenticazione di Facebook.

## <a name="secrets"> </a>Aggiungere le informazioni di Facebook all'applicazione
1. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni** > **Autenticazione/Autorizzazione**, quindi assicurarsi che l'opzione **Autenticazione servizio app** sia impostata su **Sì**.
2. Fare clic su **Facebook**, incollare nell’ID dell’app e nel segreto dell’app i valori ottenuti in precedenza, e abilitare facoltativamente tutti gli ambiti richiesti dall'applicazione, poi fare clic su **OK**.
   
    ![][0]
   
    Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.
3. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Facebook, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Facebook**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate a Facebook per l'autenticazione.
4. Al termine della configurazione dell'autenticazione, fare clic su **Salva**.

È ora possibile usare un account di Facebook per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /it-IT/develop/mobile/tutorials/get-started-with-users-dotnet/
[portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0824_2016-->