---
title: Introduzione a R Server in HDInsight | Documentazione Microsoft
description: Informazioni introduttive a R Server in HDInsight. Informazioni su come usare R Server in HDInsight per creare applicazioni per l&quot;analisi dei Big Data.
services: hdinsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: cd57f3a43142b3af3546eafd9749123fadd333c2
ms.openlocfilehash: 2f5cf1155f116b22fa28cf86bb8c87667df981b7
ms.lasthandoff: 01/14/2017


---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introduzione a R Server e alle funzionalità R open source in HDInsight

Microsoft R Server è ora disponibile come opzione in Microsoft Azure HDInsight quando si creano cluster HDInsight in Azure. Questa nuova capacità offre ai data scientist, agli statistici e ai programmatori R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight.

I cluster possono essere dimensionati in base ai progetti e alle attività ed eliminati quando non servono più. Essendo parte di Azure HDInsight, questi cluster includono il supporto 24/7 a livello di organizzazione, un contratto di servizio che prevede tempi di attività pari al 99,9% e la flessibilità di integrazione con altri componenti dell'ecosistema Azure.

R Server su HDInsight offre le più recenti funzioni di analisi basate su R per set di dati praticamente di qualsiasi dimensione caricati in Archiviazione BLOB di Azure o Azure Data Lake. Dato che R Server si basa su R open source, le applicazioni basate su R che vengono compilate possono sfruttare gli oltre 8000 pacchetti R open source disponibili, nonché le routine in ScaleR, il pacchetto di analisi dei Big Data di Microsoft incluso in R Server.

Il nodo perimetrale di un cluster offre una posizione pratica per connettersi al cluster ed eseguire gli script R. Con un nodo perimetrale è possibile eseguire funzioni distribuite parallelizzate di ScaleR nei core del server del nodo perimetrale. È anche possibile eseguire tali funzioni tra i nodi del cluster usando contesti di calcolo Hadoop MapReduce o Spark di ScaleR.

I modelli o le previsioni che risultano dalle analisi possono essere scaricate per l'uso locale o impiegate altrove in Azure, ad esempio tramite un [servizio Web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md) di [Azure Machine Learning Studio](http://studio.azureml.net).

## <a name="get-started-with-r-on-hdinsight"></a>Introduzione all'uso di R su HDInsight
Per includere R Server in un cluster HDInsight, è necessario selezionare il tipo di cluster R Server quando si crea un cluster HDInsight usando il portale di Azure. Il tipo di cluster R Server include R Server sui nodi dati del cluster e su un nodo perimetrale come destinazione per le analisi basate su R Server. Per la procedura dettagliata di creazione di un cluster, vedere [Introduzione all'uso di R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md) .

## <a name="learn-about-data-storage-options"></a>Informazioni sulle opzioni di archiviazione dei dati
La risorsa di archiviazione predefinita per il file system HDFS dei cluster HDInsight può essere associata a un account di Archiviazione di Azure o un archivio di Azure Data Lake. Ciò assicura che i dati caricati nella risorsa di archiviazione del cluster durante l'analisi siano resi persistenti. Sono disponibili vari strumenti per trasferire i dati alla risorsa di archiviazione scelta, inclusi lo strumento di caricamento basato sul portale dell'account di archiviazione e l'utilità [AzCopy](../storage/storage-use-azcopy.md).

Indipendentemente dal fatto che si scelga BLOB di Azure o Data Lake come risorsa di archiviazione primaria per il cluster, è possibile aggiungere l'accesso a ulteriori archivi BLOB o di Data Lake durante il processo di provisioning del cluster. Vedere [Introduzione all'uso di R Server su HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-get-started) per informazioni sull'aggiunta dell'accesso ad altri account e l'articolo correlato [Opzioni di Archiviazione di Azure per R Server in HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-storage) per informazioni sull'uso di più account di archiviazione.

È anche possibile ricorrere a [File di Azure](../storage/storage-how-to-use-files-linux.md) come opzione di archiviazione per l'uso sul nodo perimetrale. File di Azure consente di montare una condivisione file creata nell'Archiviazione di Azure sul file system Linux. Per altre informazioni sulle opzioni di archiviazione dei dati per R Server su cluster HDInsight, vedere [Azure Storage options for R Server on HDInsight clusters](hdinsight-hadoop-r-server-storage.md)(Opzioni di Archiviazione di Azure per R Server su cluster HDInsight).

