<properties
	pageTitle="Guida tecnica per il modello di soluzione per la previsione della domanda nel settore energetico | Microsoft Azure"
	description="Guida tecnica per il modello di soluzione di Microsoft Cortana Intelligence per la previsione della domanda nel settore energetico."
	services="cortana-analytics"
	documentationCenter=""
	authors="yijichen"
	manager="ilanr9"
	editor="yijichen"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="inqiu;yijichen;ilanr9"/>

# Guida tecnica per il modello di soluzione di Cortana Intelligence per la previsione della domanda nel settore energetico

## **Panoramica**

I modelli di soluzione sono progettati per accelerare il processo di compilazione di una demo end-to-end basata su Cortana Intelligence Suite. Un modello distribuito esegue il provisioning alla sottoscrizione dei componenti Cortana Intelligence necessari e consente di creare le relazioni tra di essi. Inoltre effettua il seeding della pipeline di dati con dati di esempio generati da un'applicazione di simulazione di dati. Scaricare il simulatore di dati dal collegamento specificato e installarlo nel computer locale. Fare riferimento al file readme.txt per istruzioni sull'uso del simulatore. I dati generati dal simulatore attivano la pipeline di dati e avviano la creazione di una stima di Machine Learning che possono essere visualizzate nel dashboard di Power BI.

Il processo di distribuzione prevede diversi passaggi per l'impostazione delle credenziali della soluzione. Assicurarsi di prendere nota delle credenziali, ad esempio il nome della soluzione, il nome utente e la password forniti durante la distribuzione.

L'obiettivo di questo documento è illustrare l'architettura di riferimento e i vari componenti di cui è stato eseguito il provisioning alla sottoscrizione nell'ambito di questo modello di soluzione. Il documento illustra anche come sostituire i dati di esempio con dati reali per poter visualizzare informazioni dettagliate o stime dai propri dati e illustra poi le parti del modello di soluzione da modificare per personalizzare la soluzione con i propri dati. Alla fine del documento vengono fornite istruzioni sulla compilazione del dashboard di Power BI per questo modello di soluzione.

## **Quadro generale**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### Spiegazione dell'architettura
Quando viene distribuita la soluzione, vengono attivati vari servizi di Azure all'interno della suite Cortana Analytics, *ad esempio* Hub eventi, Analisi di flusso, HDInsight, Data Factory, Machine Learning *e così via*. Il diagramma dell'architettura riportato sopra illustra, a livello generale, l'intero processo di costruzione del modello di soluzione per la previsione della domanda nel settore energetico. Sarà possibile esaminare i servizi facendo clic su di essi nel diagramma del modello di soluzione creato con la distribuzione della soluzione. Le sezioni seguenti descrivono i vari componenti.

## **Origine dati e inserimento**

### Origine dati sintetica

L'origine dati usata per questo modello viene generata da un'applicazione desktop da scaricare ed eseguire localmente al termine della distribuzione. Le istruzioni per il download e l'installazione di questa applicazione sono disponibili nella barra delle proprietà, selezionando il primo nodo denominato Energy Forecasting Data Simulator nel diagramma del modello di soluzione. Questa applicazione invia al servizio [Hub eventi di Azure](#azure-event-hub) punti dati, o eventi, che verranno usati nel flusso della soluzione.

L'applicazione di generazione eventi popola l'Hub eventi di Azure solo quando è in esecuzione nel computer.

### Hub eventi di Azure

Il servizio [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) è il destinatario dell'input fornito dall'origine dati sintetica descritta in precedenza.

## **Preparazione e analisi dei dati**


### Analisi di flusso di Azure

