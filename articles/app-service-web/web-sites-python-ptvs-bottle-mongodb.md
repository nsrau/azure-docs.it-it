<properties 
	pageTitle="Bottle e MongoDB in Azure con Python Tools 2.1 per Visual Studio" 
	description="Informazioni su come usare Python Tools per Visual Studio per creare un'applicazione Bottle che archivia i dati in un'istanza di database MongoDB e che può essere distribuita in app Web del servizio app di Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="huguesv"/>


# Bottle e MongoDB in Azure con Python Tools 2.1 per Visual Studio

> **Nota:** il flusso di lavoro di MongoLab non è attualmente supportato nel portale di anteprima

In questa esercitazione si userà [Python Tools per Visual Studio] al fine di creare una semplice app Web per sondaggi con uno dei modelli di esempio PTVS. Questa esercitazione è anche disponibile in formato [video](https://www.youtube.com/watch?v=8hQMyf8p_Jo).

L'app Web per sondaggi definisce un'astrazione per il proprio repository; in questo modo, è possibile passare facilmente tra diversi tipi di repository (in memoria, archiviazione tabelle di Azure, MongoDB).

Durante l'esercitazione si apprenderà a usare uno dei servizi MongoDB ospitati in Azure, come configurare l'applicazione per l'uso di MongoDB e come pubblicare l'app Web nelle [app Web del servizio appdi Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Vedere il [Centro per sviluppatori Python] per consultare altri articoli che trattano lo sviluppo di app Web del servizio app di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di MongoDB, archiviazione tabelle di Azure, MySQL e Database SQL. Sebbene questo articolo sia incentrato sul servizio app, i passaggi sono simili a quelli previsti per lo sviluppo dei [servizi cloud di Azure].

## Prerequisiti

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 per Visual Studio]
 - [VSIX degli esempi di Python Tools 2.1 per Visual Studio]
 - [Strumenti di Azure SDK per VS 2013] o [Strumenti di Azure SDK per VS 2012]
 - [Python 2.7 a 32 bit] o [Python 3.4 a 32 bit]
 - [RoboMongo](optional)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare il progetto

In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio. Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari. L'applicazione verrà quindi eseguita in locale tramite il repository in memoria predefinito.

1.  In Visual Studio selezionare **File**, **Nuovo progetto**. 

1.  I modelli di progetto del pacchetto PTVS Samples VSIX sono disponibili in **Python**, **Esempi**. Selezionare **Progetto Web di sondaggi Bottle** e fare clic su OK per creare il progetto.

  	![Finestra di dialogo Nuovo progetto](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Verrà richiesto di installare pacchetti esterni. Selezionare **Installa in un ambiente virtuale**.

  	![Finestra di dialogo dei pacchetti esterni](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  Selezionare **Python 2.7** o **Python 3.4** come interprete di base.

  	![Finestra di dialogo Aggiungi ambiente virtuale](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Verificare che l'applicazione funzioni, premendo <kbd>F5</kbd>. Per impostazione predefinita, l'applicazione usa un repository in memoria che non richiede alcuna configurazione. Tutti i dati vengono persi quando il server Web viene arrestato.

1.  Fare clic su **Crea sondaggio di esempio**, quindi fare clic su un sondaggio e su un voto.

  	![Web browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

## Creare un database MongoDB

Per il database verrà creato un database ospitato MongoLab in Azure.

In alternativa, è possibile creare una propria macchina virtuale in esecuzione in Azure, quindi installare e amministrare MongoDB manualmente.

Per creare una versione di valutazione gratuita con MongoLab, seguire questa procedura.

1.  Accedere al [portale di gestione di Azure].

1.  Nella parte inferiore del pannello di navigazione fare clic su **NEW**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png) -->

1.  Fare clic su **MARKETPLACE**, quindi su **MongoLab**, infine fare clic su **Avanti**.

  	<!-- ![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png) -->

1.  In **Nome** digitare un nome da usare per il servizio di database.

1.  Scegliere un'**area** in cui posizionare il servizio di database. Se si userà il database dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

  	<!-- ![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png) -->

1.  Fare clic su **Avanti**, quindi **ACQUISTA**.

## Configurare il progetto

In questa sezione verrà configurata l'applicazione per usare il database MongoDB appena creato. Verranno fornite informazioni su come ottenere le impostazioni di connessione dal portale di Azure, quindi verrà eseguita l'applicazione in locale.

1.  Nel [portale di gestione di Azure] fare clic su **MARKETPLACE**, quindi fare clic sul servizio MongoLab creato in precedenza.

1.  Fare clic su **INFORMAZIONI DI CONNESSIONE**. È possibile usare il pulsante Copia per inserire il valore di **MONGOLAB_URI** negli Appunti.

  	<!--![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png) -->

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Proprietà**. Fare clic sulla scheda **Debug**.

  	![Impostazioni di debug del progetto](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  Impostare i valori delle variabili di ambiente richieste dall'applicazione in **Comando debug server**, **Ambiente**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    In questo modo verranno impostate le variabili di ambiente quando si sceglie **Avvia debug**. Se si vuole che le variabili vengano impostate quando si sceglie l'opzione **Avvia senza eseguire debug**, impostare gli stessi valori anche in **Esegui comando server**.

    In alternativa, è possibile definire variabili di ambiente usando il Pannello di controllo di Windows. Si tratta di un'opzione migliore se si vuole evitare di archiviare le credenziali nel codice sorgente/nel file del progetto. Si noti che è necessario riavviare Visual Studio affinché i nuovi valori di ambiente siano disponibili per l'applicazione.

1.  Il codice che implementa il repository MongoDB si trova in **models/mongodb.py**.

1.  Fare clic su `F5` per eseguire l'applicazione. I sondaggi creati con **Crea sondaggi di esempio** e i dati inviati mediante voto verranno serializzati in MongoDB.

1.  Passare alla pagina **Informazioni** per verificare che l'applicazione usi il repository **MongoDB**.

  	![Web browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

## Esplorare il database MongoDB

È possibile usare un'applicazione come [RoboMongo] per eseguire query e apportare modifiche a un database MongoDB. In questa sezione si userà RoboMongo per visualizzare il contenuto del database dell'applicazione di sondaggio.

1.  Creare una nuova connessione. È necessario il valore **MONGOLAB_URI** recuperato nella sezione precedente.

    Si noti il formato dell'URI: `mongodb://<name>:<password>@<address>:<port>/<name>`

    Il nome corrisponde al nome immesso al momento della creazione del servizio con Azure. Viene usato sia per il nome del database che per il nome utente.

1.  Nella pagina relativa alla connessione impostare **Nome** su qualsiasi nome desiderato per la connessione. Impostare inoltre i campi **Indirizzo** e **Porta** su *indirizzo* e *porta* in **MONGOLAB_URI**.

  	![Finestra di dialogo delle impostazioni di connessione](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Nella pagina relativa all'autenticazione impostare **Database** e **Nome utente** sul *nome* di **MONGOLAB_URI**. Inoltre, impostare il campo **Password** sul valore *password* di **MONGOLAB_URI**.

  	![Finestra di dialogo delle impostazioni di connessione](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Salvare e connettersi al database. È ora possibile eseguire query sulla raccolta di sondaggi.

  	![Risultati della query di RoboMongo](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

## Pubblicare l'app Web in Azure

L'SDK .NET di Azure offre un modo semplice di distribuire l'app Web in Azure.

1.  In **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Pubblica**.

1.  Fare clic su **App Web di Microsoft Azure**.

3. Se non è stato effettuato l'accesso ad Azure, fare clic su **Accedi** e usare l'account Microsoft relativo alla sottoscrizione di Azure.

2.  Fare clic su **Nuovo** per creare una nuova app Web.

1.  Compilare i campi seguenti, quindi fare clic su **Crea**.
	-	**Nome dell'app Web**
	-	**Piano di servizio app**
	-	**Gruppo di risorse**
	-	**Area**
	-	Lasciare **Server database** impostato su **Nessun database**

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png) -->

1.  Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.

1.  L'app Web pubblicata verrà aperto automaticamente nel Web browser. Se si passa alla pagina relativa alle informazioni, si noterà che viene usato il repository **In memoria**, non il repository **MongoDB**.

    Ciò avviene perché le variabili di ambiente non sono impostate per l'istanza app Web in Azure App Service; pertanto vengono usati i valori predefiniti specificati in **settings.py**.

## Configurare l'istanza di app Web

In questa sezione verranno configurate le variabili dell'istanza di app Web.

1.  Nel [portale di gestione di Azure] fare clic sull'app Web creata nella sezione precedente.

1.  Nel menu in alto fare clic su **CONFIGURA**.

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png) -->

1.  Scorrere verso il basso fino alla sessione **impostazioni app**, quindi impostare i valori per **REPOSITORY_NAME**, **MONGODB_HOST** e **MONGODB_DATABASE**, seguendo le istruzioni fornite nella sezione precedente.

  	<!-- ![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png) -->

1.  Nel menu inferiore, fare clic su **SALVA**, quindi su **RIAVVIA** e infine su **SFOGLIA**.

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  L'applicazione dovrebbe funzionare come previsto, usando il repository **MongoDB**.

    Congratulazioni.

  	![Web browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

## Passaggi successivi

Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Bottle e MongoDB.

- [Documentazione di Python Tools per Visual Studio]
  - [Progetti Web]
  - [Progetti servizio cloud]
  - [Debug remoto in Microsoft Azure]
- [Documentazione di Bottle]
- [MongoDB]
- [Documentazione di PyMongo]
- [PyMongo]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Link references-->
[Centro per sviluppatori Python]: /develop/python/
[servizi cloud di Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[portale di gestione di Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX degli esempi di Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Strumenti di Azure SDK per VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Strumenti di Azure SDK per VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
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
 

<!---HONumber=62-->