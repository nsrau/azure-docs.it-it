La procedura seguente consente di creare un nuovo servizio mobile in Azure e di aggiungere codice al progetto per connettere la propria app a questo nuovo servizio. Visual Studio 2013 si connette ad Azure per conto dell'utente al fine di creare il nuovo servizio mobile utilizzando le credenziali fornite dall'utente. Quando si crea un nuovo servizio mobile, è necessario specificare un database SQL di Azure utilizzato per archiviare i dati dell'app.

1.  In Visual Studio 2013 aprire Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto di app di Windows Store, quindi selezionare **Aggiungi** e **Servizio connesso...**.

2.  Nella finestra di dialogo Gestione servizi fare clic su **Crea servizio...**, quindi selezionare **\<Gestisci...\>** da **Sottoscrizione** nella finestra di dialogo Crea servizio mobile.

    ![creazione servizio gestione sottoscrizioni][creazione servizio gestione sottoscrizioni]

3.  In Gestione sottoscrizioni Microsoft Azure, fare clic su **Accedi...** per accedere al proprio account Azure (se richiesto), selezionare una sottoscrizione disponibile, quindi fare clic su **Chiudi**.

    Se la sottoscrizione include già uno o più servizi mobili esistenti, verranno visualizzati i relativi nomi.

4.  Nella finestra **Crea servizio mobile** selezionare la **Sottoscrizione**, il back-end di **.NET Framework** in **Runtime** e una **Regione** per il servizio mobile, quindi digitare un **Nome** per il servizio mobile.

    > [WACOM.NOTE]I nomi dei servizi mobili devono essere univoci. Se il nome specificato non è disponibile, accanto a **Nome** verrà visualizzata una X rossa.

5.  In **Database** selezionare **\<Crea un database SQL gratuito\>**, specificare i valori per **Nome utente server**, **Password server** e **Conferma password server**, quindi fare clic su **Crea**.

    ![creazione di un nuovo servizio mobile in VS 2013][creazione di un nuovo servizio mobile in VS 2013]

    > [WACOM.NOTE]
    > Nel corso di questa esercitazione verranno creati una nuova istanza e un nuovo server di database SQL. È possibile riutilizzare questo nuovo database e amministrarlo allo stesso modo di qualsiasi altra istanza di database SQL. È possibile disporre di un'unica istanza di database gratuita. Se si dispone già di un database nella stessa area del nuovo servizio mobile, è possibile scegliere il database esistente. In questo caso, assicurarsi di specificare le credenziali di accesso corrette. Se si specificano credenziali di accesso non corrette, il servizio mobile viene creato in uno stato non integro.

6.  Una volta creato il servizio mobile, selezionarlo dall'apposito elenco in Gestione servizi e fare clic su **OK**.

    Una volta completata la procedura guidata, il progetto del servizio mobile viene aggiunto alla propria soluzione, i pacchetti NuGet vengono installati, un riferimento alla libreria client per Servizi mobili viene aggiunta al progetto e il codice sorgente del progetto viene aggiornato.

  [creazione servizio gestione sottoscrizioni]: ./media/mobile-services-dotnet-backend-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [creazione di un nuovo servizio mobile in VS 2013]: ./media/mobile-services-dotnet-backend-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png
