<properties
	pageTitle="Come configurare l'autenticazione dell'account Microsoft per un'applicazione dei servizi app"
	description="Informazioni su come configurare l'autenticazione dell'account Microsoft per un'applicazione dei servizi app."
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/24/2016"
	ms.author="mahender"/>

# Come configurare l’applicazione del servizio app per usare l'account di accesso Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare l'account Microsoft come provider di autenticazione.

## <a name="register-windows-dev-center"> </a>Registrare l'app di Windows in Windows Dev Center

Le app di Windows universale e di Windows Store devono essere registrate in Windows Dev Center. Ciò consentirà di configurare più facilmente le notifiche push per l'app.

>[AZURE.NOTE]Per le app per Windows Phone 8, Windows Phone 8.1 Silverlight e non per Windows è possibile saltare questa sezione. Se le notifiche push per l'app di Windows sono già state configurate, è possibile ignorare questa sezione.

1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l'**URL**. Verrà usato per configurare l'app con l'account Microsoft.

2. Se l'app non è ancora stata registrata, passare a [Windows Dev Center](https://dev.windows.com/dashboard/Application/New), accedere con il proprio account Microsoft, digitare un nome per l’app, verificarne la disponibilità e quindi fare clic su **Riservare un nome dell'app**.

3. Aprire il progetto dell’app Windows Visual Studio, poi in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto dell’app di Windows Store, quindi scegliere **Store** > **Associa app allo Store**.

  	![](./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-windows-store-association.png)

4. Nella procedura guidata fare clic su **Accedi** e accedere con il proprio account Microsoft, selezionare il nome dell'app riservato, quindi fare clic su **Avanti** > **Associa**. Per un'app di Windows 8.1 universale, ripetere i passaggi 4 e 5 per il progetto Windows Phone Store.

6. Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi** > **Notifiche push**.

7. Nella pagina **Notifiche push** fare clic su **Sito di servizi Live** in **Servizi notifica Push Windows (WNS) e Servizi mobili di Microsoft Azure**.

Quindi si configurerà l'autenticazione dell'account Microsoft per l'app di Windows.


## <a name="register-microsoft-account"> </a>Registrare l'app con l'account Microsoft

Se l'app di Windows è già stata registrata nella sezione precedente, è possibile saltare al passaggio 4.

1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l'**URL**. Verrà usato per configurare l'app con l'account Microsoft.

2. Passare alla pagina [My Applications] di Microsoft Account Developer Center e, se necessario, accedere con il proprio account Microsoft.

3. Fare clic su **Crea applicazione**, quindi digitare un nome in **Nome applicazione** e fare clic su **Accetto**.

4. Fare clic su **Impostazioni API**, selezionare **Sì** per **App client desktop o mobile**, specificare l'**URL di reindirizzamento** per l'applicazione, quindi fare clic su **Salva**.
 
	![][0]

	>[AZURE.NOTE]L'URI di reindirizzamento corrisponde all'URL dell'applicazione con l'aggiunta del percorso _/.auth/login/microsoftaccount/callback_. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Assicurarsi che sia in uso lo schema HTTPS.

6. Fare clic su **App Settings** e annotare i valori di **Client ID** e **Client secret**.

    > [AZURE.IMPORTANT] Il segreto client è un'importante credenziale di sicurezza. Non condividere segreto client con altri e non distribuirlo all'interno di un'applicazione client.

## <a name="secrets"> </a>Aggiungere le informazioni dell'account Microsoft all'applicazione del servizio app

1. Nel [portale Azure] passare all'applicazione, fare clic su **Impostazioni** > **Autenticazione/autorizzazione**.

2. Se la funzionalità di autenticazione/autorizzazione non è abilitata, impostare l'opzione in modo da **abilitarla**.

3. Fare clic su **Account Microsoft**. Incollare i valori ID App e Segreto app ottenuti in precedenza e facoltativamente abilitare tutti gli ambiti richiesti dall'applicazione. Fare quindi clic su **OK**.

    ![][1]

	Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.

4. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati dall’account Microsoft, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Account Microsoft**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate all’account Microsoft per l'autenticazione.

5. Fare clic su **Save**.

È ora possibile usare l'account Microsoft per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuti correlati

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portale Azure]: https://portal.azure.com/
[portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->