## <a name="access-r-server-on-the-cluster"></a>Accedere a R Server sul cluster
Dopo aver creato un cluster con R Server, è possibile connettersi a R Server sul nodo perimetrale tramite un browser se si è scelto di includere RStudio Server durante il processo di provisioning (o è stato aggiunto in seguito) oppure usando SSH/PuTTY per accedere alla console di R. Per altre informazioni sull'installazione di RStudio Server dopo la creazione di un cluster, vedere [Installing RStudio Server on HDInsight clusters](hdinsight-hadoop-r-server-install-r-studio.md) (Installazione di RStudio Server in cluster HDInsight).   

## <a name="develop-and-run-r-scripts"></a>Sviluppare ed eseguire script R
Gli script R che vengono creati ed eseguiti possono sfruttare gli oltre 8000 pacchetti R open source disponibili nonché le routine parallelizzate e distribuite della libreria ScaleR. In generale, uno script eseguito con R Server sul nodo perimetrale viene eseguito all'interno dell'interprete R su tale nodo. Sono esclusi i passaggi che richiamano una delle funzioni ScaleR con un contesto di calcolo impostato su Hadoop MapReduce (RxHadoopMR) o Spark (RxSpark).

In questi casi la funzione viene eseguita in modo distribuito su tutti i nodi dati (attività) del cluster associati con i dati a cui si fa riferimento. Per altre informazioni sulle diverse opzioni relative al contesto di calcolo, vedere l'articolo sulle [opzioni del contesto di calcolo per R Server in HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Rendere operativo un modello
Dopo aver completato la modellazione dei dati, è possibile mettere in funzione il modello per generare previsioni sui nuovi dati, sia in Azure sia in locale. Questo processo è noto come assegnazione di punteggi. Ecco alcuni esempi.

### <a name="score-in-hdinsight"></a>Assegnare punteggi in HDInsight
Per assegnare un punteggio in HDInsight, scrivere una funzione R che chiama il modello al fine di eseguire previsioni su un nuovo file di dati caricato nell'account di archiviazione. Salvare quindi le previsioni sull'account di archiviazione. È possibile eseguire la routine su richiesta sul nodo perimetrale del cluster o usando un processo pianificato.  

### <a name="score-in-azure-machine-learning"></a>Assegnare punteggi in Azure Machine Learning
Per assegnare un punteggio tramite un servizio Web di Azure Machine Learning, usare il pacchetto R open source di Azure Machine Learning noto come [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) per pubblicare il modello come servizio Web di Azure. Per comodità, questo pacchetto è già installato nel nodo perimetrale. Usare quindi le funzioni di Machine Learning per creare un'interfaccia utente per il servizio Web e poi richiamare il servizio Web quando necessario per l'assegnazione dei punteggi.

Se si sceglie questa opzione, sarà necessario convertire gli oggetti del modello ScaleR in oggetti equivalenti del modello open source per l'uso con il servizio Web. A tale scopo, è possibile usare le funzioni di coercizione di ScaleR, come `as.randomForest()` per i modelli basati su insiemi.

### <a name="score-on-premises"></a>Assegnare punteggi in locale
Per assegnare punteggi in locale dopo aver creato il modello, si potrebbe serializzare il modello in R, scaricarlo, deserializzarlo e poi usarlo per assegnare punteggi ai nuovi dati. È possibile assegnare punteggi ai nuovi dati usando l'approccio descritto in precedenza in [Assegnare punteggi in HDInsight](#scoring-in-hdinsight) o con [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Eseguire la manutenzione del cluster
### <a name="install-and-maintain-r-packages"></a>Installare e gestire i pacchetti R
Molti dei pacchetti R che si usano saranno necessari sul nodo perimetrale, in quanto la maggior parte dei passaggi degli script R sarà eseguita qui. Per installare altri pacchetti R nel nodo perimetrale è possibile usare il consueto metodo `install.packages()` in R.

Nella maggior parte dei casi non sarà necessario installare altri pacchetti R sui nodi dati se si usano solo routine della libreria ScaleR in tutto il cluster. Potrebbero invece essere necessari pacchetti aggiuntivi per supportare l'uso dell'esecuzione **rxExec** o **RxDataStep** nei nodi dati.

