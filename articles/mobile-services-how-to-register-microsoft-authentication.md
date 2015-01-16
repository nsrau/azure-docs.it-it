<properties pageTitle="Registrazione per l'autenticazione Microsoft - Servizi mobili" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Informazioni su come registrarsi per l'autenticazione Microsoft nell'applicazione Servizi mobili di Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Registrazione delle app per l'uso delle credenziali di accesso di un account Microsoft

Questo argomento descrive come registrare l'app in modo da usare Live Connect come provider di autenticazione per Servizi mobili di Azure. 

>[WACOM.NOTE]Per configurare l'autenticazione dell'account Microsoft per un'app di Windows universale o per offrire un ambiente Single Sign-On To per un'app di Windows Store, vedere [Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft](/it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication).

1. Passare alla pagina <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Applicazioni personali</a> nel Centro sviluppatori Live Connect e, se necessario, accedere con il proprio account Microsoft. 

2. Fare clic su **Crea applicazione**, quindi digitare un nome in **Nome applicazione** e fare clic su **Accetto**.

   	![][1] 

   	L'applicazione verrà registrata con Live Connect.

3. Fare clic su **Impostazioni API**, specificare un valore `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` in **URL di reindirizzamento**, quindi fare clic su **Salva**.

	>[WACOM.NOTE]Per un servizio mobile back-end .NET pubblicato in Azure tramite Visual Studio, l'URL di reindirizzamento è l'URL del servizio mobile con l'aggiunta del percorso _signin-microsoft_, ovvero il servizio mobile come servizio .NET, ad esempio <code>https://todolist.azure-mobile.net/signin-microsoft</code>.  

	![][3]

	Ciò consente di abilitare l'autenticazione dell'account Microsoft per l'app.

	>[WACOM.NOTE]Per una registrazione esistente dell'app di Live Connect, può essere necessario innanzitutto abilitare **Enhanced redirection security**.

4. Fare clic su **App Settings** e annotare i valori di **Client ID** e **Client secret**. 

   	![][2]

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
	<p>Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.</p>
    </div>

È ora possibile usare un account Microsoft per l'autenticazione nell'app fornendo i valori dell'ID e del segreto client a Servizi mobili.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Portale di gestione di Azure]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
