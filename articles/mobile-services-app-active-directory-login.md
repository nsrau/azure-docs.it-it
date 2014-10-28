<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="dwrede" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="dwrede"></tags>

# Registrazione delle app per l'utilizzo delle credenziali di accesso di un account Azure Active Directory

In questo argomento viene illustrato come registrare le app in modo da utilizzare Azure Active Directory come provider di autenticazione per Servizi mobili di Azure.

<div class="dev-callout"><b>Nota</b>
<p>Se si intende fornire anche l'autenticazione basata su client per Single Sign-On (SSO) o per le notifiche push di un'app di Windows Store, &egrave; consigliabile registrare l'app anche con Windows Store. Per ulteriori informazioni, vedere <a href="/it-it/develop/mobile/how-to-guides/register-for-single-sign-on">Registrazione delle app di Windows Store per l'autenticazione con Windows Live Connect</a>.</p>
</div>

1.  Accedere al [portale di gestione di Azure][].

2.  Passare a **Active Directory** nel portale di gestione, quindi fare clic sulla directory.

![][]

1.  Fare clic sulla scheda **Applications**, quindi scegliere **Add an App**.

![][1]

1.  Seguire le indicazioni della creazione guidata di una nuova applicazione scegliendo **Web Application And/Or Web API** per XXX. Abilitare l'accesso Single Sign-On. Quando viene richiesto d inserire **App URL**, incollare l'URL dell'applicazione di Servizi mobili.

2.  \*\*\* MORE TO COME \*\*\*

È ora possibile utilizzare un Azure Active Directory per l'autenticazione nell'app fornendo i valori dell'ID e del segreto client a Servizi mobili.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Registrazione delle app di Windows Store per l'autenticazione con Windows Live Connect]: /it-it/develop/mobile/how-to-guides/register-for-single-sign-on
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png
