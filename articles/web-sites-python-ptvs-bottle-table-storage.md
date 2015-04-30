<properties 
	pageTitle="Bottle e archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio" 
	description="Informazioni su come usare Python Tools per Visual Studio per creare un'applicazione Bottle che archivia i dati in Archiviazione tabelle di Azure e può essere distribuita in un sito Web." 
	services="app-service\web" 
	tags="python"
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
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Bottle e archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio 

In questa esercitazione si userà [Python Tools per Visual Studio][] per creare una semplice applicazione per sondaggi con uno dei modelli di esempio PTVS. Questa esercitazione è anche disponibile sotto forma di [video](https://www.youtube.com/watch?v=GJXDGaEPy94).

L'applicazione per sondaggi definisce un'astrazione per il proprio repository che consente di passare facilmente tra diversi tipi di repository (in memoria, archiviazione tabelle di Azure, MongoDB).

Si apprenderà come creare un account di archiviazione di Azure, come configurare l'applicazione per l'uso dell'archiviazione tabelle di Azure e come pubblicare l'applicazione in un sito Web di Azure.

Vedere il [Centro per sviluppatori Python][] per altri articoli che trattano lo sviluppo di siti Web di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di MongoDB, archiviazione tabelle di Azure, MySQL e Database SQL.  Sebbene questo articolo sia incentrato su Siti Web di Azure, i passaggi sono simili a quelli dello sviluppo di [Servizi cloud di Azure][].

## Prerequisiti

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 per Visual Studio][]
 - [VSIX di esempio di Python Tools 2.1 per Visual Studio][]
 - [Strumenti di Azure SDK per VS 2013][] o [Strumenti di Azure SDK per VS 2012][]
 - [Python 2.7 a 32 bit][] o [Python 3.4 a 32 bit][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Creare il progetto

In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio.  Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari.  L'applicazione verrà quindi eseguita in locale tramite il repository in memoria predefinito.

1.  In Visual Studio selezionare **File**, **Nuovo progetto**.

1.  I modelli di progetto del pacchetto VSIX degli esempi di PTVS sono disponibili in **Python**, **Esempi**.  Selezionare **Polls Bottle Web Project** e fare clic su OK per creare il progetto.

  	![Finestra di dialogo Nuovo progetto](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  Verrà richiesto di installare pacchetti esterni.  Selezionare **Installa in un ambiente virtuale**.

  	![Finestra di dialogo dei pacchetti esterni](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  Selezionare **Python 2.7** o **Python 3.4** come interprete di base.

  	![Finestra di dialogo Aggiungi ambiente virtuale](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  Verificare che l'applicazione funzioni premendo <kbd>F5</kbd>.  Per impostazione predefinita, l'applicazione usa un repository in memoria che non richiede alcuna configurazione.  Tutti i dati vengono persi quando il server Web viene arrestato.

1.  Fare clic su **Create Sample Polls**, quindi fare clic su un sondaggio e su un voto.

  	![Web browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

## Creare un account di archiviazione di Azure

Per effettuare operazioni di archiviazione, è necessario un account di archiviazione di Azure. Per creare un account di archiviazione, attenersi alla procedura riportata di seguito

1.  Accedere al [portale di gestione di Azure][].

1.  Nella parte inferiore del pannello di navigazione fare clic su **NUOVO**.

  	![Pulsante Nuovo](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzurePlusNew.png)

1.  Fare clic su **SERVIZI DATI**, quindi su **ARCHIVIAZIONE** e infine su **CREAZIONE RAPIDA**.

  	![Creazione rapida](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureStorageCreate.png)

1.  Nel campo URL digitare un nome di sottodominio da usare nell'URI per l'account di archiviazione.  La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo valore diventa il nome host all'interno dell'URI usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

1.  Scegliere una regione o un gruppo di affinità in cui situare l'archiviazione. Se si utilizzerà l'archiviazione dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

1.  Facoltativamente, è possibile abilitare la replica geografica.  Con la replica geografica, Archiviazione di Azure mantiene i dati persistenti in due posizioni. In entrambe le posizioni Archiviazione di Azure mantiene costantemente più repliche integre dei dati.

1.  Fare clic su **CREA ACCOUNT DI ARCHIVIAZIONE**.

## Configurare il progetto

In questa sezione verrà configurata l'applicazione per usare l'account di archiviazione appena creato.  Verranno fornite informazioni su come ottenere le impostazioni di connessione dal portale di Azure,  quindi verrà eseguita l'applicazione in locale.

1.  Nel [portale di gestione di Azure][] fare clic sull'account di archiviazione creato nella sezione precedente.

1.  Fare clic su **GESTISCI CHIAVI DI ACCESSO**.

  	![Finestra di dialogo Gestisci chiavi di accesso](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  In Visual Studio fare clic con il pulsante destro del mouse sul nodo del progetto in Esplora soluzioni e scegliere **Proprietà**.  Fare clic sulla scheda **Debug**.

  	![Impostazioni di debug del progetto](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  Impostare i valori delle variabili di ambiente richieste dall'applicazione in **Comando debug server**, **Ambiente**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    In questo modo verranno impostate le variabili di ambiente quando si sceglie **Avvia debug**.  Se si vuole che le variabili vengano impostate quando si sceglie l'opzione **Avvia senza eseguire debug**, impostare gli stessi valori anche in **Comando esecuzione server**.

    In alternativa, è possibile definire variabili di ambiente usando il Pannello di controllo di Windows.  Si tratta di un'opzione migliore se si vuole evitare di archiviare le credenziali nel codice sorgente/nel file del progetto.  Si noti che è necessario riavviare Visual Studio affinché i nuovi valori di ambiente siano disponibili per l'applicazione.

1.  Il codice che implementa il repository di archiviazione tabelle di Azure si trova in **models/azuretablestorage.py**.  Vedere la [documentazione] per altre informazioni su come usare il servizio tabelle da Python.

1.  Eseguire l'applicazione con <kbd>F5</kbd>.  I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati nell'archiviazione tabelle di Azure.

1.  Passare alla pagina **About** per verificare che l'applicazione usi il repository di **archiviazione tabelle di Azure**.

  	![Web browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

## Esplorare l'archiviazione tabelle di Azure

È facile visualizzare e modificare le tabelle di archiviazione tramite Esplora server in Visual Studio.  In questa sezione si userà Esplora server per visualizzare il contenuto delle tabelle dell'applicazione di sondaggio.

> [AZURE.NOTE] A tale scopo, è necessario che siano installati gli strumenti di Microsoft Azure, disponibili come parte di [Azure SDK per .NET][].

1.  Aprire **Esplora server**.  Espandere **Azure**, **Archiviazione**, l'account di archiviazione, quindi **Tabelle**.

  	![Esplora server](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)

1.  Fare doppio clic su **polls** o **choices** per visualizzare i contenuti delle tabelle in una finestra di documento, nonché aggiungere, rimuovere o modificare entità.

  	![Risultati della query relativa alla tabella](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## Pubblicare in un sito Web di Azure

PTVS offre un semplice metodo per distribuire l'applicazione Web in un sito Web di Azure.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Pubblica**.

  	![Finestra di dialogo Pubblica sito Web](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Fare clic su **Siti Web di Microsoft Azure**.

1.  Fare clic su **Nuovo** per creare un nuovo sito.

1.  Selezionare un nome in **Nome sito** e un'area in **Area**, quindi fare clic su **Crea**.

  	![Finestra di dialogo Crea sito in Microsoft Azure](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonCreateWebSite.png)

1.  Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.

1.  Il sito pubblicato verrà aperto automaticamente nel Web browser.  Se si passa alla pagina relativa alle informazioni, si noterà che viene usato il repository **in memoria**, non il repository di **archiviazione tabelle di Azure**.

    Ciò avviene perché le variabili di ambiente non sono impostate per il sito Web di Azure, pertanto vengono usati i valori predefiniti specificati in **settings.py**.

## Configurare il sito Web di Azure

In questa sezione verranno configurate le variabili di ambiente per il sito.

1.  Nel [portale di gestione di Azure][] fare clic sul sito creato nella sezione precedente.

1.  Nel menu in alto fare clic su **CONFIGURA**.

  	![Menu superiore](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Scorrere verso il basso fino alla sezione **Impostazioni app** e impostare i valori per **REPOSITORY\_NAME**, **MONGODB\_HOST** e **STORAGE\_KEY** come descritto nella sezione precedente.

  	![Impostazioni app](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. Nel menu inferiore fare clic su **SALVA**, quindi su **RIAVVIA** e infine su **SFOGLIA**.

  	![Menu inferiore](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  L'applicazione dovrebbe funzionare come previsto, usando il repository di **archiviazione tabelle di Azure**.

    Congratulazioni.

  	![Web browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

## Passaggi successivi

Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Bottle e archiviazione tabelle di Azure.

- [Documentazione di Python Tools per Visual Studio][]
  - [Progetti Web][]
  - [Progetti servizio cloud][]
  - [Debug remoto in Microsoft Azure][]
- [Documentazione di Bottle][]
- [Archiviazione di Azure][]
- [Azure SDK per Python][]
- [Come usare il servizio di archiviazione tabelle di Python][]


<!--Link references-->
[Centro per sviluppatori Python]: /develop/python/
[Servizi cloud di Azure]: cloud-services-python-ptvs.md
[documentazione]: storage-python-how-to-use-table-storage.md
[Come usare il servizio di archiviazione tabelle di Python]: storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Portale di gestione di Azure]: https://manage.windowsazure.com
[Azure SDK per .NET]: http://azure.microsoft.com/downloads/
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX di esempio di Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
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

<!--HONumber=52-->