<properties
	pageTitle="Registrarsi per l'autenticazione Microsoft | Microsoft Azure"
	description="Informazioni su come registrarsi per l'autenticazione Microsoft nell'applicazione Servizi mobili di Azure."
	authors="ggailey777"
	services="mobile-services"
	documentationCenter="Mobile"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2016" 
	ms.author="glenga"/>

# Registrare l'app per l'uso dell'account Microsoft per l'autenticazione

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

## Panoramica

Questo argomento illustra come registrare l'app per dispositivi mobili in modo da usare l'account Microsoft come provider di identità con Servizi mobili di Azure. Gli stessi passaggi si applicano all'autenticazione diretta dal servizio e all'autenticazione diretta dal client mediante Live SDK.

##Registrare l'app di Windows Store in Windows Dev Center

Le app di Windows Store devono innanzitutto essere registrate in Windows Dev Center. Con la registrazione l'app di Windows sarà in grado di usare tutte le caratteristiche della funzione Single Sign-On.

>[AZURE.NOTE]Per le app per Windows Phone 8, Windows Phone 8.1 Silverlight e non per Windows è possibile saltare questa sezione.

1. Se l'app non è ancora stata registrata, passare a [Windows Dev Center](https://dev.windows.com/dashboard/Application/New), accedere con il proprio account Microsoft, digitare un nome per l’app, quindi fare clic su **Riservare un nome dell’app**.

3. Aprire il progetto dell’app Windows Visual Studio, poi in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto dell’app di Windows Store, quindi scegliere **Store** > **Associa app allo Store**.

  	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-store-association.png)

5. Nella procedura guidata fare clic su **Accedi** e accedere con il proprio account Microsoft, selezionare il nome dell'app riservato, quindi fare clic su **Avanti** > **Associa**.

6. (Facoltativo) Per un'app 8.1 di Windows universale, ripetere i passaggi da 4 e 5 per il progetto Windows Phone Store.

6. Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi** > **Notifiche push**.

7. Nella pagina **Notifiche push** fare clic su **Sito di servizi Live** in **Servizi notifica Push Windows (WNS) e Servizi mobili di Microsoft Azure**.
 
	Verrà visualizzata la pagina delle impostazioni dell'account Microsoft per l'app.

8. Prendere nota del valore **SID pacchetto**. È possibile salvare il SID nel portale di Azure per abilitare la funzione Single Sign-On e le notifiche push per l'app di Windows.

Quindi si configurerà l'autenticazione dell'account Microsoft per l'app di Windows, iniziando dal passaggio 4 nella sezione successiva.

## Configurare la registrazione all'account Microsoft e connettersi a Servizi mobili

Se l'app di Windows è già stata registrata nella sezione precedente, è possibile saltare al passaggio 2.

1. Per un'app non di Windows Store, passare alla pagina [Applicazioni personali](http://go.microsoft.com/fwlink/p/?LinkId=262039) nel centro per sviluppatori degli account Microsoft, accedere con l'account Microsoft (se richiesto), fare clic su **Crea applicazione**, digitare un **Nome applicazione** poi fare clic su **Accetto**.

   	Questo processo riserva il nome dell'app con l'account Microsoft e visualizza la pagina dell'account Microsoft per l'app.

2. Nella pagina dell'account Microsoft per l'app, fare clic su **Impostazioni API**, abilitare **App client desktop o per dispositivi mobili**, impostare l'URL del servizio mobile come **Dominio di destinazione**, poi specificare uno dei seguenti formati di URL in **URL di reindirizzamento** e fare clic su **Salva**:

	+ **Back-end .NET**: `https://<mobile_service>.azure-mobile.net/signin-microsoft`
	+ **Back-end JavaScript**: `https://<mobile_service>.azure-mobile.net/login/microsoftaccount`

	 >[AZURE.NOTE]Assicurarsi di utilizzare il formato del percorso URL di reindirizzamento corretto per il tipo di back-end di servizi mobili. Quando questo non è corretto, l'autenticazione avrà esito negativo. Il **dominio radice** deve essere compilato automaticamente. &nbsp;

    ![Impostazioni API dell'account Microsoft](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png)


4. Fare clic su **Impostazioni app** e prendere nota dei valori di **ID client**, **segreto client** e **ID di sicurezza del pacchetto**.

   	![Impostazioni app dell'account Microsoft](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png)


    > [AZURE.NOTE] Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app. Solo per le registrazioni delle app di Windows Store il campo relativo all'ID di sicurezza del pacchetto risulta compilato.

4. Nel [portale di Azure classico] fare clic sulla scheda **Identità** relativa al servizio mobile, immettere l'ID client, il segreto client e l'ID di sicurezza del pacchetto ottenuto dal provider di identità, poi fare clic su **Salva**.

	>[AZURE.NOTE]Non è necessario specificare un valore per l'ID di sicurezza del pacchetto per un'app per Windows Phone 8, Windows Phone Store 8.1 Silverlight o non per Windows.

Il servizio mobile e l'app sono ora configurati per l'uso con l'account Microsoft.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[portale di Azure classico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->