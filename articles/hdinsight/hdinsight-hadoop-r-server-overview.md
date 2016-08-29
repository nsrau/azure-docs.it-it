<properties
	pageTitle="Cos'è R su HDInsight? Introduzione a R Server su HDInsight (anteprima) | Microsoft Azure"
	description="Cos'è R Server su HDInsight (anteprima) e come usare R Server per creare applicazioni per l'analisi di Big Data."
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
   ms.date="08/17/2016"
   ms.author="jeffstok"/>  


# Panoramica di R Server su HDInsight (anteprima)

Microsoft R Server è ora disponibile come opzione in Microsoft Azure HDInsight Premium quando si creano cluster HDInsight in Azure. Questa nuova capacità offre ai data scientist, agli statistici e ai programmatori R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight.

I cluster possono essere dimensionati in base ai progetti e alle attività ed eliminati quando non servono più. Essendo parte di Azure HDInsight, questi cluster includono il supporto 24/7 a livello di organizzazione, un contratto di servizio che prevede tempi di attività pari al 99,9% e la flessibilità di integrazione con altri componenti dell'ecosistema Azure.

>[AZURE.NOTE] R Server è disponibile solo con HDInsight Premium. Attualmente HDInsight Premium è disponibile soltanto per i cluster Hadoop e Spark. Al momento perciò è possibile scegliere R Server unicamente con i cluster Hadoop e Spark su HDInsight. Per altre informazioni, vedere [Quali sono i diversi componenti di Hadoop disponibili in HDInsight?](hdinsight-component-versioning.md)

R Server su HDInsight offre le più recenti funzioni di analisi basate su R su set di dati di grandi dimensioni caricati nell'archiviazione BLOB di Azure. Dato che R Server si basa su R open source, le applicazioni basate su R che vengono compilate possono sfruttare gli oltre 8000 pacchetti R open source disponibili, nonché le routine in ScaleR, il pacchetto di analisi dei Big Data di Microsoft incluso in R Server.

Il nodo perimetrale dei cluster Premium offre un comodo posizionamento per la connessione al cluster e l'esecuzione degli script R. Con un nodo perimetrale è possibile eseguire funzioni distribuite parallelizzate di ScaleR nei core del server del nodo perimetrale. È anche possibile eseguire tali funzioni tra i nodi del cluster usando contesti di calcolo Hadoop MapReduce o Spark di ScaleR.

I modelli o le previsioni che risultano dalle analisi possono essere scaricate per l'uso locale o impiegate altrove in Azure, ad esempio tramite un [servizio Web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md) di [Azure Machine Learning Studio](http://studio.azureml.net).

## Introduzione all'uso di R su HDInsight

Per includere R Server in un cluster HDInsight, è necessario creare un cluster Hadoop o Spark con l'opzione Premium durante la creazione del cluster nel Portale di Azure. Entrambi i tipi di cluster usano la stessa configurazione che include R Server sui nodi dati di un cluster e un nodo perimetrale come destinazione per le analisi basate su R Server. Per la procedura dettagliata di creazione di un cluster, vedere [Introduzione all'uso di R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md).

## Informazioni sulle opzioni di archiviazione dei dati

La risorsa di archiviazione predefinita per i cluster HDInsight è l'archiviazione BLOB con il file system HDFS mappato a un contenitore BLOB. Ciò assicura che i dati caricati nella risorsa di archiviazione del cluster o scritti in essa durante il corso dell'analisi siano resi persistenti. È possibile usare l'utilità [AzCopy](../storage/storage-use-azcopy.md) per copiare i dati da e verso il BLOB.

Oltre all'archiviazione BLOB, è possibile usare anche l'[archiviazione di Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) con il cluster. Se si usa Data Lake, è possibile archiviare il file HDFS sia con l'archiviazione BLOB sia con l'archiviazione Data Lake.

È anche possibile ricorrere a [File di Azure](../storage/storage-how-to-use-files-linux.md) come opzione di archiviazione per l'uso sul nodo perimetrale. File di Azure consente di montare una condivisione file creata nell'Archiviazione di Azure sul file system Linux. Per altre informazioni sulle opzioni di archiviazione dei dati per R Server su cluster HDInsight, vedere [Storage options for R Server on HDInsight clusters](hdinsight-hadoop-r-server-storage.md) (Opzioni di Archiviazione di Azure per R Server su cluster HDInsight).

