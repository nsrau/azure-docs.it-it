<properties 
	pageTitle="Come configurare l'autenticazione di Azure Active Directory per un'applicazione dei servizi app" 
	description="Informazioni su come configurare l'autenticazione di Azure Active Directory per un'applicazione dei servizi app." 
	authors="mattchenderson,wesmc7777" 
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
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory

Questo argomento descrive come configurare i servizi app di Azure per usare Azure Active Directory come provider di autenticazione. 

## <a name="register"> </a>Registrare l'applicazione con Azure Active Directory

1. Accedere all'[anteprima del portale di gestione di Azure] e passare al gateway dei servizi app.

2. In **Impostazioni**, scegliere **Identità**, quindi selezionare **Azure Active Directory**. Copiare il valore **URL APP**. Assicurarsi che sia in uso lo schema HTTPS.

    ![][1]

3. Accedere al [portale di gestione di Azure] e passare ad **Active Directory**.

    ![][2] 

4. Selezionare la directory, quindi la scheda **Applicazioni** nella parte superiore. Fare clic su **AGGIUNGI** in basso per creare una nuova registrazione dell'app. 

5. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

6. Nella procedura guidata di aggiunta applicazione immettere un nome per l'applicazione in **Nome** e fare clic sul tipo **Applicazione Web e/o API Web**. Fare quindi clic per continuare.

7. Nella casella **URL accesso** incollare l'ID dell'app copiato dalle impostazioni relative al provider di identità Active Directory del gateway. Immettere lo stesso identificatore univoco della risorsa nella casella **URI ID app**. Fare quindi clic per continuare.

8. Una volta aggiunta l'applicazione, fare clic sulla scheda **Configura**. Modificare il valore di **URL di risposta** in **Single Sign-on** in modo che sia l'URL del gateway con il percorso _/signin-aad_ come suffisso. Ad esempio, `https://contosogateway.azurewebsites.net/signin-aad`. Assicurarsi che sia in uso lo schema HTTPS.

    ![][3]

9. Fare clic su **Salva**. Copiare quindi l'**ID client** per l'app.

## <a name="secrets"> </a>Aggiungere le informazioni di Azure Active Directory all'app mobile

10. Tornare all'anteprima del portale di gestione e al pannello **Autenticazione utente** per il gateway in uso. Incollare l'**ID client** per il provider di identità Azure Active Directory.
  
11. Nell'elenco **Tenant consentiti** è necessario aggiungere il dominio della directory in cui è stata registrata l'applicazione, ad esempio contoso.onmicrosoft.com. Per individuare il dominio predefinito, fare clic sulla scheda**Domini** nel tenant di Azure Active Directory. Aggiungere il nome di dominio all'elenco **Tenant consentiti**, quindi fare clic su **Salva**.  

È ora possibile usare Azure Active Directory per l'autenticazione nell'app. 

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../includes/app-service-mobile-related-content-get-started-users.md)]

Autenticare gli utenti della propria app mobile con Single Sign-On di Azure Active Directory: [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-how-to-configure-active-directory-authentication/app-service-aad-settings.png
[2]: ./media/app-service-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Anteprima del portale di gestione di Azure]: https://portal.azure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[ios-adal]: app-service-mobile-dotnet-backend-xamarin-ios-aad-sso-preview.md

<!--HONumber=49-->