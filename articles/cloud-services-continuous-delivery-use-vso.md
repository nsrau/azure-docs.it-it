<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with TFS" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure web sites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online" authors="ghogen" solutions="" manager="" editor="" />

Recapito continuo in Azure utilizzando Visual Studio Online
===========================================================

Visual Studio Online (precedentemente Team Foundation Service) è la versione del servizio ospitato su cloud del popolare software Team Foundation Server (TFS) di Microsoft, che offre funzioni altamente personalizzabili di gestione del codice sorgente e compilazione, un agile flusso di lavoro per il processo di sviluppo e dei team, il rilevamento dei problemi e degli elementi di lavoro e molto altro. È possibile configurare i progetti del proprio team di Visual Studio Online in modo da compilare ed eseguire automaticamente la distribuzione sui siti Web o sui servizi cloud di Azure. Per informazioni su come configurare un sistema di compilazione e distribuzione continua mediante un Team Foundation Server locale, vedere [Recapito continuo per Servizi cloud in Azure](../cloud-services-dotnet-continuous-delivery).

In questa esercitazione si presuppone che l'utente abbia installato Visual Studio 2013 e Azure SDK. Se non si dispone ancora di Visual Studio 2013, scaricarlo scegliendo il collegamento **Inizia gratuitamente** all'indirizzo [www.visualstudio.com](http://www.visualstudio.com). Installare Azure SDK da [questa pagina](http://go.microsoft.com/fwlink/?LinkId=239540).

Per configurare un servizio cloud in modo da compilare e distribuire automaticamente in Azure mediante Visual Studio Online, eseguire la procedura seguente:

-   [Passaggio 1: Iscrizione a Visual Studio Online.](#step1)

-   [Passaggio 2: Archiviazione di un progetto nel controllo del codice sorgente.](#step2)

-   [Passaggio 3: Connessione del progetto ad Azure.](#step3)

-   [Passaggio 4: Modifiche e attivazione di una ricompilazione e una ridistribuzione.](#step4)

-   [Passaggio 5: Ridistribuzione di una compilazione precedente (facoltativa)](#step5)

-   [Passaggio 6: Modifica della distribuzione di produzione (solo servizi cloud)](#step6)

Iscrizione a Visual Studio Online:Passaggio 1: Iscrizione a Visual Studio Online
--------------------------------------------------------------------------------

1.  Creare un account di Visual Studio Online visitando il sito <http://www.visualstudio.com>. Fare clic sul collegamento **Accedi**. Sarà necessario effettuare l'accesso con un account Microsoft. Se è la prima volta che si effettua l'accesso verrà richiesto di fornire alcune informazioni sull'utente, ad esempio il nome e l'indirizzo di posta elettronica. ![](./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG)

2.  Se non è la prima volta che si esegue l'accesso verrà visualizzata la schermata seguente. Fare clic sul collegamento **Crea un account gratuito**.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG)

3.  Creare un URL account per il nuovo progetto. L'account avrà il formato: https://&lt;nomeaccount\>.visualstudio.com.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG)

4.  È ora possibile creare il primo progetto. Immettere il nome e la descrizione del progetto. Scegliere il sistema di controllo delle versioni che si desidera utilizzare. Sono supportati Team Foundation Version Control (TFVC) o Git. Ulteriori informazioni su queste opzioni sono disponibili nell'articolo [Utilizzare il controllo della versione](http://go.microsoft.com/fwlink/?LinkId=324037). In questa procedura dettagliata si presume l'utilizzo di TFVC. Scegliere quindi il modello di processo in uso presso la propria organizzazione e fare clic sul pulsante **Create Project**. Per ulteriori informazioni sui modelli di processo vedere l'articolo [Utilizzare elementi del progetto team, scegliere un modello di processo](http://go.microsoft.com/fwlink/?LinkId=324035).

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs1.png)

5.  Al termine della creazione del progetto fare clic sul pulsante **Open with Visual Studio to connect** per avviare automaticamente Visual Studio connesso al progetto team. Se vengono visualizzati avvisi di sicurezza scegliere Consenti.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs2.png)

Archiviazione di un progetto nel controllo del codice sorgente.Passaggio 2: Archiviazione di un progetto nel controllo del codice sorgente
------------------------------------------------------------------------------------------------------------------------------------------

1.  In Visual Studio, aprire la soluzione che si desidera distribuire o crearne una nuova. È possibile distribuire un sito Web o un servizio cloud (applicazione Azure) seguendo i passaggi di questa procedura dettagliata. Se si desidera creare una nuova soluzione, creare un nuovo progetto di servizio cloud di Azure o un nuovo progetto ASP.NET MVC. Assicurarsi che la destinazione del progetto sia .NET Framework 4 o 4.5 e, se si sta creando un progetto di servizio cloud, aggiungere un ruolo Web ASP.NET MVC e un ruolo di lavoro, quindi scegliere l'applicazione Internet per il ruolo Web. Quando richiesto, scegliere **Applicazione Internet**. Per creare un sito Web, scegliere il modello di progetto Applicazione Web ASP.NET e quindi scegliere MVC. Vedere [Introduzione ad Azure e ASP.NET](http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-get-started/).

2.  Aprire il menu di scelta rapida relativo alla soluzione e selezionare **Aggiungi soluzione al controllo del codice sorgente**.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs5.png)

3.  Accettare o modificare le impostazioni predefinite e quindi scegliere **OK**. Al completamento del processo, in Esplora soluzioni vengono visualizzate le icone del controllo codice sorgente.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs6.png)

4.  Aprire il menu di scelta rapida relativo alla soluzione e scegliere **Archivia**.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs7.png)

5.  Nell'area Modifiche in sospeso di Team Explorer, digitare un commento per l'archiviazione e scegliere il pulsante **Archivia**.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs8.png)

 Si notino le opzioni per includere o escludere modifiche specifiche quando si esegue l'archiviazione; se le modifiche desiderate sono escluse, scegliere il collegamento **Includi tutto**.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs9.png)

Connessione del progetto ad Azure.Passaggio3: Connessione del progetto ad Azure
-------------------------------------------------------------------------------

1.  A questo punto, dopo aver creato un progetto team VSO contenente il codice sorgente, è possibile connettere il progetto team ad Azure. Nel [portale di Azure](http://manage.windowsazure.com), selezionare il servizio cloud o il sito Web oppure crearne uno nuovo selezionando l'icona + in basso a sinistra e scegliendo **Cloud Service** o **Web Site**, quindi selezionare **Quick Create**. Scegliere il collegamento **Set up publishing with Visual Studio Online**.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs10.png)

2.  Nella procedura guidata, digitare il nome del proprio account di Visual Studio Online nella casella di testo e fare clic sul collegamento **Authorize Now**. È possibile che venga richiesto di effettuare l'accesso.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs11.png)

3.  Nella finestra di dialogo popup OAuth scegliere **Accept** per autorizzare Azure a configurare il progetto team in VSO.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs12.png)

4.  Dopo aver concesso l'autorizzazione verrà visualizzato un elenco a discesa contenente un elenco dei progetti team di Visual Studio Online. Selezionare il nome del progetto team creato nei passaggi precedenti e fare clic sul pulsante di segno di spunta della procedura guidata.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs13.png)

5.  Quando il progetto sarà stato collegato, saranno visualizzate alcune istruzioni per l'archiviazione delle modifiche nel progetto team di Visual Studio Online. Alla successiva archiviazione, Visual Studio Online compilerà e distribuirà il progetto in Azure. Effettuare subito una prova facendo clic sul collegamento **Check In from Visual Studio** quindi sul collegamento **Launch Visual Studio** (o sul pulsante equivalente di **Visual Studio** in basso nella schermata del portale).

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs14.png)

