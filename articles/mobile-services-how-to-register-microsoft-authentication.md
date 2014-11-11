<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrazione delle app per l'utilizzo delle credenziali di accesso di un account Microsoft

Questo argomento illustra come registrare l'app in modo da usare Live Connect come provider di autenticazione per Servizi mobili di Azure.

> [WACOM.NOTE]Per configurare l'autenticazione dell'account Microsoft per un'app per Windows 8.1 o Windows Phone 8.1, vedere [Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft][Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft].

1.  Passare alla pagina [Applicazioni personali][Applicazioni personali] nel Centro sviluppatori Live Connect e, se necessario, accedere con il proprio account Microsoft.

2.  Fare clic su **Crea applicazione**, quindi digitare un nome in **Nome applicazione** e fare clic su **Accetto**.

    ![][0]

    L'applicazione verrà registrata con Live Connect.

3.  Fare clic su **Impostazioni API**, specificare un valore di `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` in **URL di reindirizzamento**, quindi fare clic su **Salva**.

    > [WACOM.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso *signin-microsoft*, ovvero il servizio mobile come servizio .NET, ad esempio `https://todolist.azure-mobile.net/signin-microsoft`.

    ![][1]

    Ciò consente di abilitare l'autenticazione dell'account Microsoft per l'app.

    > [WACOM.NOTE]Per una registrazione esistente dell'app di Live Connect, può essere necessario innanzitutto abilitare **Enhanced redirection security**.

4.  Fare clic su **App Settings** e annotare i valori di **Client ID** e **Client secret**.

    ![][2]

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
<p>Il segreto client &egrave; un'importante credenziale di sicurezza. Non condividere questo valore con altri n&eacute; distribuirlo con l'app.</p>
</div>

È ora possibile utilizzare un account Microsoft per l'autenticazione nell'app fornendo i valori dell'ID e della chiave privata client a Servizi mobili.



  [Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft]: /it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
  [Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [0]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png
  [2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
