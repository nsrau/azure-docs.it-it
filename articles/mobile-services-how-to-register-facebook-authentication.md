<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registrazione delle app per l'autenticazione di Facebook con Servizi mobili

In questo argomento viene illustrato come registrare le app in modo da poter utilizzare Facebook per l'autenticazione con Servizi mobili di Azure.

> [WACOM.NOTE] Questa esercitazione riguarda [Servizi mobili di Azure][], una soluzione che consente di creare applicazioni scalabili per dispositivi mobili compatibili con qualsiasi piattaforma. Servizi mobili semplifica attività come la sincronizzazione di dati, l'autenticazione di utenti e l'invio di notifiche push. Questa pagina supporta l'esercitazione [Introduzione all'autenticazione in Servizi mobili][] che illustra come consentire agli utenti l'accesso alla propria app. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione relativa all'[introduzione a Servizi mobili][].

Per completare la procedura descritta in questo argomento, è necessario disporre di un account di Facebook con un indirizzo di posta elettronica verificato e un numero di cellulare. Per creare un nuovo account di Facebook, visitare il sito [facebook.com][].

1.  Passare al sito Web [Facebook Developers][] e accedere con le proprie credenziali dell'account di Facebook.

2.  (Facoltativo) Se non è ancora stata effettuata la registrazione, fare clic su **Apps** e quindi su **Register as a Developer**, accettare le condizioni e seguire la procedura di registrazione.

    ![][]

3.  Fare clic su **Apps**, quindi su **Create a New App**.

    ![][1]

4.  Scegliere un nome univoco per l'app, selezionare **Apps for Pages**, fare clic su **Create App** e completare la domanda di verifica.

    ![][2]

    L'app verrà registrata in Facebook

5.  Fare clic su **Settings**, digitare il nome di dominio del servizio mobile in **App Domains**, immettere un valore in **Contact Email**, quindi fare clic su **Add Platform** e selezionare **Website**.

    ![][3]

6.  Digitare l'URL del servizio mobile in **Site URL** e quindi fare clic su **Save Changes**.

    ![][4]

7.  Fare clic su **Show**, digitare la propria password, se richiesto, quindi annotare i valori di **App ID** e **App Secret**.

    ![][5]

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
<p>Il segreto dell'app &egrave; una credenziale di sicurezza importante. Non condividere questo valore con altri n&eacute; distribuirlo con l'app.</p>
</div>

8.  Fare clic sulla scheda **Advanced**, immettere l'URL del servizio mobile con l'aggiunta del percorso */login/facebook* in **Valid OAuth redirect URIs**, quindi fare clic su **Save Changes**.

    > [WACOM.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso *signin-facebook*, ovvero il servizio mobile come servizio .NET, ad esempio `https://todolist.azure-mobile.net/signin-facebook`.

    ![][6]

9.  L'account di Facebook per il quale è stata definita la nuova app, ha la funzione di amministratore dell'app e dispone quindi dell'accesso all'app con privilegi di amministratore. Per autenticare altri account di Facebook, questi devono avere accesso all'app. Questo passaggio garantisce l'accesso pubblico in modo che l'app possa autenticare altri account di Facebook. Fare clic su **Status & Review**, quindi su **Yes** per abilitare l'accesso pubblico.

    ![][7]

È ora possibile utilizzare un account di accesso di Facebook per l'autenticazione nell'app fornendo i valori dell'ID e del segreto dell'app a Servizi mobili.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Servizi mobili di Azure]: http://azure.microsoft.com/it-it/services/mobile-services/
  [Introduzione all'autenticazione in Servizi mobili]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-ios-get-started-users/
  [introduzione a Servizi mobili]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-ios-get-started/
  [facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
  [Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
  []: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
  [1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
  [2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
  [3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
  [4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
  [5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
  [6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png
  [7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