## Accedere a R Server sul cluster

Dopo aver creato un cluster con R Server, è possibile connettersi alla console di R sul nodo perimetrale del cluster tramite SSH/PuTTY. Se si sceglie di installare l'IDE facoltativa di RStudio Server sul nodo perimetrale, è anche possibile connettersi tramite un browser. Per altre informazioni sull'installazione di RStudio Server, vedere [Installing RStudio Server on HDInsight clusters](hdinsight-hadoop-r-server-install-r-studio.md) (Installazione di RStudio Server su cluster HDInsight).

## Sviluppare ed eseguire script R

Gli script R che vengono creati ed eseguiti possono sfruttare gli oltre 8000 pacchetti R open source disponibili nonché le routine parallelizzate e distribuite della libreria ScaleR. In generale, uno script che viene eseguito in R Server sul nodo perimetrale sarà eseguito qui all'interno dell'interprete R su tale nodo. Sono esclusi i passaggi che richiamano una delle funzioni ScaleR con un contesto di calcolo impostato su Hadoop MapReduce (RxHadoopMR) o Spark (RxSpark).

In questi casi la funzione viene eseguita in modo distribuito su tutti i nodi dati (attività) del cluster associati con i dati a cui si fa riferimento. Per altre informazioni sulle diverse opzioni relative al contesto di calcolo, vedere [Compute context options for R Server on HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md) (Opzioni del contesto di calcolo per R Server su HDInsight Premium).

## Rendere operativo un modello

Dopo aver completato la modellazione dei dati, è possibile mettere in funzione il modello per generare previsioni sui nuovi dati, sia in Azure sia in locale. Questo processo è noto come assegnazione di punteggi. Ecco alcuni esempi.

### Assegnare punteggi in HDInsight

Per assegnare un punteggio in HDInsight, scrivere una funzione R che chiama il modello al fine di eseguire previsioni su un nuovo file di dati caricato nell'account di archiviazione. Salvare quindi le previsioni sull'account di archiviazione. È possibile eseguire la routine su richiesta sul nodo perimetrale del cluster o usando un processo pianificato.

### Assegnare punteggi in Azure Machine Learning

Per assegnare un punteggio tramite un servizio Web di Azure Machine Learning, usare il pacchetto R open source di Azure Machine Learning noto come [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) per pubblicare il modello come servizio Web di Azure. Per comodità, questo pacchetto è già installato nel nodo perimetrale. Usare quindi le funzioni di Machine Learning per creare un'interfaccia utente per il servizio Web e poi richiamare il servizio Web quando necessario per l'assegnazione dei punteggi.

Se si sceglie questa opzione, sarà necessario convertire gli oggetti del modello ScaleR in oggetti equivalenti del modello open source per l'uso con il servizio Web. A tale scopo, è possibile usare le funzioni di coercizione di ScaleR, come `as.randomForest()` per i modelli basati su insiemi.


### Assegnare punteggi in locale

Per assegnare punteggi in locale dopo aver creato il modello, si potrebbe serializzare il modello in R, scaricarlo, deserializzarlo e poi usarlo per assegnare punteggi ai nuovi dati. È possibile assegnare punteggi ai nuovi dati usando l'approccio descritto in precedenza in [Assegnare punteggi in HDInsight](#scoring-in-hdinsight) o tramite [DeployR](https://deployr.revolutionanalytics.com/).

## Eseguire la manutenzione del cluster

### Installare e gestire i pacchetti R

Molti dei pacchetti R che si usano saranno necessari sul nodo perimetrale, in quanto la maggior parte degli script R sarà eseguita qui. Per installare altri pacchetti R nel nodo perimetrale è possibile usare il consueto metodo `install.packages()` in R.

Nella maggior parte dei casi non è necessario installare altri pacchetti R sui nodi dati se si usano solo routine della libreria ScaleR in tutto il cluster. Potrebbero invece essere necessari pacchetti aggiuntivi per supportare l'uso dell'esecuzione **rxExec** o **RxDataStep** nei nodi dati.

In questi casi i pacchetti aggiuntivi devono essere specificati tramite l'uso di un'azione script dopo la creazione del cluster. Per altre informazioni, vedere l'articolo relativo alla [creazione di un cluster HDInsight con R Server](hdinsight-hadoop-r-server-get-started.md).

### Modificare le impostazioni di memoria per Hadoop MapReduce

