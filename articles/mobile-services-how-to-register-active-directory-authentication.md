<properties urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Effettuare la registrazione per l'autenticazione di Azure Active Directory - Sevizi mobili" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Informazioni su come effettuare la registrazione per l'autenticazione di Azure Active Directory nell'app di Servizi mobili." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory

Questo argomento illustra come registrare le app in modo da usare Azure Active Directory come provider di autenticazione per Servizi mobili di Azure. 


>[AZURE.NOTE] I passaggi descritti in questo argomento devono essere usati con l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/) se si vogliono usare [operazioni di accesso dirette dal servizio](http://msdn.microsoft.com/it-it/library/azure/dn283952.aspx) con l'app. Se invece l'app richiede [operazioni di accesso dirette dal client](http://msdn.microsoft.com/it-it/library/azure/jj710106.aspx) per Azure Active Directory e un servizio mobile back-end .NET, sarà necessario eseguire prima di tutto l'esercitazione [Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library](/it-it/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/).


1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sul servizio mobile.

    ![][1]

2. Fare clic sulla scheda **Identità** per il servizio mobile. 

    ![][2]

3. Scorrere fino alla sezione relativa al provider di identità **Azure Active Directory** e copiare il valore **URL app** elencato in tale punto.

    ![][3]

4. Passare a **Active Directory** nel portale di gestione, quindi fare clic sulla directory.

    ![][4] 

5. Fare clic sulla scheda **Applicazioni** nella parte superiore, quindi fare clic su **AGGIUNGI** per aggiungere un'app. 

    ![][10]

6. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

7. Nella procedura guidata di aggiunta applicazione immettere un nome per l'applicazione in **Nome** e fare clic sul tipo   **Applicazione Web e/o API Web**. Fare quindi clic per continuare.

    ![][5]

8. Nella casella **URL ACCESSO** incollare l'ID dell'app copiato dalle impostazioni relative al provider di identità Active Directory del servizio mobile. Immettere lo stesso identificatore univoco della risorsa nella casella **URI ID app**. Fare quindi clic per continuare.
 
    ![][6]


9. Una volta aggiunta l'applicazione, fare clic sulla scheda **Configura**. Fare clic per copiare il valore **ID client** per l'app.

    Se il servizio mobile è stato creato per l'uso del back-end .NET, modificare anche il campo **URL di risposta** in **Single Sign-on** in modo che costituisca l'URL del servizio mobile con l'aggiunta del percorso, _signin-aad_. Ad esempio, `https://todolist.azure-mobile.net/signin-aad`

    ![][8]


10. Tornare alla scheda **Identità** del servizio mobile. Nella parte inferiore incollare l'impostazione **ID client** per il provider di identità Azure Active Directory.

  
11. Nell'elenco **Tenant consentiti** è necessario aggiungere il dominio della directory in cui è stata registrata l'applicazione, ad esempio contoso.onmicrosoft.com. Per individuare il dominio predefinito, fare clic sulla scheda **Domini** in Active Directory.

    ![][11]
 
    Aggiungere il nome di dominio all'elenco **Tenant consentiti** quindi fare clic su **Salva**.    


    ![][9]



È ora possibile usare Azure Active Directory per l'autenticazione nell'app. 



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
[11]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png

<!-- URLs. -->
[Portale di gestione di Azure]: https://manage.windowsazure.com/

