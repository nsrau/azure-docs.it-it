<properties 
	pageTitle="Bottle e archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio" 
	description="Informazioni su come usare Python Tools per Visual Studio per creare un'applicazione Bottle che archivia i dati nel servizio di archiviazione tabelle di Azure e distribuisce l'app Web in App Web del servizio app di Azure." 
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
	ms.date="04/16/2015" 
	ms.author="huguesv"/>




# Bottle e archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio 

In questa esercitazione si userà [Python Tools per Visual Studio] al fine di creare una semplice app Web per sondaggi con uno dei modelli di esempio PTVS. Questa esercitazione è anche disponibile in formato [video](https://www.youtube.com/watch?v=GJXDGaEPy94).

L'app Web per sondaggi definisce un'astrazione per il proprio repository; in questo modo, è possibile passare facilmente tra diversi tipi di repository (in memoria, archiviazione tabelle di Azure, MongoDB).

Si apprenderà come creare un account di archiviazione di Azure, come configurare l'app Web per l'uso dell'archiviazione tabelle di Azure e come pubblicare l'app Web in [App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Vedere il [Centro per sviluppatori Python] per consultare altri articoli che trattano lo sviluppo di app Web del servizio app di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di MongoDB, archiviazione tabelle di Azure, MySQL e Database SQL. Sebbene questo articolo sia incentrato sul servizio app, i passaggi sono simili a quelli previsti per lo sviluppo dei [servizi cloud di Azure].

## Prerequisiti

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 per Visual Studio]
 - [VSIX degli esempi di Python Tools 2.1 per Visual Studio]
 - [Strumenti di Azure SDK per VS 2013] o [Strumenti di Azure SDK per VS 2012]
 - [Python 2.7 a 32 bit] o [Python 3.4 a 32 bit]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare il progetto

In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio. Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari. L'applicazione verrà quindi eseguita in locale tramite il repository in memoria predefinito.

1.  In Visual Studio selezionare **File**, **Nuovo progetto**.

1.  I modelli di progetto del pacchetto PTVS Samples VSIX sono disponibili in **Python**, **Esempi**. Selezionare **Progetto Web di sondaggi Bottle** e fare clic su OK per creare il progetto.

  	![Finestra di dialogo Nuovo progetto](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  Verrà richiesto di installare pacchetti esterni. Selezionare **Installa in un ambiente virtuale**.

  	![Finestra di dialogo dei pacchetti esterni](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  Selezionare **Python 2.7** o **Python 3.4** come interprete di base.

  	![Finestra di dialogo Aggiungi ambiente virtuale](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  Verificare che l'applicazione funzioni premendo `F5`. Per impostazione predefinita, l'applicazione usa un repository in memoria che non richiede alcuna configurazione. Tutti i dati vengono persi quando il server Web viene arrestato.

1.  Fare clic su **Crea sondaggio di esempio**, quindi fare clic su un sondaggio e su un voto.

  	![Web browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

## Creare un account di Archiviazione di Azure

Per effettuare operazioni di archiviazione, è necessario un account di archiviazione di Azure. Per creare un account di archiviazione, attenersi alla procedura riportata di seguito

1.  Accedere al [Portale di Azure].

2. Fare clic sull'icona **Nuovo** nella parte inferiore sinistra del portale, quindi fare clic su **Dati e archiviazione** > **Archiviazione**. Assegnare un nome univoco all'account di archiviazione e creare un nuovo [gruppo di risorse](../resource-group-overview.md) ad esso correlato.

  	<!-- ![New Button](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzurePlusNew.png) -->Quando l'account di archiviazione viene creato, nel pulsante **Notifiche** lampeggia in verde il testo **OPERAZIONE RIUSCITA** e il pannello dell'account di archiviazione si apre per visualizzare che appartiene al nuovo gruppo di risorse creato.

  	<!-- ![Quick Create](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureStorageCreate.png) -->

5. Fare clic sulla sezione **Impostazioni** del pannello dell'account di archiviazione. Prendere nota del nome dell'account e della chiave primaria.

	Queste informazioni sono necessarie per configurare il progetto nella sezione successiva.

## Configurare il progetto

In questa sezione verrà configurata l'applicazione per usare l'account di archiviazione appena creato. quindi verrà eseguita l'applicazione in locale.

1.  In Visual Studio fare clic con il pulsante destro del mouse sul nodo del progetto in Esplora soluzioni e scegliere **Proprietà**. Fare clic sulla scheda **Debug**.

  	![Impostazioni di debug del progetto](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  Impostare i valori delle variabili di ambiente richieste dall'applicazione in **Comando debug server**, **Ambiente**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    In questo modo verranno impostate le variabili di ambiente quando si sceglie **Avvia debug**. Se si vuole che le variabili vengano impostate quando si sceglie l'opzione **Avvia senza eseguire debug**, impostare gli stessi valori anche in **Esegui comando server**.

    In alternativa, è possibile definire variabili di ambiente usando il Pannello di controllo di Windows. Si tratta di un'opzione migliore se si vuole evitare di archiviare le credenziali nel codice sorgente/nel file del progetto. Si noti che è necessario riavviare Visual Studio affinché i nuovi valori di ambiente siano disponibili per l'applicazione.

1.  Il codice che implementa il repository di archiviazione tabelle di Azure si trova in **models/azuretablestorage.py**. Per altre informazioni su come usare il servizio tabelle da Python, vedere la [documentazione].

1.  Eseguire l'applicazione con `F5`. I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati nell'archiviazione tabelle di Azure.

1.  Passare alla pagina **About** per verificare che l'applicazione usi il repository di **archiviazione tabelle di Azure**.

  	![Web browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

## Esplorare l'archiviazione tabelle di Azure

È facile visualizzare e modificare le tabelle di archiviazione tramite Esplora server in Visual Studio. In questa sezione si userà Esplora server per visualizzare il contenuto delle tabelle dell'applicazione di sondaggio.

> [AZURE.NOTE]A tale scopo, è necessario che siano installati gli strumenti di Microsoft Azure, disponibili come parte di [Azure SDK per .NET].

1.  Aprire **Esplora server**. Espandere **Azure**, **Archiviazione**, l'account di archiviazione e quindi **Tabelle**.

  	<!-- ![Server Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png) -->

1.  Fare doppio clic sulla tabella **polls** o **choices** per visualizzare i contenuti della tabella in una finestra di documento e per aggiungere, rimuovere o modificare entità.

  	<!-- ![Table Query Results](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png) -->

## Pubblicare l'app Web nel servizio app di Azure

L'SDK .NET di Azure offre un modo semplice di distribuire l'app Web nel servizio app di Azure.

1.  In **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Pubblica**.

  	<!-- ![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png) -->

1.  Fare clic su **App Web di Microsoft Azure**.

1.  Fare clic su **Nuovo** per creare una nuova app Web.

1.  Compilare i campi seguenti, quindi fare clic su **Crea**.
	-	**Nome dell'app Web**
	-	**Piano di servizio app**
	-	**Gruppo di risorse**
	-	**Area**
	-	Lasciare **Server database** impostato su **Nessun database**

  	<!-- ![Create Web App on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonCreateWebSite.png) -->

1.  Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.

1.  L'app Web pubblicata verrà aperto automaticamente nel Web browser. Se si passa alla pagina relativa alle informazioni, si noterà che viene usato il repository **in memoria** anziché il repository di **archiviazione tabelle di Azure**.

    Ciò avviene perché le variabili di ambiente non sono impostate per l'istanza app Web in Azure App Service; pertanto vengono usati i valori predefiniti specificati in **settings.py**.

## Configurare l'istanza di app Web

In questa sezione verranno configurate le variabili dell'istanza di App Web.

1.  Nel [portale di Azure] aprire il pannello dell'app Web facendo clic su **Sfoglia** > **App Web** > nome dell'app Web.

1.  Nel pannello dell'app Web fare clic su **Tutte le impostazioni** e quindi su **Impostazioni applicazione**.

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteTopMenu.png) -->

1.  Scorrere verso il basso fino alla sezione **Impostazioni app** e impostare i valori per **REPOSITORY_NAME**, **STORAGE_NAME** e **STORAGE_KEY**, come descritto nella sezione **Configurare il progetto**.

  	<!-- ![App Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png) -->

1. Fare clic su **SALVA**, **RIAVVIA** e quindi su **SFOGLIA**.

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  L'app Web dovrebbe funzionare come previsto, usando il repository di **archiviazione tabelle di Azure**.

    Congratulazioni.

  	![Web browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

## Passaggi successivi

Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Bottle e archiviazione tabelle di Azure.

- [Documentazione di Python Tools per Visual Studio]
  - [Progetti Web]
  - [Progetti servizio cloud]
  - [Debug remoto in Microsoft Azure]
- [Documentazione di Bottle]
- [Archiviazione di Azure]
- [Azure SDK per Python]
- [Come usare il servizio di archiviazione tabelle di Python]

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal portale precedente al nuovo portale, vedere [Informazioni di riferimento per l'esplorazione del portale](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--Link references-->
[Centro per sviluppatori Python]: /develop/python/
[servizi cloud di Azure]: ../cloud-services-python-ptvs.md
[documentazione]: ../storage-python-how-to-use-table-storage.md
[Come usare il servizio di archiviazione tabelle di Python]: ../storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Portale di Azure]: https://portal.azure.com
[Azure SDK per .NET]: http://azure.microsoft.com/downloads/
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX degli esempi di Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Strumenti di Azure SDK per VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Strumenti di Azure SDK per VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentazione di Python Tools per Visual Studio]: http://pytools.codeplex.com/documentation
[Documentazione di Bottle]: http://bottlepy.org/docs/dev/index.html
[Debug remoto in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Progetti Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Progetti servizio cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Archiviazione di Azure]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK per Python]: https://github.com/Azure/azure-sdk-for-python
 

<!---HONumber=July15_HO3-->