<properties 
	pageTitle="Importare il file delle impostazioni di pubblicazione in Visual Studio 2013 | Servizi mobili" 
	description="Informazioni su come importare il file delle impostazioni di pubblicazione di una sottoscrizione per l'applicazione Servizi mobili di Azure in Visual Studio 2013." 
	documentationCenter="" 
	services="mobile-services" 
	manager="dwrede" 
	editor="" 
	authors="ggailey777"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# Importare il file delle impostazioni di pubblicazione della sottoscrizione in Visual Studio 2013

Prima di poter creare il servizio mobile, è necessario importare il file delle impostazioni di pubblicazione dalla sottoscrizione di Azure in Visual Studio. In questo modo Visual Studio sarà in grado di connettersi ad Azure per conto dell'utente.

>[AZURE.NOTE]A partire da Visual Studio 2013 Update 2, non è più necessario usare un file delle impostazioni di pubblicazione. Visual Studio può connettersi direttamente ad Azure con le credenziali specificate.

1. In Visual Studio 2013 aprire Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi fare clic su **Servizio connesso**. 

	![aggiunta di un servizio connesso](./media/mobile-services-windows-how-to-import-publishsettings/mobile-add-connected-service.png)

2. Nella finestra di dialogo Gestione servizi fare clic su **Crea servizio**, quindi selezionare **&lt;Importa&gt;** da **Sottoscrizione** nella finestra di dialogo Crea servizio mobile.

	![creazione di un nuovo servizio mobile da VS 2013](./media/mobile-services-windows-how-to-import-publishsettings/mobile-create-service-from-vs2013.png)

3. In Import Azure Subscriptions fare clic su **Download subscription file**, accedere al proprio account Azure (se richiesto) e fare clic su **Save** quando nel browser viene richiesto se salvare il file.

	![download del file di sottoscrizione in VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription.png)

	> [AZURE.NOTE]La finestra di accesso viene visualizzata nel browser e potrebbe trovarsi dietro la finestra di Visual Studio. Prendere nota del percorso in cui è stato salvato il file .publishsettings scaricato. Se il progetto è già connesso alla sottoscrizione Azure, è possibile ignorare questo passaggio.

4. Fare clic su **Sfoglia**, passare al percorso in cui è stato salvato il file .publishsettings, selezionare il file, quindi fare clic su **Apri** e poi su **Importa**.

	![importazione della sottoscrizione in VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription-2.png)

	In Visual Studio verranno importati i dati necessari per connettersi alla sottoscrizione di Azure. Se la sottoscrizione include già uno o più servizi mobili esistenti, verranno visualizzati i relativi nomi.

	> [AZURE.NOTE]Dopo l'importazione delle impostazioni di pubblicazione, è consigliabile eliminare il file .publishsettings, in quanto contiene informazioni che possono essere utilizzate da altri utenti per accedere all'account. Proteggere il file se si prevede di conservarlo per l'utilizzo in altri progetti di app connesse.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/how-to-guides/register-for-single-sign-on/
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-users-js/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->