Attivazione di una ricompilazionePassaggio 4: Attivazione di una ricompilazione e ridistribuzione del progetto
--------------------------------------------------------------------------------------------------------------

1.  In Team Explorer di Visual Studio fare clic sul collegamento **Esplora controllo codice sorgente**.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs15.png)

2.  Passare al file della soluzione e aprirlo.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs16.png)

3.  In Esplora soluzioni aprire un e apportarvi delle modifiche. Ad esempio, modificare il file \_Layout.cshtml nella cartella Views\\Shared di un ruolo Web MVC.
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs17.png)

4.  Modificare il logo del sito e premere Ctrl+S per salvare il file.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs18.png)

5.  In Team Explorer scegliere il collegamento **Modifiche in sospeso**.
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs19.png)

6.  Digitare un commento e scegliere il pulsante **Archivia**.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs20.png)

7.  Scegliere il pulsante Home per tornare alla pagina iniziale di Team Explorer.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs21.png)

8.  Scegliere il collegamento **Compilazioni** per visualizzare le compilazioni in corso.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs22.png)

     In Team Explorer è possibile osservare che è stata attivata una compilazione per la propria archiviazione.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs23.png)

9.  Fare doppio clic sul nome della compilazione in corso per visualizzare un log dettagliato con l'avanzare della compilazione.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs24.png)

