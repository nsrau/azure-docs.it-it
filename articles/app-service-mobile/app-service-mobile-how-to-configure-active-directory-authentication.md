---
title: Come configurare l&quot;autenticazione di Azure Active Directory per un&quot;applicazione dei servizi app
description: Informazioni su come configurare l&quot;autenticazione di Azure Active Directory per un&quot;applicazione dei servizi app.
author: mattchenderson
services: app-service
documentationcenter: 
manager: erikre
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 425ecfeec54df9c52a4325964b2b48f6a2856693


---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare i servizi app di Azure per usare Azure Active Directory come provider di autenticazione.

## <a name="express"> </a>Configurare Azure Active Directory usando le impostazioni rapide
1. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.
2. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.
3. Fare clic su **Azure Active Directory** e quindi su **Express** in **Modalità di gestione**.
4. Fare clic su **OK** per registrare l'applicazione in Azure Active Directory. Verrà creata una nuova registrazione. Se invece si intende scegliere una registrazione esistente, fare clic su **Seleziona un'app esistente** e quindi cercare il nome di una registrazione creata in precedenza all'interno del tenant.
   Fare clic sulla registrazione per selezionarla e fare clic su **OK**. Quindi, fare clic su **OK** nel pannello Impostazioni di Azure Active Directory.
   
   ![][0]
   
   Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.
5. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Azure Active Directory, impostare **Azione da eseguire quando la richiesta non è autenticata** su **Accedi con Azure Active Directory**. Per poter usare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate ad Azure Active Directory per l'autenticazione.
6. Fare clic su **Save**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'app.

## <a name="advanced"> </a>(Metodo alternativo) Configurare manualmente Azure Active Directory con impostazioni avanzate
È inoltre possibile scegliere di specificare manualmente le impostazioni di configurazione. Questa è la soluzione migliore se il tenant AAD che si desidera utilizzare è diverso dal tenant con cui si accede ad Azure. Per completare la configurazione, è innanzitutto necessario creare una registrazione in Azure Active Directory. È quindi necessario fornire alcune informazioni di registrazione al servizio app.

### <a name="register"> </a>Registrare l'applicazione con Azure Active Directory
1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l' **URL**. L'URL verrà utilizzato per configurare l'app di Azure Active Directory.
2. Accedere al [portale di Azure classico] e passare ad **Active Directory**.
   
    ![][2]
3. Selezionare la directory, quindi la scheda **Applicazioni** nella parte superiore. Fare clic su **AGGIUNGI** in basso per creare una nuova registrazione dell'app.
4. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
5. Nella procedura guidata di aggiunta applicazione immettere un nome per l'applicazione in **Nome** e fare clic sul tipo **Applicazione Web e/o API Web**. Fare quindi clic per continuare.
6. Nella casella **URL ACCESSO** incollare l'URL dell'applicazione copiato in precedenza. Immettere lo stesso URL nella casella **URI ID app** . Fare quindi clic per continuare.
7. Una volta aggiunta l'applicazione, fare clic sulla scheda **Configura** . Modificare il valore di **URL di risposta** in **Single Sign-on** in modo che corrisponda all'URL dell'applicazione con il percorso */.auth/login/aad/callback come suffisso*. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assicurarsi che sia in uso lo schema HTTPS.
   
    ![][3]
8. Fare clic su **Save**. Copiare quindi l' **ID client** per l'app. L'applicazione verrà configurata in questo modo più avanti.
9. Nella barra dei comandi in basso fare clic su **Visualizza endpoint**, copiare l'URL di **Documento metadati federazione** e scaricare quest'ultimo oppure aprirlo nel browser.
10. Nell'elemento radice **EntityDescriptor** dovrebbe trovarsi un attributo **entityID** nel formato `https://sts.windows.net/` seguito da un GUID specifico per il tenant (denominato "ID tenant"). Copiare questo valore, che verrà usato come **URL dell'autorità di certificazione**. L'applicazione verrà configurata in questo modo più avanti.

### <a name="secrets"> </a>Aggiungere informazioni di Azure Active Directory all'applicazione
1. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.
2. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.
3. Fare clic su **Azure Active Directory** e quindi su **Avanzata** in **Modalità di gestione**. Incollare i valori relativi all'ID client e all'URL dell'autorità di certificazione ottenuti in precedenza. Fare quindi clic su **OK**.
   
   ![][1]
   
   Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.
4. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Azure Active Directory, impostare **Azione da eseguire quando la richiesta non è autenticata** su **Accedi con Azure Active Directory**. Per poter usare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate ad Azure Active Directory per l'autenticazione.
5. Fare clic su **Save**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'app.

## <a name="optional-configure-a-native-client-application"></a>(Facoltativo) Configurare un'applicazione client nativa
Azure Active Directory consente inoltre di registrare i client nativi, il che offre maggiore controllo sul mapping delle autorizzazioni. È utile se si desidera eseguire l'account di accesso mediante una libreria, come ad esempio il **Active Directory Authentication Library**.

1. Passare a **Active Directory** nel [portale di Azure classico]
2. Selezionare la directory, quindi la scheda **Applicazioni** nella parte superiore. Fare clic su **AGGIUNGI** in basso per creare una nuova registrazione dell'app.
3. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
4. Nella procedura guidata di aggiunta applicazione immettere un **Nome** per l'applicazione e fare clic sul tipo **Applicazione client nativa**. Fare quindi clic per continuare.
5. Nella casella **URI di reindirizzamento** immettere l'endpoint del sito */.auth/login/done*, usando lo schema HTTPS. Questo valore deve essere simile a *https://contoso.azurewebsites.net/.auth/login/done*. Se si crea un'applicazione Windows, usare invece il [SID pacchetto](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) come URI.
6. Dopo aver aggiunto l'applicazione nativa, fare clic sulla scheda **Configura** . Trovare l’ **ID Client** e prendere nota del valore.
7. Scorrere verso il basso fino alla sezione **Autorizzazioni per altre applicazioni** e fare clic su **Aggiungi applicazione**.
8. Cercare l'applicazione Web registrata in precedenza e fare clic sull'icona più. Quindi, fare clic sul segni di spunta per chiudere la finestra di dialogo. Se l'applicazione Web non viene trovata, passare alla relativa registrazione e aggiungere un nuovo URL di risposta, ad esempio la versione HTTP dell'URL corrente, fare clic per salvare e quindi ripetere questi passaggi. L'applicazione dovrebbe essere visualizzata nell'elenco.
9. Nella nuova voce appena aggiunta aprire l'elenco a discesa **Autorizzazioni delegate** e selezionare **Accesso (nomeApp)**. Fare quindi clic su **Salva**.

Ora è stata configurata un'applicazione client nativa che può accedere all'applicazione del servizio App.

## <a name="related-content"> </a>Contenuti correlati
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[portale di Azure]: https://portal.azure.com/
[portale di Azure classico]: https://manage.windowsazure.com/
[metodo alternativo]:#advanced



<!--HONumber=Nov16_HO3-->


