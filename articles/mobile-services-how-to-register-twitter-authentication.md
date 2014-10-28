<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registrazione delle app per l'autenticazione di Twitter con Servizi mobili

In questo argomento viene illustrato come registrare le proprie app in modo da poter utilizzare Twitter per l'autenticazione con Servizi mobili di Azure.

<div class="dev-callout"><b>Nota</b>
<p>Per completare la procedura descritta in questo argomento, &egrave; necessario disporre di un account Twitter con un indirizzo di posta elettronica verificato. Per creare un nuovo account Twitter, visitare il sito <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.</p>
</div>

1.  Passare al sito Web [Twitter Developers][], accedere con le credenziali dell'account Twitter, quindi fare clic su **Create a new application**.

    ![][]

2.  Immettere i valori per l'app in **Name**, **Description** e **Website**, quindi digitare l'URL del servizio mobile con l'aggiunta del percorso */login/twitter* in **Callback URL**.

    > [WACOM.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso *signin-twitter*, ovvero il servizio mobile come servizio .NET, ad esempio `https://todolist.azure-mobile.net/signin-twitter`.

    ![][1]

3.  Nella parte inferiore della pagina, leggere e accettare le condizioni, digitare le parole CAPTCHA corrette e quindi fare clic su **Create your Twitter application**.

    ![][2]

    L'app verrà registrata e verranno visualizzati i dettagli dell'applicazione.

4.  Prendere nota dei valori in **Consumer key** e **Consumer secret**.

    ![][3]

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
<p>Il segreto consumer &egrave; un'importante credenziale di sicurezza. Non condividere questo valore con altri n&eacute; distribuirlo con l'app.</p>
</div>

5.  Fare clic sulla scheda **Settings**, scorrere verso il basso e selezionare **Allow this application to be used to sign in with Twitter**, quindi fare clic su **Update this Twitter application's settings**.

    ![][4]

È ora possibile utilizzare un account di accesso Twitter per l'autenticazione nell'app fornendo i valori di chiave e segreto consumer a Servizi mobili.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [twitter.com]: http://go.microsoft.com/fwlink/p/?LinkID=268287
  [Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  []: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
  [1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
  [2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
  [3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
  [4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png
