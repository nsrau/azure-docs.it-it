<properties 
	pageTitle="Come configurare l'autenticazione di Azure Active Directory per un'applicazione dei servizi app" 
	description="Informazioni su come configurare l'autenticazione di Azure Active Directory per un'applicazione dei servizi app." 
	authors="mattchenderson" 
	services="app-service\mobile" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="mahender"/>

# Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come configurare i servizi app di Azure per usare Azure Active Directory come provider di autenticazione.

> [AZURE.NOTE]Questo argomento illustra le modalità di utilizzo della funzione di autenticazione/autorizzazione del servizio app che, nella maggior parte delle applicazioni, sostituisce il gateway del servizio app. Se si usa il gateway, vedere la sezione relativa al [metodo alternativo], in cui le Note indicano le principali differenze di cui tener conto quando si usa il gateway.


## <a name="express"> </a>Configurare Azure Active Directory usando le impostazioni rapide

13. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.

14. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.

15. Fare clic su **Azure Active Directory** e quindi su **Express** in **Modalità di gestione**.

16. Fare clic su **OK** per registrare l'applicazione in Azure Active Directory. Verrà creata una nuova registrazione. Se invece si desidera scegliere una registrazione esistente, fare clic su **Seleziona un'app esistente** e quindi cercare il nome di una registrazione creata in precedenza all'interno del tenant. Fare clic sulla registrazione per selezionarla e fare clic su **OK**. Quindi, fare clic su **OK** nel pannello Impostazioni di Azure Active Directory.

    ![][0]
	
	Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.

17. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Azure Active Directory, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Azure Active Directory**. Per poter usare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate ad Azure Active Directory per l'autenticazione.

17. Fare clic su **Save**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'app.

## <a name="advanced"> </a>(Metodo alternativo) Configurare manualmente Azure Active Directory con impostazioni avanzate
È inoltre possibile scegliere di specificare manualmente le impostazioni di configurazione. Questa è la soluzione migliore se il tenant AAD che si desidera utilizzare è diverso dal tenant con cui si accede ad Azure. Per completare la configurazione, è innanzitutto necessario creare una registrazione in Azure Active Directory. È quindi necessario fornire alcune informazioni di registrazione al servizio app.

### <a name="register"> </a>Registrare l'applicazione con Azure Active Directory

1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l'**URL**. L'URL verrà utilizzato per configurare l'app di Azure Active Directory.

3. Accedere al [portale di Azure classico] e passare ad **Active Directory**.

    ![][2]

4. Selezionare la directory, quindi la scheda **Applicazioni** nella parte superiore. Fare clic su **AGGIUNGI** in basso per creare una nuova registrazione dell'app.

5. Fare clic su **Add an application my organization is developing**.

6. Nella procedura guidata di aggiunta applicazione immettere un nome per l'applicazione in **Name** e fare clic sul tipo **Web Application And/Or Web API**. Fare quindi clic per continuare.

7. Nella casella **URL ACCESSO** incollare l'URL dell'applicazione copiato in precedenza. Immettere lo stesso URL nella casella **URI ID app**. Fare quindi clic per continuare.

8. Una volta aggiunta l'applicazione, fare clic sulla scheda **Configura**. Modificare il valore di **URL di risposta** in **Single Sign-on** in modo che corrisponda all'URL dell'applicazione con il percorso _/.auth/login/aad/callback_ come suffisso. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assicurarsi che sia in uso lo schema HTTPS.

    ![][3]
	
	
	> [AZURE.NOTE]Se si usa il gateway del servizio app, anziché la funzionalità Autenticazione/Autorizzazione, l'URL di risposta usa l'URL del gateway URL con il percorso _/signin-aad_.


9. Fare clic su **Save**. Copiare quindi l'**ID client** per l'app. L'applicazione verrà configurata in questo modo più avanti.

10. Nella barra dei comandi in basso, fare clic su **Visualizza endpoint**, copiare l'URL del **documento metadati federazione** e scaricare quest'ultimo oppure aprirlo nel browser.

11. All'interno dell'elemento radice **EntityDescriptor** dovrebbe trovarsi un attributo **entityID** nel formato `https://sts.windows.net/` seguito da un GUID specifico per il tenant (denominato "ID tenant"). Copiare questo valore, che verrà usato come **URL dell'autorità di certificazione**. L'applicazione verrà configurata in questo modo più avanti.

### <a name="secrets"> </a>Aggiungere informazioni di Azure Active Directory all'applicazione

> [AZURE.NOTE]Se si usa il gateway del servizio app, ignorare questa sezione e accedere al gateway nel portale. Selezionare **Impostazioni**, **Identità** e quindi **Azure Active Directory**. Incollare l'ID client e aggiungere l'ID tenant all'elenco **Tenant consentiti**. Fare clic su **Salva**.


13. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.

14. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.

15. Fare clic su **Azure Active Directory** e quindi su **Avanzata** in **Modalità di gestione**. Incollare i valori relativi all'ID client e all'URL dell'autorità di certificazione ottenuti in precedenza. Fare quindi clic su **OK**.

    ![][1]
	
	Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.

17. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati da Azure Active Directory, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Azure Active Directory**. Per poter usare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate ad Azure Active Directory per l'autenticazione.

17. Fare clic su **Save**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'app.

## (Facoltativo) Configurare un'applicazione client nativa

Azure Active Directory consente inoltre di registrare i client nativi, il che offre maggiore controllo sul mapping delle autorizzazioni. È utile se si desidera eseguire l'account di accesso mediante una libreria, come ad esempio il **Active Directory Authentication Library**.

1. Passare a **Active Directory** nel [Portale di Azure classico]

2. Selezionare la directory, quindi la scheda **Applicazioni** nella parte superiore. Fare clic su **AGGIUNGI** in basso per creare una nuova registrazione dell'app.

3. Fare clic su **Add an application my organization is developing**.

4. Nella procedura guidata di aggiunta applicazione immettere un **Nome** per l'applicazione e fare clic sul tipo **Applicazione client nativa**. Fare quindi clic per continuare.

5. Nella casella **URI di reindirizzamento** immettere l’endpoint del sito _/.auth/login/done_, utilizzando lo schema HTTPS. Questo valore dovrebbe essere simile a \__https://contoso.azurewebsites.net/.auth/login/done_.

6. Dopo aver aggiunto l'applicazione nativa, fare clic sulla scheda **Configura**. Trovare l’**ID Client** e prendere nota del valore.

7. Scorrere verso il basso fino alla sezione **Autorizzazioni per altre applicazioni** e fare clic su **Aggiungi applicazione**.

8. Cercare l'applicazione Web registrata in precedenza e fare clic sull'icona più. Quindi, fare clic sul segni di spunta per chiudere la finestra di dialogo.

9. Sulla nuova voce appena aggiunta, aprire l'elenco a discesa **Delega autorizzazioni** e selezionare **Accesso (appName)**. Fare quindi clic su **Salva**.

Ora è stata configurata un'applicazione client nativa che può accedere all'applicazione del servizio App.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[portale di Azure]: https://portal.azure.com/
[portale di Azure classico]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[metodo alternativo]: #advanced

<!---HONumber=AcomDC_1203_2015-->