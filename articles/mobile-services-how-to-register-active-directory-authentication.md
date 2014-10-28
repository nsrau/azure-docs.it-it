<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Mobile Services application." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Registrazione delle app per l'utilizzo delle credenziali di accesso di un account Azure Active Directory

In questo argomento viene illustrato come registrare le app in modo da utilizzare Azure Active Directory come provider di autenticazione per Servizi mobili di Azure.

> [WACOM.NOTE] Se si desidera fornire l'autenticazione basata su client per Single Sign-On (SSO) con Azure Active Directory, vedere l'esercitazione relativa all'[autenticazione dell'app con il Single Sign-On di Active Directory Authentication Library][].

1.  Accedere al [portale di gestione di Azure][], fare clic su **Mobile Services** e quindi sul servizio mobile.

    ![][]

2.  Fare clic sulla scheda **Identity** per il servizio mobile.

    ![][1]

3.  Scorrere fino alla sezione relativa al provider di identità **Azure Active Directory** e copiare il valore **APP URL** elencato in tale punto.

    ![][2]

4.  Passare a **Active Directory** nel portale di gestione, quindi fare clic sulla directory.

    ![][3]

5.  Fare clic sulla scheda **Applicazioni** nella parte superiore, quindi fare clic su **AGGIUNGI** per aggiungere un'app.

    ![][4]

6.  Fare clic su **Add an application my organization is developing**.

7.  Nella procedura guidata di aggiunta applicazione immettere un nome per l'applicazione in **Name** e fare clic sul tipo **Web Application And/Or Web API**. Fare quindi clic per continuare.

    ![][5]

8.  Nella casella **SIGN-ON URL** incollare l'ID dell'app copiato dalle impostazioni relative al provider di identità Active Directory del servizio mobile. Immettere lo stesso identificatore univoco della risorsa nella casella **URI ID app**. Fare quindi clic per continuare.

    ![][6]

9.  Una volta aggiunta l'applicazione, fare clic sulla scheda **Configura**. Fare clic per copiare il valore **Client ID** per l'app.

    Se il servizio mobile è stato creato per l'uso del back-end .NET, modificare anche il campo **URL di risposta** in **Single Sign-on** in modo che costituisca l'URL del servizio mobile con l'aggiunta del percorso *signin-aad*. Ad esempio, `https://todolist.azure-mobile.net/signin-aad`

    ![][7]

10. Tornare alla scheda **Identity** del servizio mobile. Nella parte inferiore incollare l'impostazione **Client ID** per il provider di identità Azure Active Directory.

11. Nell'elenco **Allowed Tenants** è necessario aggiungere il dominio della directory in cui è stata registrata l'applicazione, ad esempio contoso.onmicrosoft.com. Per individuare il dominio predefinito, fare clic sulla scheda **Domains** in Active Directory.

    ![][8]

    Aggiungere il nome di dominio all'elenco **Allowed Tenants**, quindi fare clic su **Save**.

    ![][9]

È ora possibile utilizzare Azure Active Directory per l'autenticazione nell'app.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [autenticazione dell'app con il Single Sign-On di Active Directory Authentication Library]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
  [2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
  [3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
  [4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
  [5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
  [6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
  [7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
  [8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png
  [9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