È possibile modificare un cluster per cambiare la quantità di memoria disponibile per R Server quando si esegue un processo MapReduce. A tale scopo, usare l'interfaccia utente Apache Ambari disponibile nel pannello del Portale di Azure per il cluster. Per le istruzioni su come accedere all'interfaccia utente Ambari per il cluster, vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md).

È anche possibile modificare la quantità di memoria disponibile per R Server tramite gli switch Hadoop nella chiamata di **RxHadoopMR**, come mostrato di seguito:

	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### Applicare la scalabilità al cluster

Un cluster esistente può essere dimensionato, in aumento o in riduzione, tramite il portale. Eseguendo la scalabilità è possibile ottenere funzionalità aggiuntive necessarie per le attività di elaborazione più consistenti oppure è possibile ridimensionare il cluster quando inutilizzato. Per istruzioni su come ridimensionare un cluster, vedere l'articolo relativo alla [gestione di cluster HDInsight](hdinsight-administer-use-portal-linux.md).

### Eseguire la manutenzione del sistema

La manutenzione viene eseguita sulle macchine virtuali Linux sottostanti di un cluster HDInsight durante gli orari di minore attività per applicare patch del sistema operativo e altri aggiornamenti. In genere, la manutenzione viene eseguita alle 3:30 AM (ora locale della macchina virtuale) ogni lunedì e giovedì. Gli aggiornamenti vengono eseguiti in modo tale da non compromettere più di un quarto del cluster per volta.

Poiché i nodi head sono ridondanti e non tutti i nodi dati sono influenzati dalla procedura, i processi che vengono eseguiti in questo intervallo di tempo potrebbero essere rallentati, ma comunque completati. Gli eventuali software personalizzati o i dati locali di cui si dispone vengono preservati durante tutti questi eventi di manutenzione, a meno che si verifichi un errore catastrofico che richiede la ricompilazione del cluster.

## Informazioni sulle opzioni IDE per R Server su un cluster HDInsight

Il nodo perimetrale Linux di un cluster HDInsight Premium è la destinazione delle analisi basate su R. Dopo la connessione al cluster, è possibile avviare la console di interfaccia di R Server digitando **R** al prompt dei comandi di Linux. L'uso della console di interfaccia è più semplice se si esegue un editor di testo per lo sviluppo di script R in un'altra finestra e si tagliano e incollano le sezioni dello script nella console R.

Uno strumento più avanzato per lo sviluppo di script R è l'IDE basato su R per l'uso su PC desktop, come [Strumenti R per Visual Studio](https://www.visualstudio.com/it-IT/features/rtvs-vs.aspx) (RTVS), recentemente annunciato da Microsoft. Si tratta di una famiglia di strumenti per desktop e server di [RStudio](https://www.rstudio.com/products/rstudio-server/). È anche possibile usare [StatET](http://www.walware.de/goto/statet), basato su Eclipse, di Walware.

Un'altra opzione è installare un'IDE sul nodo perimetrale stesso di Linux. Una scelta molto comune è [RStudio Server](https://www.rstudio.com/products/rstudio-server/) che offre un IDE basato sul browser per l'uso da parte di client remoti. L'installazione di RStudio Server nel nodo perimetrale di un cluster HDInsight Premium offre un'esperienza IDE completa per lo sviluppo e l'esecuzione di script R con R Server nel cluster. Questo metodo può risultare molto più produttivo rispetto all'uso della console di R predefinita. Per informazioni sull'uso di RStudio Server, vedere [Installazione di RStudio con R Server su HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## Informazioni sui prezzi

I prezzi associati a un cluster HDInsight Premium con Server R struttura sono strutturati in modo analogo ai prezzi relativi ai cluster HDInsight standard. Queste si basano sul dimensionamento delle macchine virtuali sottostanti sui nodi nome, dati e perimetrali, con una maggiorazione nelle ore di punta per Premium. Per altre informazioni sui prezzi di HDInsight Premium, inclusi i prezzi relativi alla fase di anteprima pubblica, e la disponibilità della versione di valutazione gratuita di 30 giorni, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Passaggi successivi

Per leggere altre informazioni sull'uso di R Server con i cluster HDInsight, seguire i collegamenti riportati di seguito.

- [Introduzione a R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Aggiungere RStudio Server a HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Opzioni di Archiviazione di Azure per R Server su HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0817_2016-->