Il servizio [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/) consente di analizzare quasi in tempo reale il flusso di input dal servizio [Hub eventi di Azure](#azure-event-hub), pubblicare i risultati in un dashboard di [Power BI](https://powerbi.microsoft.com) e archiviare tutti gli eventi in ingresso non elaborati nel servizio [Archiviazione di Azure](https://azure.microsoft.com/services/storage/), per l'elaborazione successiva da parte del servizio [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

### Aggregazione personalizzata di HDInsight

Il servizio Azure HDInsight, orchestrato da Azure Data Factory, consente di eseguire script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) per aggregare eventi non elaborati archiviati usando il servizio Analisi di flusso di Azure.

### Azure Machine Learning

Il servizio [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), orchestrato da Azure Data Factory, consente di eseguire previsioni sul consumo futuro di elettricità di un'area specifica in base agli input ricevuti.

## **Pubblicazione dei dati**


### Servizio database SQL di Azure

Il servizio [database SQL di Azure](https://azure.microsoft.com/services/sql-database/), gestito da Azure Data Factory, consente di archiviare le stime ricevute dal servizio Azure Machine Learning che verranno usate nel dashboard di [Power BI](https://powerbi.microsoft.com).

## **Uso dei dati**

### Power BI

Il servizio [Power BI](https://powerbi.microsoft.com) consente di visualizzare un dashboard contenente le aggregazioni fornite dal servizio [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/), oltre ai risultati delle previsioni della domanda archiviati nel [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) e prodotti usando il servizio [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Per istruzioni sulla compilazione del dashboard di Power BI per questo modello di soluzione, vedere la sezione sottostante.

## **Come inserire i propri dati**

Questa sezione descrive come inserire i propri dati in Azure e quali aree sarebbe necessario modificare per i dati inseriti in questa architettura.

È poco probabile che i set di dati inseriti possano corrispondere al set di dati usato per questo modello di soluzione. Per poter adattare questo modello ai propri dati è fondamentale comprendere i dati e i requisiti. Se si tratta della prima esperienza con il servizio Azure Machine Learning, è possibile ottenere un'introduzione eseguendo l'esercitazione [Creare il primo esperimento](machine-learning\machine-learning-create-experiment.md).

Le sezioni seguenti illustrano le sezioni del modello che è necessario modificare quando viene introdotto un nuovo set di dati.

### Hub eventi di Azure

Il servizio [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) è molto generico e accetta la registrazione di dati in formato CSV o JSON. In Hub eventi di Azure non viene eseguita alcuna elaborazione speciale, ma è importante comprendere i dati inseriti.

Questo documento non descrive come inserire i dati, ma è possibile inviare facilmente eventi o dati a un hub eventi di Azure usando l'[API di Hub eventi](event-hubs\event-hubs-programming-guide.md).

### Analisi di flusso di Azure

Il servizio [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/) viene usato per fornire l'analisi quasi in tempo reale tramite la lettura dai flussi di dati e l'output dei dati in un numero qualsiasi di origini.

Per il modello di soluzione per la previsione della domanda nel settore energetico, la query di Analisi di flusso di Azure è costituita da due sottoquery che usano come input eventi provenienti dal servizio Hub eventi di Azure e inviano l'output a due posizioni distinte. L'output è costituito da un set di dati di Power BI e da una posizione di Archiviazione di Azure.

Per trovare la query di [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/):

-   Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).

-   Trovare i processi di Analisi di flusso ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) generati con la distribuzione della soluzione. Uno è per il push dei dati nell'archiviazione BLOB (ad esempio mytest1streaming432822asablob) e l'altro per il push dei dati in Power BI (ad esempio mytest1streaming432822asapbi).


-   Selezionare:

    -   ***INPUTS*** per visualizzare l'input della query.

    -   ***QUERY*** per visualizzare la query stessa.

    -   ***OUTPUTS*** per visualizzare i vari output.

Per informazioni sulla costruzione di query di Analisi di flusso di Azure, vedere la pagina relativa al [riferimento alla query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx) in MSDN.

