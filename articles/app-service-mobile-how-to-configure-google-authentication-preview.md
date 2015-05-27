<properties 
	pageTitle="Come configurare l'autenticazione Google per un'applicazione dei servizi app"
	description="Informazioni su come configurare l'autenticazione Google per un'applicazione dei servizi app." 
    services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson,ggailey777" 
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

# Come configurare un'applicazione per usare l'account di accesso di Google

Questo argomento descrive come configurare i servizi app di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

## <a name="register"> </a>Registrare l'applicazione con Google

1. Passare al sito Web delle <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API di Google</a>, accedere con le credenziali dell'account Google, fare clic su **Create Project**, specificare un valore in **Project name** e quindi fare clic su **Create**.

2. Nella barra di spostamento a sinistra fare clic su **API & Auth**. Fare clic su **Consent screen**. Selezionare il proprio indirizzo in **Email Address**, quindi immettere un nome prodotto in **Product Name**. Fare quindi clic su **Salva**.

3. In **API & Auth** selezionare anche **APIs** e abilitare **Google+ API**.

4. Sempre in **API & Auth** selezionare **Credentials**, quindi **Create new Client ID**.

5. Selezionare **Web application**. Digitare l'URL del gateway dei servizi app in **Authorized JavaScript Origins**, quindi sostituire l'URL generato in **Authorized Redirect URI** con l'URL del gateway con il percorso _/signin-google_ aggiunto come suffisso. Ad esempio: `https://contoso.azurewebsites.net/signin-google`. Assicurarsi che sia in uso lo schema HTTPS. Fare clic su **Create client ID**.

     ![][0]

6. Nella schermata successiva, in **Client ID for web applications**, prendere nota dei valori di **Client ID** e **Client secret**.

    > [AZURE.IMPORTANT]Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.


## <a name="secrets"> </a>Aggiungere le informazioni di Google all'app per dispositivi mobili

7. Accedere [portale di gestione di Azure] e passare al gateway dei servizi app.

8. In **Impostazioni** scegliere **Identità**, quindi selezionare **Google**. Incollare i valori relativi all'ID e al segreto client ottenuti in precedenza. Fare quindi clic su **Salva**.

     ![][1]


È ora possibile usare un account Google per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-how-to-configure-google-authentication/app-service-google-redirect.png
[1]: ./media/app-service-how-to-configure-google-authentication/app-service-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portale di gestione di Azure]: https://portal.azure.com/

<!--HONumber=54-->