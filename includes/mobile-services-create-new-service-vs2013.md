La procedura seguente consente di creare un nuovo servizio mobile in Azure e di aggiungere codice nel progetto per consentirne l'accesso. Prima di poter creare il servizio mobile, è necessario importare il file .publishsettings dalla sottoscrizione Azure in Visual Studio. In questo modo Visual Studio sarà in grado di connettersi ad Azure per conto dell'utente. Quando si crea un nuovo servizio mobile, è necessario specificare un database SQL di Azure utilizzato per archiviare i dati dell'app.

1.  In Visual Studio 2013 aprire Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi fare clic su **Servizio connesso**.

    ![aggiunta di un servizio connesso][aggiunta di un servizio connesso]

2.  Nella finestra di dialogo Gestione servizi fare clic su **Crea servizio**, quindi selezionare **\<Importa\>** da **Sottoscrizione** nella finestra di dialogo Crea servizio mobile.

    ![creazione di un nuovo servizio mobile da VS 2013][creazione di un nuovo servizio mobile da VS 2013]

3.  In Import Azure Subscriptions fare clic su **Download subscription file**, accedere al proprio account Azure (se richiesto) e fare clic su **Save** quando nel browser viene richiesto se salvare il file.

    ![download del file di sottoscrizione in VS][download del file di sottoscrizione in VS]

    <div class="dev-callout"><strong>Nota</strong> <p>La finestra di accesso viene visualizzata nel browser e potrebbe trovarsi dietro la finestra di Visual Studio. Prendere nota del percorso in cui &egrave; stato salvato il file .publishsettings scaricato. Se il progetto &egrave; gi&agrave; connesso alla sottoscrizione di Azure, &egrave; possibile ignorare questo passaggio.</p></div>

4.  Fare clic su **Sfoglia**, passare al percorso in cui è stato salvato il file .publishsettings, selezionare il file, quindi fare clic su **Apri** e infine su **Importa**.

    ![importazione della sottoscrizione in VS][importazione della sottoscrizione in VS]

    In Visual Studio verranno importati i dati necessari per connettersi alla sottoscrizione di Azure. Se la sottoscrizione include già uno o più servizi mobili esistenti, verranno visualizzati i relativi nomi.

    <div class="dev-callout"><strong>Nota sulla sicurezza</strong> <p>Dopo l'importazione delle impostazioni di pubblicazione, &egrave; consigliabile eliminare il file .publishsettings, in quanto contiene informazioni che possono essere utilizzate da altri utenti per accedere all'account. Proteggere il file se si prevede di conservarlo per l'utilizzo in altri progetti di app connesse.</p></div>

5.  Nella finestra **Crea servizio mobile** selezionare i valori appropriati per **Sottoscrizione** e **Area** in base al servizio mobile, quindi digitare un **Nome** per il servizio mobile.

    <div class="dev-callout"><strong>Nota</strong> <p>I nomi dei servizi mobili devono essere univoci. Se il nome specificato non &egrave; disponibile, accanto a <strong>Nome</strong> verr&agrave; visualizzata una X rossa. </p></div>

6.  In **Database** selezionare **\<Crea un database SQL gratuito\>**, specificare i valori per **Nome utente server**, **Password server** e **Conferma password server**, quindi fare clic su **Crea**.

    ![creazione di un servizio da VS 2013 parte 2][creazione di un servizio da VS 2013 parte 2]

    > [WACOM.NOTE]
    > Nel corso di questa esercitazione verranno creati una nuova istanza e un nuovo server di database SQL. È possibile riutilizzare questo nuovo database e amministrarlo allo stesso modo di qualsiasi altra istanza di database SQL. È possibile disporre di un'unica istanza di database gratuita. Se si dispone già di un database nella stessa area del nuovo servizio mobile, è possibile scegliere il database esistente. In questo caso, assicurarsi di specificare le credenziali di accesso corrette. Se si specificano credenziali di accesso non corrette, il servizio mobile viene creato in uno stato non integro.

    Dopo la creazione del servizio mobile, nel progetto viene aggiunto un riferimento alla libreria client di Servizi mobili e il codice sorgente del progetto viene aggiornato.

  [aggiunta di un servizio connesso]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [creazione di un nuovo servizio mobile da VS 2013]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [download del file di sottoscrizione in VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [importazione della sottoscrizione in VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
  [creazione di un servizio da VS 2013 parte 2]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png