All'interno di questo modello di soluzione viene fornito il processo di Analisi di flusso di Azure che restituisce come output un set di dati con informazioni di analisi quasi in tempo reale sul flusso di dati in ingresso in un dashboard di Power BI. Dal momento che la conoscenza del formato dei dati in ingresso è implicita, sarebbe necessario modificare queste query in base al formato dei propri dati.

L'altro processo di Analisi di flusso di Azure restituisce tutti gli eventi di [Hub eventi](https://azure.microsoft.com/services/event-hubs/) ad [Archiviazione di Azure](https://azure.microsoft.com/services/storage/). Non richiede quindi alcuna modifica, indipendentemente dal formato dei dati, dal momento che le informazioni complete sull'evento vengono trasmesse all'archiviazione.

### Data factory di Azure

Il servizio [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestra lo spostamento e l'elaborazione dei dati. Nel modello di soluzione per la previsione della domanda di energia, la data factory è costituita da 12 [pipeline](data-factory\data-factory-create-pipelines.md) che spostano ed elaborano i dati facendo uso di varie tecnologie.

  Per accedere alla data factory, è possibile aprire il nodo Data factory nella parte inferiore del diagramma del modello di soluzione creato con la distribuzione della soluzione. Si passerà alla data factory nel Portale di gestione di Azure. È possibile ignorare gli eventuali errori presenti nei set di dati, perché sono dovuti al fatto che la data factory è stata distribuita prima dell'avvio del generatore di dati. Questi errori non impediscono il funzionamento della data factory.

Questa sezione illustra le [pipeline](data-factory\data-factory-create-pipelines.md) e le [attività](data-factory\data-factory-create-pipelines.md) necessarie contenute in [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Di seguito è riportata la vista diagramma della soluzione.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Cinque delle pipeline di questa data factory contengono script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) usati per partizionare e aggregare i dati. Se specificato, gli script verranno inseriti nell'account di [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) creato durante l'installazione, nel percorso: demandforecasting\\\script\\\hive\\\ o, in alternativa, in: https://[Your della soluzione].blob.core.windows.net/demandforecasting.

Come le query di [Analisi di flusso di Azure](#azure-stream-analytics-1), gli script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) hanno una conoscenza implicita del formato dei dati in ingresso. Sarebbe quindi necessario modificare queste query in base al formato dei propri dati e ai requisiti di [progettazione delle funzionalità](machine-learning\machine-learning-feature-selection-and-engineering.md).

#### *AggregateDemandDataTo1HrPipeline*

Questa [pipeline](data-factory\data-factory-create-pipelines.md) contiene un'unica attività, [HDInsightHive](data-factory\data-factory-hive-activity.md), che usa un servizio [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx). Il servizio esegue uno script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) per aggregare i dati sulla domanda trasmessi ogni 10 secondi a livello di sottostazione nel livello dell'area oraria e inserirli in [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) attraverso il processo di Analisi di flusso di Azure.

Lo script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) per questa attività di partizionamento è ***AggregateDemandRegion1Hr.hql***.


#### *LoadHistoryDemandDataPipeline*

Questa [pipeline](data-factory\data-factory-create-pipelines.md) contiene due attività:
- L'attività [HDInsightHive](data-factory\data-factory-hive-activity.md), che usa un servizio [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) che esegue uno script Hive per aggregare i dati storici sulla domanda a livello di sottostazione nel livello dell'area oraria e inserirli in Archiviazione di Azure attraverso il processo di Analisi di flusso di Azure.

- L'attività [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), che sposta i dati aggregati dal BLOB di Archiviazione di Azure al database SQL di Azure di cui è stato effettuato il provisioning durante l'installazione del modello di soluzione.

Lo script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) per questa attività è ***AggregateDemandHistoryRegion.hql***.


#### *MLScoringRegionXPipeline*