In questi casi, i pacchetti aggiuntivi possono essere installati usando un'azione script dopo la creazione del cluster. Per altre informazioni, vedere l'articolo relativo alla [creazione di un cluster HDInsight con R Server](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Modificare le impostazioni di memoria per Hadoop MapReduce
È possibile modificare un cluster per cambiare la quantità di memoria disponibile per R Server quando si esegue un processo MapReduce. A tale scopo, usare l'interfaccia utente Apache Ambari disponibile nel pannello del Portale di Azure per il cluster. Per le istruzioni su come accedere all'interfaccia utente Ambari per il cluster, vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md).

È anche possibile modificare la quantità di memoria disponibile per R Server tramite gli switch Hadoop nella chiamata di **RxHadoopMR** , come mostrato di seguito:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Applicare la scalabilità al cluster
Un cluster esistente può essere dimensionato, in aumento o in riduzione, tramite il portale. Eseguendo la scalabilità è possibile ottenere funzionalità aggiuntive necessarie per le attività di elaborazione più consistenti oppure è possibile ridimensionare il cluster quando inutilizzato. Per istruzioni su come ridimensionare un cluster, vedere l'articolo relativo alla [gestione di cluster HDInsight](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Eseguire la manutenzione del sistema
La manutenzione viene eseguita sulle macchine virtuali Linux sottostanti di un cluster HDInsight durante gli orari di minore attività per applicare patch del sistema operativo e altri aggiornamenti. In genere, la manutenzione viene eseguita alle 3:30 AM (ora locale della macchina virtuale) ogni lunedì e giovedì. Gli aggiornamenti vengono eseguiti in modo tale da non compromettere più di un quarto del cluster per volta.  

Poiché i nodi head sono ridondanti e non tutti i nodi dati sono influenzati dalla procedura, i processi che vengono eseguiti in questo intervallo di tempo potrebbero essere rallentati, ma comunque completati. Gli eventuali software personalizzati o i dati locali di cui si dispone vengono preservati durante tutti questi eventi di manutenzione, a meno che si verifichi un errore catastrofico che richiede la ricompilazione del cluster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Informazioni sulle opzioni IDE per R Server su un cluster HDInsight
Il nodo perimetrale Linux di un cluster HDInsight è la destinazione delle analisi basate su R.  Le versioni recenti di HDInsight includono un'opzione predefinita per l'installazione della versione Community di [RStudio Server](https://www.rstudio.com/products/rstudio-server/) sul nodo perimetrale come ambiente IDE basato su browser. L'uso di RStudio Server come IDE per lo sviluppo e l'esecuzione di script R può essere notevolmente più produttivo rispetto all'uso esclusivo della console di R. Se si sceglie di non aggiungere RStudio Server durante la creazione del cluster, ma si desidera aggiungerlo in seguito, vedere [Installazione di RStudio con R Server su HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-install-r-studio).

Un'altra opzione per avere a disposizione un ambiente IDE completo consiste nell'installare un IDE desktop, come gli strumenti annunciati da Microsoft di recente [R Tools for Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS), RStudio o lo strumento [StatET](http://www.walware.de/goto/statet) basato su Eclipse di Walware e quindi accedere al cluster usando un contesto di calcolo remoto MapReduce o Spark.  

Infine, è possibile accedere semplicemente alla console di R Server nel nodo perimetrale digitando **R** al prompt dei comandi di Linux dopo aver stabilito la connessione tramite SSH o PuTY. L'uso della console di interfaccia è più semplice se si esegue un editor di testo per lo sviluppo di script R in un'altra finestra e si tagliano e incollano le sezioni dello script nella console R.

## <a name="learn-about-pricing"></a>Informazioni sui prezzi
I prezzi associati a un cluster HDInsight con R Server sono strutturati in modo analogo ai prezzi per i cluster HDInsight standard. Sono basati sulle dimensioni delle VM sottostanti in nodi nome, dati e perimetrali, con una maggiorazione per le ore core. Per altre informazioni sui prezzi di HDInsight e sulla disponibilità della versione di valutazione gratuita di 30 giorni, vedere [HDInsight Prezzi](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passaggi successivi
Per leggere altre informazioni sull'uso di R Server con i cluster HDInsight, seguire i collegamenti riportati di seguito.

* [Introduzione a R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md)
* [Aggiungere RStudio Server a HDInsight (se non è stato installato durante la creazione del cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opzioni di Archiviazione di Azure per R Server su HDInsight](hdinsight-hadoop-r-server-storage.md)

