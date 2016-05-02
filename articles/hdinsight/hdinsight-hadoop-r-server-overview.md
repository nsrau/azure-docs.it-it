<properties
	pageTitle="Cos'è R su HDInsight? Introduzione a R Server su HDInsight (anteprima) | Microsoft Azure"
	description="Cos'è R Server su HDInsight (anteprima) e come usare R per creare applicazioni per l'analisi di Big Data."
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/29/2016"
   ms.author="jeffstok"/>


#Panoramica: R Server su HDInsight (anteprima)

R Server è ora disponibile come opzione in HDInsight Premium quando si creano cluster HDInsight in Azure. Questa nuova capacità offre ai data scientist, agli statistici e ai programmatori R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight. I cluster possono essere dimensionati in base ai progetti e alle attività ed eliminati quando non servono più. Essendo parte di Azure HDInsight, questi cluster includono il supporto 24/7 a livello di organizzazione, un contratto di servizio che prevede tempi di attività pari al 99,9% e la flessibilità di integrazione con altri componenti dell'ecosistema Azure.

>[AZURE.NOTE] R Server è disponibile solo con HDInsight Premium. Attualmente HDInsight Premium è disponibile soltanto per i cluster Hadoop e Spark. Al momento perciò è possibile scegliere R Server unicamente con i cluster Hadoop e Spark su HDInsight. Per altre informazioni, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight](hdinsight-component-versioning.md).

R Server su HDInsight offre le più recenti funzioni di analisi basate su R su set di dati di grandi dimensioni caricati nel BLOB di Azure (WASB). Dato che R Server si basa su R open source, le applicazioni basate su R che vengono compilate possono sfruttare gli oltre 8000 pacchetti R open source disponibili, nonché le routine in ScaleR, il pacchetto di analisi dei Big Data di Microsoft incluso in R Server. Il nodo perimetrale dei cluster Premium offre una comoda destinazione per la connessione al cluster e l'esecuzione degli script R. Con un nodo edge è possibile eseguire funzioni distribuite parallelizzate di ScaleR nei core del server del nodo edge o tra i nodi del cluster tramite l'uso di contesti di calcolo Hadoop MapReduce o Spark di ScaleR. I modelli o le previsioni che risultano dalle analisi possono essere scaricate per l'uso locale o impiegate altrove in Azure, ad esempio mediante un [servizio Web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md) di [Azure Machine Learning Studio](http://studio.azureml.net) (AML).

## Introduzione all'uso di R su HDInsight

Per includere R Server in un cluster HDInsight, è necessario creare un cluster Hadoop o Spark con l'opzione Premium nel Portale di Azure. Entrambi i tipi di cluster usano la stessa configurazione che include R Server sui nodi dati di un cluster e un nodo perimetrale come destinazione per le analisi basate su R Server. Per la procedura dettagliata di creazione di un cluster, vedere le [informazioni introduttive riguardo a R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md).

## Opzioni di archiviazione dei dati

La risorsa di archiviazione predefinita per i cluster HDInsight è Azure Blob (WASB) con il file system HDFS mappato a un contenitore BLOB. Ciò assicura che i dati caricati nella risorsa di archiviazione del cluster o scritti in essa durante il corso dell'analisi siano resi persistenti. Un comodo metodo di copiare i dati da e verso un BLOB è ricorrere all'utilità [AzCopy](../storage/storage-use-azcopy/).

È anche possibile ricorrere a [File di Azure](../storage/storage-how-to-use-files-linux.md) come opzione di archiviazione per l'uso sul nodo perimetrale. File di Azure consente di montare una condivisione file creata su un account di Archiviazione di Azure nel file system Linux. Per altre informazioni sulle opzioni di archiviazione dei dati per R Server su cluster HDInsight, vedere l'articolo dedicato alle [opzioni di archiviazione per R Server su cluster HDInsight](hdinsight-hadoop-r-server-storage.md).
  
## Accesso a R Server sul cluster