10. Quando la compilazione è in corso, osservare la definizione di compilazione creata quando TFS è stato collegato ad Azure mediante la procedura guidata. Aprire il menu di scelta rapida relativo alla definizione di compilazione e scegliere **Modifica definizione di compilazione**.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs25.png)

     Nella scheda **Trigger** è possibile osservare che, per impostazione predefinita, la definizione di compilazione è configurata per la compilazione a ogni archiviazione.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs26.png)

     Nella scheda **Processo** è possibile osservare che l'ambiente di distribuzione è configurato con il nome del proprio servizio cloud o sito Web. Se si lavora con i siti Web, le proprietà visualizzate saranno diverse da quelle presenti nella sezione riportata di seguito.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs27.png)

     Specificare i valori per le proprietà se si desidera che siano diversi da quelli predefiniti. Nella tabella seguente sono illustrati i valori predefiniti delle proprietà per un servizio cloud:

	<table data-morhtml="true">
	<tr data-morhtml="true"><td data-morhtml="true"><b data-morhtml="true">Propriet&agrave;</b></td><td data-morhtml="true"><b data-morhtml="true">Valore predefinito</b></td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Allow Upgrade</td><td data-morhtml="true">true</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Cloud Service Environment</td><td data-morhtml="true">Staging</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Cloud Service Name</td><td data-morhtml="true">Nome del servizio a cui si &egrave; connessi</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Deployment Label</td><td data-morhtml="true">Analoga al nome del servizio</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Service Configuration</td><td data-morhtml="true">ServiceConfiguration.Cloud.cscfg</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Storage Account Name</td><td data-morhtml="true">Vuoto, comporta un tentativo di individuare un account di archiviazione.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Publish Profile</td><td data-morhtml="true">File .azurePubxml. Se si esegue l'archiviazione di uno di questi file &egrave; possibile sceglierlo in questa fase.</td></tr>
	</table>  


 Se la proprietà dell'account di archiviazione viene lasciata vuota, Azure eseguirà la ricerca di una proprietà. Se esiste un account di archiviazione con lo stesso nome del servizio cloud, verrà utilizzato questo account. Altrimenti, verrà utilizzato un altro account di archiviazione oppure, in assenza di account di archiviazione, ne verrà creato uno nuovo. L'account di archiviazione rappresenta un archivio per file e altri dati in Azure. Per ulteriori informazioni, vedere la pagina [Informazioni sull'account di archiviazione](http://www.windowsazure.com/en-us/documentation/articles/storage-whatis-account).

1.  A questo punto la compilazione sarà stata completata correttamente.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs28.png)

2.  Facendo doppio clic sul nome della compilazione, in Visual Studio verrà visualizzato un **Riepilogo compilazione** che include eventuali risultati del test restituiti dai progetti unit test associati.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs29.png)

3.  Nel [portale di Azure](http://manage.windowsazure.com) è possibile visualizzare la distribuzione associata nella scheda Deployments quando si seleziona l'ambiente di gestione temporanea.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs30.png)

4.  Passare all'URL del proprio sito. Per un sito Web basta fare clic sul pulsante Browse della barra dei comandi. Per un servizio cloud, scegliere l'URL nella sezione **Quick Glance** della pagina **Dashboard** in cui è visualizzato l'ambiente di gestione temporanea per un servizio cloud. Le distribuzioni derivanti dall'integrazione continua per i servizi cloud vengono pubblicate nell'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostazione configurando la proprietà Ambiente del servizio cloud alternativo su Produzione. In questa schermata è possibile osservare dove si trova l'URL del sito nella pagina dashboard del servizio cloud:

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs31.png)

     Il sito in esecuzione verrà aperto in una nuova scheda del browser.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs32.png)

5.  Per i servizi cloud, se si apportano altre modifiche al progetto, si attiveranno ulteriori compilazioni e si accumuleranno più distribuzioni. L'ultima è contrassegnata come Active.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs33.png)

Ridistribuzione di una compilazione precedentePassaggio 5: Ridistribuzione di una compilazione precedente
---------------------------------------------------------------------------------------------------------

Questo passaggio si applica ai servizi cloud ed è facoltativo. Nel portale di gestione selezionare una distribuzione precedente e fare clic sul pulsante **Redeploy** per riportare il sito a un'archiviazione precedente. SI noti che verrà attivata una nuova compilazione in TFS e verrà creata una nuova voce nella cronologia di distribuzione.

  ![](./media/cloud-services-continuous-delivery-use-vso/tfs34.png)

Modifica della distribuzione di produzionePassaggio 6: Modifica della distribuzione di produzione
-------------------------------------------------------------------------------------------------

Questo passaggio si applica solo ai servizi cloud, non ai siti Web. Quando si è pronti, è possibile promuovere l'ambiente di gestione temporanea all'ambiente di produzione scegliendo il pulsante Swap nel portale di gestione. L'ambiente di gestione temporanea appena distribuito verrà promosso alla produzione e il precedente ambiente di produzione (se presente) diventerà un ambiente di gestione temporanea. La distribuzione attiva potrebbe differire per gli ambienti di produzione e di gestione temporanea, ma la cronologia di distribuzione delle recenti compilazioni è la stessa indipendentemente dall'ambiente.

  ![](./media/cloud-services-continuous-delivery-use-vso/tfs35.png)

Per ulteriori informazioni, vedere [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Se si utilizza Git, vedere [Condivisione del codice in Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [Pubblicazione da controllo del codice sorgente in Siti Web di Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-publish-source-control).

