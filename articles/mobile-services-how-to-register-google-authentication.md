<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registrazione delle app per l'autenticazione di Google con Servizi mobili

In questo argomento viene illustrato come registrare le proprie app in modo da poter utilizzare Google per l'autenticazione con Servizi mobili di Azure.

<div class="dev-callout"><b>Nota</b>
<p>Per completare la procedura descritta in questo argomento, &egrave; necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div>

1.  Passare al sito Web delle [API di Google][], accedere con le credenziali dell'account Google, fare clic su **Create Project**, specificare un valore in **Project name**, quindi fare clic su **Create**.

    ![][]

2.  Fare clic su **API & Auth**, selezionare **Credentials**, quindi **Create new Client ID**.

    ![][1]

3.  Selezionare **Web application** e digitare l'URL del servizio mobile in **Authorized JavaScript Origins**, sostituire l'URL generato in **Authorized Redirect URI** con l'URL del servizio mobile con l'aggiunta del percorso */login/google*, quindi fare clic su **Create client ID**.

    > [WACOM.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso *signin-google*, ovvero il servizio mobile come servizio .NET, ad esempio `https://todolist.azure-mobile.net/signin-google`.

    ![][2]

4.  In **Client ID for web applications** prendere nota dei valori di **Client ID** e **Client secret**.

    ![][3]

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
<p>Il segreto client &egrave; un'importante credenziale di sicurezza. Non condividere questo valore con altri n&eacute; distribuirlo con l'app.</p>
</div>

È ora possibile utilizzare un account di accesso Google per l'autenticazione nell'app fornendo i valori dell'ID e del segreto client a Servizi mobili.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [API di Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  []: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
