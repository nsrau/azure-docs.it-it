<properties linkid="web-sites-python-ptvs-bottle-mongodb" title="Bottle and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Bottle e MongoDB su Azure con Python Tools 2.1 per Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Bottle application that stores data in a MongoDB database instance and can be deployed to a web site." metaKeywords="" services="web-sites" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Bottle e MongoDB su Azure con Python Tools 2.1 per Visual Studio

In questa esercitazione verrà creata una semplice applicazione per sondaggi usando uno dei modelli di esempio PTVS.

L'applicazione per sondaggi definisce un'astrazione per il proprio repository che consente di passare facilmente tra diversi tipi di repository (in memoria, archiviazione tabelle di Azure, MongoDB).

Si apprenderà come usare uno dei servizi MongoDB ospitati in Azure, come configurare l'applicazione per l'uso di MongoDB e come pubblicare l'applicazione in un sito Web di Azure.

Vedere il [Centro per sviluppatori Python][] per altri articoli che trattano lo sviluppo di siti Web di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di MongoDB, archiviazione tabelle di Azure, MySQL e Database SQL.  Sebbene questo articolo sia incentrato sui siti Web di Azure, i passaggi sono simili a quelli dello sviluppo di [Servizi Cloud di Azure][].

+ [Prerequisiti](#prerequisites)
+ [Creare il progetto](#create-the-project)
+ [Creare un database MongoDB](#create-a-mongodb-database)
+ [Configurare il progetto](#configure-the-project)
+ [Esplorare il database MongoDB](#explore-the-mongodb-database)
+ [Pubblicare in un sito Web di Azure](#publish-to-an-azure-website)
+ [Configurare il sito Web di Azure](#configure-the-azure-website)
+ [Passaggi successivi](#next-steps)

##<a name="prerequisites"></a>Prerequisiti

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 per Visual Studio][]
 - [VSIX degli esempi di Python Tools 2.1 per Visual Studio][]
 - [Strumenti di Azure SDK per VS 2013][] o [Strumenti di Azure SDK per VS 2012][]
 - [Python 2.7 a 32 bit][] o [Python 3.4 a 32 bit][]
 - [RoboMongo][] (facoltativo)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Creare il progetto

In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio.  Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari,  quindi l'applicazione verrà eseguita localmente tramite il repository in memoria predefinito.

1.  In Visual Studio selezionare **File**, **Nuovo progetto**. 

1.  I modelli di progetto del VSIX degli esempi di PTVS sono disponibili in **Python**, **Esempi**.  Selezionare **Polls Bottle Web Project** e fare clic su OK per creare il progetto.

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Verrà richiesto di installare pacchetti esterni.  Selezionare **Install into a virtual environment**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  Selezionare **Python 2.7** o **Python 3.4** come interprete di base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Verificare che l'applicazione funzioni premendo <kbd>F5</kbd>.  Per impostazione predefinita, l'applicazione usa un repository in memoria che non richiede alcuna configurazione.  Tutti i dati vengono persi quando il server Web viene arrestato.

1.  Fare clic su **Create Sample Polls**, quindi fare clic su un sondaggio e su un voto.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>Creare un database MongoDB

Per il database verrà creato un database ospitato MongoLab in Azure.

In alternativa, è possibile creare una propria macchina virtuale in esecuzione in Azure, quindi installare e amministrare MongoDB manualmente.

Per creare una versione di valutazione gratuita con MongoLab, attenersi alla procedura seguente.

1.  Accedere al [portale di gestione di Azure][].

1.  Nella parte inferiore del pannello di navigazione fare clic su **NUOVO**.

  	![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  Fare clic su **NEGOZIO**, quindi su **MongoLab**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  In Nome digitare un nome da usare per il servizio di database.

1.  Scegliere un'area o un gruppo di affinità in cui posizionare il servizio di database. Se si userà il database dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  Fare clic su **ACQUISTA**.

##<a name="configure-the-project"></a>Configurare il progetto

In questa sezione verrà configurata l'applicazione per usare il database MongoDB appena creato.  Verranno fornite informazioni su come ottenere le impostazioni di connessione dal portale di Azure,  quindi verrà eseguita l'applicazione localmente.

1.  In [Portale di gestione di Azure][] fare clic su **COMPONENTI AGGIUNTIVI**, quindi fare clic sul servizio MongoLab creato in precedenza.

1.  Fare clic su **INFORMAZIONI DI CONNESSIONE**.  È possibile usare il pulsante Copia per inserire il valore di **MONGOLAB\_URI** negli Appunti.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  In Visual Studio fare clic con il pulsante destro del mouse sul nodo del progetto in Esplora soluzioni e selezionare **Proprietà**.  Fare clic sulla scheda **Debug**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  Impostare i valori delle variabili di ambiente richieste dall'applicazione in **Debug Server Command**, **Environment**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    In questo modo verranno impostate le variabili di ambiente quando si **avvia il debug**.  Se si vuole che le variabili vengano impostate quando si **avvia senza eseguire il debug**, impostare gli stessi valori anche in **Run Server Command**.

    In alternativa, è possibile definire variabili di ambiente usando il pannello di controllo di Windows.  Si tratta di un'opzione migliore se si vuole evitare di archiviare le credenziali nel codice sorgente/nel file del progetto.  Si noti che è necessario riavviare Visual Studio affinché i nuovi valori di ambiente siano disponibili per l'applicazione.

1.  Il codice che implementa il repository MongoDB si trova in **models/mongodb.py**.

1.  Eseguire l'applicazione con <kbd>F5</kbd>.  I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati in MongoDB.

1.  Passare alla pagina **About** per verificare che l'applicazione usi il repository **MongoDB**.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>Esplorare il database MongoDB

È possibile usare un'applicazione come [RoboMongo][] per eseguire query e apportare modifiche a un database MongoDB.  In questa sezione si userà RoboMongo per visualizzare il contenuto del database dell'applicazione di sondaggio.

1.  Creare una nuova connessione.  È necessario il **MONGOLAB\_URI** recuperato nella sezione precedente.

    Si noti il formato dell'URI: "mongodb://<nome>:<password>@<indirizzo>:<porta>/<nome>"

    Il nome corrisponde al nome immesso al momento della creazione del servizio con Azure.  Viene usato sia per il nome del database che per il nome utente.

1.  Nella pagina relativa alla connessione impostare **Name** su qualsiasi nome desiderato per la connessione.  Impostare inoltre i campi **Address** e **Port** sull'*indirizzo* e sulla *porta* del **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Nella pagina relativa all'autenticazione impostare **Database** e **User name** sul *nome* del **MONGOLAB\_URI**.  Impostare inoltre **Password** sulla *password* del **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Salvare e connettersi al database.  È ora possibile eseguire query sulla raccolta di sondaggi.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Pubblicare in un sito Web di Azure

PTVS fornisce un modo semplice per distribuire l'applicazione Web in un sito Web di Azure.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo di progetto e scegliere **Pubblica**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Fare clic su **Siti Web di Microsoft Azure**.

1.  Fare clic su **Nuovo** per creare un nuovo sito.

1.  Selezionare un **Nome sito** e un'**Area geografica**, quindi fare clic su **Crea**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.

1.  Il sito pubblicato verrà aperto automaticamente nel browser Web.  Se si passa alla pagina relativa alle informazioni, si noterà che viene usato il repository **In memoria**, non il repository **MongoDB**.

    Ciò avviene perché le variabili di ambiente non sono impostate per il sito Web di Azure, pertanto usa i valori predefiniti specificati in **settings.py**.

##<a name="configure-the-azure-website"></a>Configurare il sito Web di Azure

In questa sezione verranno configurate le variabili di ambiente per il sito.

1.  Nel [portale di gestione di Azure][] fare clic sul sito creato nella sezione precedente.

1.  Nel menu in alto fare clic su **CONFIGURA**.

  	![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Scorrere verso il basso fino alla sezione **Impostazioni app** e impostare i valori per **REPOSITORY\_NAME**, **MONGODB\_HOST** e **MONGODB\_DATABASE** come descritto nella sezione precedente.

  	![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  Nel menu inferiore, fare clic su **SALVA**, quindi su **RIAVVIA** e infine su **SFOGLIA**.

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  L'applicazione dovrebbe funzionare come previsto, usando il repository **MongoDB**.

    Congratulazioni.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Bottle e MongoDB.

- [Documentazione di Python Tools per Visual Studio][]
  - [Progetti Web][]
  - [Progetti servizio cloud][]
  - [Debug remoto in Microsoft Azure][]
- [Documentazione di Bottle][]
- [MongoDB][]
- [Documentazione di PyMongo][]
- [PyMongo][]


<!--Link references-->
[Centro per sviluppatori di Python]: /it-it/develop/python/
[Servizi cloud di Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portale di gestione di Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX degli esempi di Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Strumenti SDK di Azure per Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Strumenti SDK di Azure per Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentazione di Python Tools per Visual Studio]: http://pytools.codeplex.com/documentation
[Documentazione di Bottle]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[Documentazione di PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Debug remoto in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Progetti Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Progetti servizio cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
