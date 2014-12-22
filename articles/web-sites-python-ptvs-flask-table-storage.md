<properties linkid="web-sites-python-ptvs-flask-table-storage" title="Flask and Azure Table Storage on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Flask e archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Flask application that stores data in Azure Table Storage and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Flask e archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio 

In questa esercitazione verrà creata una semplice applicazione per sondaggi usando uno dei modelli di esempio PTVS.

L'applicazione per sondaggi definisce un'astrazione per il proprio repository che consente di passare facilmente tra diversi tipi di repository (in memoria, archiviazione tabelle di Azure, MongoDB).

Si apprenderà come creare un account di archiviazione di Azure, come configurare l'applicazione per l'uso dell'archiviazione tabelle di Azure e come pubblicare l'applicazione in un sito Web di Azure.

Vedere il [Centro per sviluppatori Python][] per altri articoli che trattano lo sviluppo di siti Web di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di MongoDB, archiviazione tabelle di Azure, MySQL e Database SQL.  Sebbene questo articolo sia incentrato sui siti Web di Azure, i passaggi sono simili a quelli dello sviluppo di [Servizi Cloud di Azure][].

+ [Prerequisiti](#prerequisites)
+ [Creare il progetto](#create-the-project)
+ [Creare un account di archiviazione di Azure](#create-an-azure-storage-account)
+ [Configurare il progetto](#configure-the-project)
+ [Esplorare l'archiviazione tabelle di Azure](#explore-the-azure-table-storage)
+ [Pubblicare in un sito Web di Azure](#publish-to-an-azure-website)
+ [Configurare il sito Web di Azure](#configure-the-azure-website)
+ [Passaggi successivi](#next-steps)

##<a name="prerequisites"></a>Prerequisiti

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 per Visual Studio][]
 - [VSIX degli esempi di Python Tools 2.1 per Visual Studio][]
 - [Strumenti di Azure SDK per VS 2013][] o [Strumenti di Azure SDK per VS 2012][]
 - [Python 2.7 a 32 bit][] o [Python 3.4 a 32 bit][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Creare il progetto

In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio.  Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari,  quindi l'applicazione verrà eseguita localmente tramite il repository in memoria predefinito.

1.  In Visual Studio selezionare **File**, **Nuovo progetto**.

1.  I modelli di progetto del VSIX degli esempi di PTVS sono disponibili in **Python**, **Esempi**.  Selezionare **Polls Flask Web Project** e fare clic su OK per creare il progetto.

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Verrà richiesto di installare pacchetti esterni.  Selezionare **Install into a virtual environment**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Selezionare **Python 2.7** o **Python 3.4** come interprete di base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Verificare che l'applicazione funzioni premendo <kbd>F5</kbd>.  Per impostazione predefinita, l'applicazione usa un repository in memoria che non richiede alcuna configurazione.  Tutti i dati vengono persi quando il server Web viene arrestato.

1.  Fare clic su **Create Sample Polls**, quindi fare clic su un sondaggio e su un voto.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

Per effettuare operazioni di archiviazione, è necessario un account di archiviazione di Azure. Per creare un account di archiviazione, attenersi alla procedura riportata di seguito

1.  Accedere al [portale di gestione di Azure][].

1.  Nella parte inferiore del pannello di navigazione fare clic su **NUOVO**.

  	![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  Fare clic su **SERVIZI DATI**, quindi su **ARCHIVIAZIONE** e infine su **CREAZIONE RAPIDA**.

  	![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.  Nel campo URL, digitare un nome di sottodominio da usare nell'URI per l'account di archiviazione.  La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo valore diventa il nome host all'interno dell'URI usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

1.  Scegliere una regione o un gruppo di affinità in cui situare l'archiviazione. Se si utilizzerà l'archiviazione dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

1.  Facoltativamente, è possibile abilitare la replica geografica.  Con la replica geografica, Archiviazione di Azure mantiene i dati durevoli in due posizioni. In entrambe le posizioni Archiviazione di Azure gestisce costantemente più repliche integre dei dati.

1.  Fare clic su **CREA ACCOUNT DI ARCHIVIAZIONE**.

##<a name="configure-the-project"></a>Configurare il progetto

In questa sezione verrà configurata l'applicazione per usare l'account di archiviazione appena creato.  Verranno fornite informazioni su come ottenere le impostazioni di connessione dal portale di Azure,  quindi verrà eseguita l'applicazione localmente.

1.  Nel [portale di gestione di Azure][] fare clic sull'account di archiviazione creato nella sezione precedente.

1.  Fare clic su **GESTISCI CHIAVI DI ACCESSO**.

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  In Visual Studio fare clic con il pulsante destro del mouse sul nodo del progetto in Esplora soluzioni e selezionare **Proprietà**.  Fare clic sulla scheda **Debug**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Impostare i valori delle variabili di ambiente richieste dall'applicazione in **Debug Server Command**, **Environment**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<nome account di archiviazione>
        STORAGE_KEY=<chiave di accesso primaria>

    In questo modo verranno impostate le variabili di ambiente quando si **avvia il debug**.  Se si vuole che le variabili vengano impostate quando si **avvia senza eseguire il debug**, impostare gli stessi valori anche in **Run Server Command**.

    In alternativa, è possibile definire variabili di ambiente usando il pannello di controllo di Windows.  Si tratta di un'opzione migliore se si vuole evitare di archiviare le credenziali nel codice sorgente/nel file del progetto.  Si noti che è necessario riavviare Visual Studio affinché i nuovi valori di ambiente siano disponibili per l'applicazione.

1.  Il codice che implementa il repository di archiviazione tabelle di Azure si trova in **models/azuretablestorage.py**.  Vedere la [documentazione] per altre informazioni su come usare il servizio tabelle da Python.

1.  Eseguire l'applicazione con <kbd>F5</kbd>.  I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati nell'archiviazione tabelle di Azure.

1.  Passare alla pagina **About** per verificare che l'applicazione usi il repository di **archiviazione tabelle di Azure**.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Esplorare l'archiviazione tabelle di Azure

È facile visualizzare e modificare le tabelle di archiviazione tramite Esplora server in Visual Studio.  In questa sezione si userà Esplora server per visualizzare il contenuto delle tabelle dell'applicazione di sondaggio.

> [WACOM.NOTE] Ciò richiede che siano installati gli strumenti di Microsoft Azure, disponibili come parte di [Azure SDK per .NET][].

1.  Aprire **Esplora server**.  Espandere **Azure**, **Archiviazione**, l'account di archiviazione, quindi **Tabelle**.

  	![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  Fare doppio clic su **sondaggi** o **opzioni** per visualizzarne i contenuti in una finestra di documento, nonché aggiungere/rimuovere/modificare entità.

  	![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Pubblicare in un sito Web di Azure

PTVS fornisce un modo semplice per distribuire l'applicazione Web in un sito Web di Azure.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo di progetto e scegliere **Pubblica**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Fare clic su **Siti Web di Microsoft Azure**.

1.  Fare clic su **Nuovo** per creare un nuovo sito.

1.  Selezionare un **Nome sito** e un'**Area geografica**, quindi fare clic su **Crea**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.

1.  Il sito pubblicato verrà aperto automaticamente nel browser Web.  Se si passa alla pagina relativa alle informazioni, si noterà che viene usato il repository **in memoria**, non il repository di **archiviazione tabelle di Azure**.

    Ciò avviene perché le variabili di ambiente non sono impostate per il sito Web di Azure, pertanto usa i valori predefiniti specificati in **settings.py**.

##<a name="configure-the-azure-website"></a>Configurare il sito Web di Azure

In questa sezione verranno configurate le variabili di ambiente per il sito.

1.  Nel [portale di gestione di Azure][] fare clic sul sito creato nella sezione precedente.

1.  Nel menu in alto fare clic su **CONFIGURA**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Scorrere verso il basso fino alla sezione **Impostazioni app** e impostare i valori per **REPOSITORY\_NAME**, **MONGODB\_HOST** e **STORAGE\_KEY** come descritto nella sezione precedente.

  	![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. Nel menu inferiore, fare clic su **SALVA**, quindi su **RIAVVIA** e infine su **SFOGLIA**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  L'applicazione dovrebbe funzionare come previsto, usando il repository di **archiviazione tabelle di Azure**.

    Congratulazioni.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Flask e archiviazione tabelle di Azure.

- [Documentazione di Python Tools per Visual Studio][]
  - [Progetti Web][]
  - [Progetti servizio cloud][]
  - [Debug remoto in Microsoft Azure][]
- [Documentazione di Flask][]
- [Archiviazione di Azure][]
- [Azure SDK per Python][]
- [Come usare il servizio di archiviazione tabelle di Python][]


<!--Link references-->
[Centro per sviluppatori di Python]: /it-it/develop/python/
[Servizi cloud di Azure]: ../cloud-services-python-ptvs/
[documentazione]: ../storage-python-how-to-use-table-storage/
[Come usare il servizio di archiviazione tabelle di Python]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Portale di gestione di Azure]: https://manage.windowsazure.com
[Azure SDK per .NET]: http://azure.microsoft.com/it-it/downloads/
[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX degli esempi di Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Strumenti SDK di Azure per Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Strumenti SDK di Azure per Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentazione di Python Tools per Visual Studio]: http://pytools.codeplex.com/documentation
[Documentazione di Flask]: http://flask.pocoo.org/
[Debug remoto in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Progetti Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Progetti servizio cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Archiviazione di Azure]: http://azure.microsoft.com/it-it/documentation/services/storage/
[Azure SDK per Python]: https://github.com/Azure/azure-sdk-for-python