Dopo aver creato un cluster con R Server, è possibile connettersi alla console di R sul nodo perimetrale del cluster mediante SSH/Putty o mediante un browser se si sceglie di installare facoltativamente l'IDE di RStudio Server sul nodo perimetrale. Per altre informazioni sull'installazione di RStudio Server, vedere l'articolo dedicato all'[installazione di RStudio Server su cluster HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## Sviluppo ed esecuzione di script R

Gli script R che vengono creati ed eseguiti possono sfruttare gli oltre 8000 pacchetti R open source disponibili nonché le routine parallelizzate e distribuite della libreria ScaleR. Uno script che viene eseguito in R Server sul nodo perimetrale sarà eseguito qui mediante l'interprete R, eccetto per i passaggi che richiamano una delle funzioni ScaleR con un contesto di calcolo impostato su Hadoop MapReduce (RxHadoopMR) o Spark (RxSpark). In questi casi la funzione sarà eseguita in modo distribuito su tutti i nodi dati (attività) del cluster associati con i dati a cui si fa riferimento. Per altre informazioni sulle diverse opzioni relative al contesto di calcolo, vedere l'articolo dedicato alle [opzioni per il contesto di calcolo di R Server su HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

## Messa in funzione di un modello

Dopo aver completato la modellazione dei dati, è possibile mettere in funzione il modello per generare previsioni sui nuovi dati, una procedura che viene anche definita assegnazione di punteggi, sia in Azure sia localmente. Ecco alcuni esempi.

### Assegnazione di punteggi in HDInsight

Per assegnare punteggi in HDInsight, si potrebbe scrivere una funzione R che richiama il modello al fine di fare previsioni su un nuovo file di dati che è stato caricato nell'account di Archiviazione di Azure e salvare le previsioni nell'account di archiviazione. Si potrebbe eseguire la routine su richiesta sul nodo perimetrale del cluster o usando un processo pianificato.

### Assegnazione di punteggi in Azure Machine Learning 

Per assegnare punteggi usando un servizio Web Azure Machine Learning, si potrebbe ricorrere al [pacchetto R open source Azure Machine Learning ](http://www.inside-r.org/blogs/2015/11/18/enhancements-azureml-package-connect-r-azureml-studio) per [pubblicare il modello come servizio Web di Azure ](http://www.r-bloggers.com/deploying-a-car-price-model-using-r-and-azureml/), usare le funzioni di Azure Machine Learning per creare un'interfaccia utente per il servizio Web e poi richiamare il servizio Web quando necessario per l'assegnazione dei punteggi. Se si sceglie questa opzione, sarà necessario convertire gli oggetti del modello ScaleR in oggetti equivalenti del modello open source per l'uso con il servizio Web. È possibile farlo mediante le funzioni di coercizione di ScaleR, come `as.randomForest()`, per i modelli basati su insiemi.
  
### Assegnazione di punteggi in locale

Per assegnare punteggi in locale dopo aver creato il modello, si potrebbe serializzare il modello in R, scaricarlo in locale, deserializzarlo e poi usarlo per assegnare punteggi ai nuovi dati. È possibile farlo mediante l'approccio descritto nella precedente sezione [Assegnazione di punteggi in HDInsight](#scoring-in-hdinsight) o usando [DeployR](https://deployr.revolutionanalytics.com/).

## Manutenzione del cluster 

### Installazione e manutenzione dei pacchetti R

Molti dei pacchetti R che si usano saranno necessari sul nodo perimetrale, in quanto la porzione principale degli script R sarà eseguita qui. Per installare altri pacchetti R sul nodo perimetrale è possibile usare il consueto metodo `install.packages()` in R.
  
Nella maggior parte dei casi non sarà necessario installare altri pacchetti R sui nodi dati se si usano solo routine della libreria ScaleR da eseguire in tutto il cluster. Potrebbero invece essere necessari pacchetti aggiuntivi per supportare l'uso dell'esecuzione **rxExec** o **RxDataStep** sui nodi dati. In questi casi i pacchetti aggiuntivi devono essere specificati mediante l'uso di un'azione script dopo la creazione del cluster. Per altre informazioni, vedere l'articolo dedicato alla [creazione di un cluster HDInsight con R Server](hdinsight-hadoop-r-server-get-started.md).
  
### Modifica delle impostazioni di memoria per Hadoop MapReduce 

È possibile modificare un cluster per cambiare la quantità di memoria disponibile per R Server quando si esegue un processo MapReduce. A tale scopo, usare l'interfaccia utente Ambari disponibile nel pannello del Portale di Azure per il cluster. Per le istruzioni su come accedere all'interfaccia utente Ambari per il cluster, vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md).

È inoltre possibile modificare la quantità di memoria disponibile per R Server usando switch Hadoop nella chiamata di **RxHadoopMR**, come mostrato di seguito.
 
	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### Dimensionamento del cluster

Un cluster esistente può essere dimensionato, in aumento o in riduzione, mediante il Portale di Azure. In questo modo è possibile ottenere la capacità aggiuntiva necessaria per le elaborazioni più estese e ridurre nuovamente il cluster quando la capacità rimane inutilizzata. Per istruzioni su come dimensionare un cluster, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md).

### Manutenzione del sistema 

La manutenzione viene eseguita sulle macchine virtuali Linux sottostanti di un cluster HDInsight durante gli orari di minore attività per applicare patch del sistema operativo e altro. In genere queste procedure vengono eseguite alle 3:30 del mattino (ora locale della macchina virtuale) il lunedì e il giovedì, tutte le settimane. Gli aggiornamenti vengono applicati in modo da non interessare oltre un quarto del cluster alla volta. Poiché i nodi head sono ridondanti e non tutti i nodi dati sono influenzati dalla procedura, i processi che vengono eseguiti in questo intervallo di tempo potrebbero essere rallentati ma dovrebbero giungere al termine. Gli eventuali software personalizzati che potrebbero essere installati o i dati locali vengono preservati durante tutti questi eventi di manutenzione, a meno che si verifichi un errore catastrofico che richiede la ricostruzione del cluster.

## Opzioni IDE per R Server su un cluster HDInsight

Il nodo perimetrale Linux di un cluster HDInsight Premium è la destinazione delle analisi basate su R. Dopo la connessione al cluster, è possibile avviare la console di interfaccia di R Server digitando "R" al prompt dei comandi di Linux. L'uso della console di interfaccia è più semplice se si esegue un editor di testo per lo sviluppo di script R in un'altra finestra e si tagliano e incollano le sezioni dello script nella console R.
  
Una procedura un po' più avanzata rispetto all'uso di un semplice editor di testo per lo sviluppo dello script R è usare un'IDE per desktop basata su R, come [R Tools per Visual Studio](https://www.visualstudio.com/it-IT/features/rtvs-vs.aspx) (RTVS), una famiglia di strumenti desktop e server di [RStudio](https://www.rstudio.com/products/rstudio-server/) annunciata di recente da Microsoft, o [StatET](http://www.walware.de/goto/statet), basato su Eclipse, di Walware.
  
Un'altra opzione è installare un'IDE sul nodo perimetrale stesso di Linux. Una scelta molto comune in questi casi è [RStudio Server](https://www.rstudio.com/products/rstudio-server/) che offre un'IDE basata sul browser per l'uso da parte di client remoti. L'installazione di RStudio Server nel nodo perimetrale di un cluster HDInsight Premium offre un'esperienza IDE completa per lo sviluppo e l'esecuzione di script R con R Server nel cluster e può essere molto più produttiva rispetto all'uso della console di R predefinita. Se si desidera usare RStudio Server, vedere l'articolo dedicato all'[installazione di RStudio Server su cluster HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## Prezzi
 
Le tariffe per i cluster HDInsight Premium con R Server sono strutturate in modo simile a quelle per i cluster HDInsight standard e saranno basate sul dimensionamento delle macchine virtuali sottostanti sui nodi nome, dati e perimetrali, con una maggiorazione nelle ore di punta per Premium. Per altre informazioni sui prezzi di HDInsight Premium, inclusi i prezzi relativi alla fase di anteprima pubblica, e la disponibilità della versione di prova gratuita di 30 giorni, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Passaggi successivi

Per leggere altre informazioni sull'uso di R Server con i cluster HDInsight, seguire i collegamenti riportati di seguito.

- [Introduzione a R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Aggiungere RStudio Server a HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Opzioni del contesto di calcolo per Server R in HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

- [Opzioni di Archiviazione di Azure per R Server su HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

 

<!---HONumber=AcomDC_0420_2016-->