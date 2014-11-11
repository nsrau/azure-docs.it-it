<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="" />

Importazione del file delle impostazioni di pubblicazione della sottoscrizione in Visual Studio 2013
====================================================================================================

Prima di poter creare il servizio mobile, è necessario importare il file delle impostazioni di pubblicazione dalla sottoscrizione di Azure in Visual Studio. In questo modo Visual Studio sarà in grado di connettersi ad Azure per conto dell'utente.

1.  In Visual Studio 2013 aprire Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi fare clic su **Servizio connesso**.

    ![aggiunta di un servizio connesso](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2.  Nella finestra di dialogo Gestione servizi fare clic su **Crea servizio**, quindi selezionare **&lt;Importa\>** da **Sottoscrizione** nella finestra di dialogo Crea servizio mobile.

    ![creazione di un nuovo servizio mobile da VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3.  In Import Azure Subscriptions fare clic su **Download subscription file**, accedere al proprio account Azure (se richiesto) e fare clic su **Save** quando nel browser viene richiesto se salvare il file.

    ![download del file di sottoscrizione in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

    **Nota**

    La finestra di accesso viene visualizzata nel browser e potrebbe trovarsi dietro la finestra di Visual Studio. Prendere nota del percorso in cui è stato salvato il file .publishsettings scaricato. Se il progetto è già connesso alla sottoscrizione di Azure, è possibile ignorare questo passaggio.

4.  Fare clic su **Sfoglia**, passare al percorso in cui è stato salvato il file .publishsettings, selezionare il file, quindi fare clic su **Apri** e infine su **Importa**.

    ![importazione della sottoscrizione in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

    In Visual Studio verranno importati i dati necessari per connettersi alla sottoscrizione di Azure. Se la sottoscrizione include già uno o più servizi mobili esistenti, verranno visualizzati i relativi nomi.

    **Nota sulla sicurezza**

    Dopo l'importazione delle impostazioni di pubblicazione, è consigliabile eliminare il file .publishsettings, in quanto contiene informazioni che possono essere utilizzate da altri utenti per accedere all'account. Proteggere il file se si prevede di conservarlo per l'utilizzo in altri progetti di app connesse.


