<properties urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Importare il file delle impostazioni di pubblicazione in Visual Studio 2013 | Servizi mobili" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="mobile-services" solutions="" manager="dwrede" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Importazione del file delle impostazioni di pubblicazione della sottoscrizione in Visual Studio 2013

Prima di poter creare il servizio mobile, è necessario importare il file delle impostazioni di pubblicazione dalla sottoscrizione di Azure in Visual Studio. In questo modo Visual Studio sarà in grado di connettersi ad Azure per conto dell'utente.  

>[WACOM.NOTE]A partire da Visual Studio 2013 Update 2, non è più necessario usare un file delle impostazioni di pubblicazione. Visual Studio può connettersi direttamente ad Azure con le credenziali specificate.

1. In Visual Studio 2013 aprire Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi fare clic su **Servizio connesso**. 

	![add connected service](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. Nella finestra di dialogo Gestione servizi fare clic su **Crea servizio**, quindi selezionare **&lt;Importa&gt;** da **Sottoscrizione** nella finestra di dialogo Crea servizio mobile.  

	![create a new mobile service from VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. In Importa sottoscrizioni di Azure fare clic su **Scarica file di sottoscrizione**, accedere al proprio account Azure (se richiesto) e fare clic su **Salva** quando nel browser viene richiesto se salvare il file.

	![download subscription file in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	<div class="dev-callout"><strong>Nota</strong> <p>La finestra di accesso viene visualizzata nel browser e potrebbe trovarsi dietro la finestra di Visual Studio. Prendere nota del percorso in cui è stato salvato il file .publishsettings scaricato. Se il progetto è già connesso alla sottoscrizione di Azure, è possibile ignorare questo passaggio.</p></div> 

4. Fare clic su **Sfoglia**, passare al percorso in cui è stato salvato il file publishsettings, selezionare il file, quindi fare clic su **Apri** e infine su **Importa**. 

	![import subscription in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	In Visual Studio verranno importati i dati necessari per connettersi alla sottoscrizione di Azure. Se la sottoscrizione include già uno o più servizi mobili esistenti, verranno visualizzati i relativi nomi. 

	<div class="dev-callout"><strong>Nota sulla sicurezza</strong> <p>Dopo l'importazione delle impostazioni di pubblicazione, è consigliabile eliminare il file .publishsettings, in quanto contiene informazioni che possono essere usate da altri utenti per accedere all'account. Proteggere il file se si prevede di conservarlo per l'uso in altri progetti di app connesse.</p></div>

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Single Sign-On per app di Windows Store tramite Live Connect]: /it-it/develop/mobile/how-to-guides/register-for-single-sign-on/
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet/
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/get-started-with-users-js/

[Portale di gestione di Azure]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
