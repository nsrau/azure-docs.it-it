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
	ms.date="10/29/2015" 
	ms.author="mahender"/>

# Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come configurare i servizi app di Azure per usare Azure Active Directory come provider di autenticazione.


	> [AZURE.NOTE] This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. If using the gateway, please see the [alternative method]. Differences that apply to using the gateway are called out in notes throughout that section.


## <a name="express"> </a>Configurare Azure Active Directory utilizzando le impostazioni rapide

13. Nel [Portale di gestione di Azure] passare all'applicazione desiderata. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.

14. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.

15. Fare clic su **Azure Active Directory** e quindi su **Express** in **Modalità di gestione**.

16. Fare clic su **OK** per registrare l'applicazione in Azure Active Directory. Verrà creata una nuova registrazione. Se si desidera scegliere invece una registrazione esistente, fare clic su **Select an existing app** e quindi cercare il nome di una registrazione creata in precedenza all'interno del tenant. Fare clic sulla registrazione per selezionarla e fare clic su **OK**. Quindi, fare clic su **OK** nel pannello Impostazioni di Azure Active Directory.

    ![][0]
	
16. Per impostazione predefinita, il servizio app consente l'accesso ma non pone limiti per quanto riguarda il contenuto e le interfacce API accessibili nel sito. Questo è compito del codice dell'app. Se si desidera proteggere interamente il sito mediante la funzionalità di accesso di Azure Active Directory, modificare l'elenco a discesa **Action to take when request is not authenticated** in modo da poter utilizzare l'opzione **Azure Active Directory**. Ciò richiede che tutte le richieste siano autenticate. Le richieste non autenticate verranno reindirizzate per l'accesso con Azure Active Directory.

17. Fare clic su **Save**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'app.

## <a name="advanced"> </a>(Metodo alternativo) Configurare manualmente Azure Active Directory con impostazioni avanzate
È inoltre possibile scegliere di specificare manualmente le impostazioni di configurazione. Questa è la soluzione migliore se il tenant AAD che si desidera utilizzare è diverso dal tenant con cui si accede ad Azure. Per completare la configurazione, è innanzitutto necessario creare una registrazione in Azure Active Directory. È quindi necessario fornire alcune informazioni di registrazione al servizio app.

### <a name="register"> </a>Registrare l'applicazione con Azure Active Directory

1. Accedere all'[anteprima del portale di gestione di Azure] e passare all'applicazione. Copiare l'**URL**. L'URL verrà utilizzato per configurare l'app di Azure Active Directory.

3. Accedere al [portale di gestione di Azure] e passare ad **Active Directory**.

    ![][2]

4. Selezionare la directory, quindi la scheda **Applicazioni** nella parte superiore. Fare clic su **AGGIUNGI** in basso per creare una nuova registrazione dell'app.

5. Fare clic su **Add an application my organization is developing**.

6. Nella procedura guidata di aggiunta applicazione immettere un nome per l'applicazione in **Name** e fare clic sul tipo **Web Application And/Or Web API**. Fare quindi clic per continuare.

7. Nella casella **URL ACCESSO** incollare l'URL dell'applicazione copiato in precedenza. Immettere lo stesso URL nella casella **URI ID app**. Fare quindi clic per continuare.

8. Una volta aggiunta l'applicazione, fare clic sulla scheda **Configura**. Modificare il valore di **URL di risposta** in **Single Sign-on** in modo che corrisponda all'URL dell'applicazione con il percorso _/.auth/login/aad/callback_ come suffisso. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assicurarsi che sia in uso lo schema HTTPS.

    ![][3]
	
	
	> [AZURE.NOTE]Se si utilizza il gateway del servizio app anziché la funzionalità Autenticazione/Autorizzazione, l'URL di risposta utilizza l'URL del gateway URL con il percorso _/signin-aad_.


9. Fare clic su **Save**. Copiare quindi l'**ID client** per l'app. L'applicazione verrà configurata in questo modo più avanti.

10. Nella barra dei comandi in basso, fare clic su **Visualizza endpoint**, copiare L'URL del **documento metadati federazione** e scaricare quest'ultimo oppure aprirlo nel browser.

11. All'interno dell'elemento **EntityDescriptor** root dovrebbe trovarsi un attributo **entityID** nel formato `https://sts.windows.net/` seguito da un GUID specifico per il tenant (detto "ID tenant"). Copiare questo valore, che verrà utilizzato come **URL dell'autorità di certificazione**. L'applicazione verrà configurata in questo modo più avanti.

### <a name="secrets"> </a>Aggiungere le informazioni di Azure Active Directory all'applicazione


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Azure Active Directory**. Paste in the ClientID and add the tenant ID to the **Allowed Tenants** list. Click **Save**.


13. Tornare all'[Anteprima del portale di gestione di Azure] e passare all'applicazione. Fare clic su **Impostazioni** e quindi su **Autenticazione/Autorizzazione**.

14. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.

15. Fare clic su **Azure Active Directory** e quindi su **Avanzate** in **Modalità di gestione**. Incollare i valori relativi all'ID client e all'URL dell'autorità di certificazione ottenuti in precedenza. Fare quindi clic su **OK**.

    ![][1]
	
16. Per impostazione predefinita, il servizio app consente l'accesso ma non pone limiti per quanto riguarda il contenuto e le interfacce API accessibili nel sito. Questo è compito del codice dell'app. Se si desidera proteggere interamente il sito mediante la funzionalità di accesso di Azure Active Directory, modificare l'elenco a discesa **Action to take when request is not authenticated** in modo da poter utilizzare l'opzione **Azure Active Directory**. Ciò richiede che tutte le richieste siano autenticate. Le richieste non autenticate verranno reindirizzate per l'accesso con Azure Active Directory.

17. Fare clic su **Save**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[anteprima del portale di gestione di Azure]: https://portal.azure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[alternative method]: #advanced

<!---HONumber=Nov15_HO4-->