Queste [pipeline](data-factory\data-factory-create-pipelines.md) contengono diverse attività il cui risultato finale è costituito dalle stime con punteggio provenienti dall'esperimento di Azure Machine Learning associato a questo modello di soluzione. Sono quasi identiche, ad eccezione del fatto che ognuna gestisce solo un'area specifica. Ciò avviene perché alla pipeline ADF viene passato un RegionID diverso e per ogni area viene eseguito uno script diverso. Le attività contenute nella pipeline sono:
-	L'attività [HDInsightHive](data-factory\data-factory-hive-activity.md), che usa un servizio [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) con uno script Hive per eseguire le operazioni necessarie di aggregazione e progettazione delle funzioni per l'esperimento di Azure Machine Learning. Gli script Hive per questa attività sono ***PrepareMLInputRegionX.hql***.

-	L'attività [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), che sposta i risultati dall'attività [HDInsightHive](data-factory\data-factory-hive-activity.md) in un unico BLOB di Archiviazione di Azure accessibile dall'attività [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).

-	L'attività [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx), che chiama l'esperimento di Azure Machine Learning con cui i risultati vengono inseriti in un unico BLOB di Archiviazione di Azure.

#### *CopyScoredResultRegionXPipeline*
Queste [pipeline](data-factory\data-factory-create-pipelines.md) contengono un'unica attività, [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), che sposta i risultati dell'esperimento di Azure Machine Learning dalla rispettiva ***MLScoringRegionXPipeline*** al database SQL di Azure di cui è stato effettuato il provisioning durante l'installazione del modello di soluzione.

#### *CopyAggDemandPipeline*
Queste [pipeline](data-factory\data-factory-create-pipelines.md) contengono un'unica attività, [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), che sposta i dati aggregati sulla domanda in corso da ***LoadHistoryDemandDataPipeline*** al database SQL di Azure di cui è stato effettuato il provisioning durante l'installazione del modello di soluzione.

#### *CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Queste [pipeline](data-factory\data-factory-create-pipelines.md) contengono un'unica attività, [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), che sposta i dati di riferimento di area/sottostazione/topologia caricati nel BLOB di Archiviazione di Azure durante l'installazione del modello di soluzione nel database SQL di Azure di cui è stato effettuato il provisioning durante l'installazione del modello di soluzione.

### Azure Machine Learning
L'esperimento di [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) usato per questo modello di soluzione fornisce la previsione della domanda dell'area. L'esperimento è specifico per il set di dati usato. Per l'uso con dati propri dovrà quindi essere modificato o sostituito in base alle specifiche dei dati inseriti.

## **Monitorare lo stato**
Dopo aver avviato il generatore di dati, la pipeline inizia ad attivarsi e i diversi componenti della soluzione cominciano ad avviarsi in base ai comandi inviati da Data factory. Esistono due modi per monitorare la pipeline.

1. Caricare dati dall'archiviazione BLOB di Azure

	Uno dei processi di Analisi di flusso scrive i dati in ingresso non elaborati nell'archivio BLOB. Se si fa clic sul componente **Archivio BLOB di Azure** della soluzione dalla schermata in cui è stata distribuita la soluzione e si fa clic su **Apri** nel pannello di destra, verrà visualizzato il [portale di gestione di Azure](https://portal.azure.com). Nel portale fare clic su **BLOB**. Nel pannello successivo verrà visualizzato un elenco di contenitori. Fare clic su **energysadata**. Nel pannello successivo verrà visualizzata la cartella **demandongoing**. All'interno della cartella rawdata saranno presenti cartelle con nomi quali date=2016-01-28 e così via. Se vengono visualizzate queste cartelle, significa che i dati non elaborati sono stati generati nel computer e archiviati nell'archivio BLOB. Verranno visualizzati file che in tali cartelle devono avere dimensioni limitate in MB.

2. Caricare dati dal database SQL di Azure

	L'ultimo passaggio della pipeline consiste nella scrittura di dati, ad esempio stime da Machine Learning, nel database SQL. Possono essere necessarie al massimo due ore prima che i dati siano visibili nel database SQL. È possibile monitorare la quantità di dati disponibile nel database SQL usando il [portale di gestione di Azure](https://manage.windowsazure.com/). Nel riquadro di sinistra trovare DATABASE SQL ![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) e fare clic su questa voce. Individuare quindi il database (ad esempio demo123456db) e selezionarlo. Nella sezione **Connetti al database** della pagina successiva fare clic su **Esegui query Transact-SQL nel database SQL**.

	Qui è possibile fare clic su Nuova query ed eseguire una query per il numero di righe (ad esempio "select count(*) from DemandRealHourly)" Man mano che il database cresce, il numero delle righe nella tabella dovrebbe aumentare.

3. Caricare dati dal dashboard di Power BI.

	È possibile impostare un dashboard per il percorso critico di Power BI per monitorare i dati non elaborati in ingresso. Seguire le istruzioni nella sezione "Dashboard di Power BI".



## **Dashboard di Power BI**

### Panoramica

Questa sezione descrive come configurare il dashboard di Power BI per visualizzare i dati in tempo reale provenienti da Analisi di flusso di Azure (percorso critico), nonché i risultati di previsione provenienti da Azure Machine Learning (percorso non critico).


### Configurare il dashboard per il percorso critico

La procedura seguente mostra come visualizzare l'output dei dati in tempo reale da processi di Analisi di flusso generati al momento della distribuzione della soluzione. Per seguire questa procedura è necessario un account di [Power BI online](http://www.powerbi.com/). Se non è disponibile un account, è possibile [crearne uno](https://powerbi.microsoft.com/pricing).

1.  Aggiungere l'output di Power BI ad Analisi di flusso di Azure.

    -  Per impostare l'output del processo di Analisi di flusso di Azure come dashboard di Power BI, seguire le istruzioni in [Analisi di flusso di Azure e Power BI: Dashboard di analisi in tempo reale per una visibilità in tempo reale dei flussi di dati](stream-analytics-power-bi-dashboard.md).

	- Trovare il processo di Analisi di flusso nel [portale di gestione di Azure](https://manage.windowsazure.com). Il nome del processo deve essere formato come segue: NomeSoluzione+"streamingjob"+numero casuale+"asapbi" (ad esempio demostreamingjob123456asapbi).

	- Aggiungere un output di PowerBI per il processo ASA. Impostare l'**Alias di output** su **'PBIoutput'**. Impostare il **Nome del set di dati** e il **Nome tabella** su **'EnergyStreamData'**. Dopo aver aggiunto l'output, fare clic su **Avvia** nella parte inferiore della pagina per avviare il processo di Analisi di flusso. Viene visualizzato un messaggio di conferma *simile al seguente*: "L'avvio del processo di Analisi di flusso myteststreamingjob12345asablob è stato completato".

2. Accedere a [Power BI online](http://www.powerbi.com).

    -   In My Workspace, nella sezione Datasets del pannello di sinistra di Power BI, dovrebbe essere visualizzato un nuovo set di dati. Si tratta dei dati di flusso di cui si è eseguito il push da Analisi di flusso di Azure nel passaggio precedente.

    -   Assicurarsi che il riquadro ***Visualizations*** sia aperto e visualizzato sul lato destro dello schermo.

3. Creare il riquadro "Demand by Timestamp":
	-	Fare clic sul set di dati **EnergyStreamData** nella sezione Datasets del pannello di sinistra.

	-	Fare clic sull'icona del **grafico a linee** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

	-	Fare clic su EnergyStreamData nel pannello **Fields**.

	-	Fare clic su **Timestamp** e verificare che sia visualizzato in Axis. Fare clic su **Load** e verificare che sia visualizzato in Values.

	-	Fare clic su **SAVE** nella parte superiore e assegnare al report il nome EnergyStreamDataReport. Il report EnergyStreamDataReport verrà visualizzato nella sezione Reports del riquadro di spostamento a sinistra.

	-	Fare clic sull'icona di **aggiunta di un oggetto visivo**![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) nell'angolo superiore destro di questo grafico a linee. Potrebbe essere visualizzata la finestra Pin to Dashboard da cui scegliere un dashboard. Selezionare EnergyStreamDataReport, quindi fare clic su Pin.

	-	Passare il puntatore del mouse su questo riquadro nel dashboard e fare clic sull'icona "Modifica" nell'angolo superiore destro per modificarne il titolo in "Demand by Timestamp".

4.	Creare altri riquadri del dashboard in base a set di dati appropriati. La visualizzazione finale del dashboard è riportata di seguito. ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### Configurare il dashboard per il percorso non critico
Nella pipeline di dati del percorso non critico l'obiettivo principale consiste nell'ottenere la previsione della domanda di ogni area. Power BI si connette a un database SQL di Azure come origine dati, in cui sono archiviati i risultati della stima.

> [AZURE.NOTE] 1) Per raccogliere risultati di previsione sufficienti per il dashboard è necessaria qualche ora. Si consiglia di iniziare questo processo 2-3 ore dopo l'avvio del generatore di dati. 2) In questo passaggio il download e l'installazione del software gratuito [Power BI Desktop](https://powerbi.microsoft.com/desktop) sono un prerequisito.



1.  Ottenere le credenziali del database.

    Prima di procedere con i passaggi successivi, è necessario ottenere **il nome del server di database, il nome del database, il nome utente e la password**. I passaggi per ottenerli sono riportati di seguito.

    -   Quando la voce **Database SQL di Azure** nel diagramma del modello di soluzione diventa verde, selezionarla e quindi fare clic su **Apri**. Si passerà al Portale di gestione di Azure e verrà aperta la pagina di informazioni del database.

    -   Nella pagina è presente la sezione Database in cui sono elencati i database creati dall'utente. Il nome del database deve essere formato da **nome della soluzione + numero casuale + db**, ad esempio, "mytest12345db".

	-	Fare clic sul database. Nella parte superiore del pannello a comparsa visualizzato è possibile trovare il nome del server di database. Il nome del server di database deve essere formato da **nome della soluzione + numero casuale + database.windows.net,1433**, ad esempio "mytest12345.database.windows.net,1433".

	-   Il **nome utente** e la **password** del database sono quelli registrati in precedenza durante la distribuzione della soluzione.

2.	Aggiornare l'origine dati del file percorso non critico di Power BI
	-  Assicurarsi che sia installata la versione più recente di [Power BI Desktop](https://powerbi.microsoft.com/desktop).

	-	Nella cartella **DemandForecastingDataGeneratorv1.0** scaricata fare doppio clic sul file **Power BI Template\\DemandForecastPowerBI.pbix**. Le visualizzazioni iniziali sono basate su dati fittizi. **Nota:** se viene visualizzato un messaggio di errore, assicurarsi che sia installata la versione più recente di Power BI Desktop.

		Dopo averlo aperto, fare clic su **Modifica query** nella parte superiore del file. Nella finestra popup fare doppio clic su **Origine** nel pannello di destra. ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

	-   Nella finestra popup sostituire **Server** e **Database** con i nomi del server e del database usati e quindi fare clic su **OK**. Per il nome del server, assicurarsi di specificare la porta 1433, **NomeDellaSoluzione.database.windows.net, 1433**. Ignorare i messaggi di avviso visualizzati sullo schermo.

	-   Nella finestra popup successiva sono disponibili due opzioni nel pannello di sinistra, **Windows** e **Database**. Fare clic su **Database** e inserire il proprio **nome utente** e **password**, ovvero il nome utente e la password immessi al momento della prima distribuzione della soluzione e della creazione del database SQL di Azure. In ***Selezionare il livello a cui applicare queste impostazioni*** selezionare l'opzione relativa al livello di database. Fare clic su **Connetti**.

	-   Quando viene di nuovo visualizzata la pagina precedente, chiudere la finestra. Nel messaggio popup visualizzato fare clic su **Applica**. Infine, fare clic sul pulsante **Salva** per salvare le modifiche. Il file di Power BI è ora connesso al server. Se le visualizzazioni sono vuote, assicurarsi di annullare le selezioni per visualizzare tutti i dati facendo clic sull'icona della gomma nell'angolo superiore destro della legenda. Usare il pulsante Aggiorna per aggiornare le visualizzazioni con i nuovi dati. Inizialmente le visualizzazioni contengono solo i dati di seeding, perché l'aggiornamento della data factory è pianificato ogni 3 ore. Dopo 3 ore, aggiornando i dati nelle visualizzazioni saranno disponibili le nuove stime.

3. (Facoltativo) Pubblicare il dashboard per il percorso non critico in [Power BI online](http://www.powerbi.com/). Si noti che per questo passaggio è necessario un account Power BI o Office 365.

	-   Fare clic su **Pubblica**. Dopo alcuni secondi viene visualizzato un messaggio di conferma della pubblicazione in Power BI con un segno di spunta verde. Fare clic sul collegamento sotto Apri demoprediction.pbix in Power BI. Per istruzioni dettagliate, vedere [Pubblicare da Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

	-   Per creare un nuovo dashboard, fare clic sul segno **+** accanto alla sezione **Dashboard** nel riquadro di sinistra. Immettere il nome "Demand Forecasting Demo" per questo nuovo dashboard.

	-   Dopo aver aperto il report, fare clic su ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) per aggiungere tutte le visualizzazioni al dashboard. Per istruzioni dettagliate, vedere [Aggiungere un riquadro a un dashboard di Power BI da un report](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Passare alla pagina del dashboard, regolare le dimensioni e la posizione delle visualizzazioni e modificarne i titoli. Per istruzioni dettagliate su come modificare i riquadri, vedere [Modificare un riquadro: ridimensionare, spostare, rinominare, aggiungere ed eliminare un collegamento ipertestuale](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Di seguito è riportato un dashboard di esempio a cui sono state aggiunte alcune visualizzazioni del percorso non critico.

		![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Facoltativo) Pianificare l'aggiornamento dell'origine dati.
	-	  Per pianificare l'aggiornamento dei dati, passare il puntatore del mouse sul set di dati **EnergyBPI-Final**, fare clic su ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) e quindi scegliere **Pianifica aggiornamento**. **Nota:** se viene visualizzato un messaggio di avviso, fare clic su **Modifica credenziali** e assicurarsi che le credenziali del database siano identiche a quelle illustrate nel passaggio 1.

	![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

	-   Espandere la sezione **Pianifica aggiornamento**. Attivare l'opzione "Mantieni aggiornati i dati".

	-   Pianificare l'aggiornamento in base alle esigenze. Per altre informazioni, vedere [Aggiornamento dei dati in Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).


## **Come eliminare la soluzione**
Accertarsi di arrestare il generatore di dati quando non lo si usa in modo attivo, poiché l'esecuzione di questa applicazione può comportare costi elevati. Se non la si usa, è consigliabile eliminarla. Con l'eliminazione di questa soluzione vengono eliminati anche tutti i componenti di cui è stato effettuato il provisioning nella sottoscrizione durante la procedura di distribuzione. Per eliminare la soluzione, fare clic sul nome della soluzione nel riquadro sinistro del modello e su Elimina.

## **Strumenti di stima dei costi**

I due strumenti indicati di seguito permettono di comprendere meglio i costi totali per l'esecuzione del modello di soluzione previsione della domanda nel settore energetico nella propria sottoscrizione:

-   [Calcolatore prezzi (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure Cost Estimator Tool (PC desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

## **Riconoscimenti**
Questo articolo è stato redatto dal ricercatore informatico Yijing Chen e dal software engineer Min Qiu presso Microsoft.

<!---HONumber=AcomDC_0615_2016-->