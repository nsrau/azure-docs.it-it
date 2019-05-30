---
title: Introduzione a ML Services in Azure HDInsight
description: Informazioni su come usare ML Services in HDInsight per creare applicazioni per l'analisi di Big Data.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 6fecd2278ddad4de3a2cff9035d60083101e873e
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393846"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Novità di servizi di Machine Learning in Azure HDInsight

Microsoft Machine Learning Server è disponibile come opzione di distribuzione quando si creano i cluster HDInsight in Azure. Il tipo di cluster che offre questa opzione è denominato **ML Services**. Questa funzionalità offre ai data scientist, agli statistici e ai programmatori in R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

ML Services su HDInsight offre le più recenti funzioni di analisi basate su R per set di dati praticamente di qualsiasi dimensione caricati in Archiviazione BLOB di Azure o in Azure Data Lake Storage. Poiché il cluster ML Services si basa su R open source, le applicazioni basate su R compilate dall'utente possono sfruttare gli oltre 8000 pacchetti R open source. Sono disponibili anche le routine di ScaleR, il pacchetto di analisi di Big Data di Microsoft.

Il nodo perimetrale di un cluster offre una posizione pratica per connettersi al cluster ed eseguire gli script R. Con un nodo perimetrale è possibile eseguire le funzioni distribuite parallelizzate di ScaleR nei core del server del nodo perimetrale. È anche possibile eseguire tali funzioni tra i nodi del cluster usando contesti di calcolo Hadoop MapReduce o Apache Spark di ScaleR.

I modelli o le previsioni che risultano dalle analisi possono essere scaricate per l'uso locale o impiegate altrove in Azure, in particolare tramite il [servizio Web](../../machine-learning/studio/publish-a-machine-learning-web-service.md) di [Azure Machine Learning Studio](https://studio.azureml.net).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Introduzione all'uso di ML Services in HDInsight

Per creare un cluster ML Services in Azure HDInsight, selezionare il tipo di cluster **ML Services** quando si crea un cluster HDInsight usando il portale di Azure. Il tipo di cluster ML Services include ML Server sui nodi dati del cluster e su un nodo perimetrale, che funge da destinazione per le analisi basate su ML Services. Per la procedura guidata di creazione del cluster, vedere [Introduzione all'uso di ML Services in HDInsight](r-server-get-started.md).

## <a name="why-choose-ml-services-in-hdinsight"></a>Perché scegliere ML Services in HDInsight?

ML Services in HDInsight offre i vantaggi seguenti:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Le opzioni di intelligenza artificiale innovative Microsoft e open source

  ML Services include un set distribuito altamente scalabile di algoritmi, ad esempio [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) e [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package), in grado di operare su dimensioni di dati più grandi rispetto a quelle della memoria fisica ed eseguiti in un'ampia gamma di piattaforme in modalità distribuita. Altre informazioni sulla raccolta di [pacchetti R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) e [pacchetti Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) personalizzata da Microsoft inclusa nel prodotto.
  
  ML Services offre queste innovazioni Microsoft e i contributi della community open source, ovvero R, Python e i toolkit di intelligenza artificiale, su un'unica piattaforma aziendale. Qualsiasi pacchetto per l'apprendimento automatico open source Python o R può operare in affiancamento alle innovative opzioni proprietarie Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Funzionalità di amministrazione e operazionalizzazione semplici, sicure e su vasta scala

  Le aziende che fanno affidamento su ambienti e paradigmi tradizionali investono molto tempo ed energie nell'operazionalizzazione. Questo comporta aumenti dei costi e ritardi, che includono il tempo di conversione per i modelli, le iterazioni per mantenerli validi e aggiornati, l'approvazione dei requisiti normativi e la gestione delle autorizzazioni tramite l'operazionalizzazione.

  ML Services offre opzioni di [operazionalizzazione](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) di livello aziendale, dal momento che, dopo il completamento di un modello di apprendimento automatico, sono sufficienti pochi clic per generare le API di servizi Web. Questi [servizi Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) sono ospitati in una griglia di server nel cloud e possono essere integrati con applicazioni line-of-business. La distribuzione in una griglia elastica consente una perfetta scalabilità con le esigenze aziendali, per l'assegnazione dei punteggi in batch e in tempo reale. Per istruzioni, vedere [Rendere operativo un cluster ML Services in HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Funzionalità principali di ML Services su HDInsight

Le funzionalità seguenti sono incluse in ML Services su HDInsight.

| Categoria funzionalità | Descrizione |
|------------------|-------------|
| Abilitazione per R | [Pacchetti R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) per soluzioni scritte in R, con una distribuzione open source di R e un'infrastruttura di runtime per l'esecuzione di script. |
| Abilitazione per Python | [Moduli Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) per soluzioni scritte in Python, con una distribuzione open source di Python e un'infrastruttura di runtime per l'esecuzione di script.
| [Modelli con training preliminare](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Per analisi visiva e analisi del sentiment in messaggi di testo, idonei all'assegnazione dei punteggi ai dati immessi. |
| [Distribuzione e utilizzo](r-server-operationalize.md) | Rendere operativo il server e distribuire soluzioni come servizio Web. |
| [Esecuzione remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Iniziare sessioni remote sul cluster ML Services nella rete dalla propria workstation client. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opzioni di archiviazione dei dati per ML Services su HDInsight

La risorsa di archiviazione predefinita per il file system HDFS dei cluster HDInsight può essere associata a un account di Archiviazione di Azure o ad Azure Data Lake Storage. Questa associazione assicura che i dati caricati nella risorsa di archiviazione del cluster durante l'analisi siano resi persistenti e che i dati siano disponibili anche dopo l'eliminazione del cluster. Sono disponibili vari strumenti per gestire il trasferimento dei dati alla risorsa di archiviazione scelta, inclusi lo strumento di caricamento basato sul portale dell'account di archiviazione e l'utilità [AzCopy](../../storage/common/storage-use-azcopy.md).

Indipendentemente dall'opzione di archiviazione primaria in uso, è possibile abilitare l'accesso ad altri archivi BLOB o di Data Lake durante il processo di provisioning del cluster. Vedere [Introduzione all'uso di ML Services in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) per informazioni sull'aggiunta di accesso agli account aggiuntivi. Per altre informazioni sull'uso di più account di archiviazione, vedere l'articolo [Opzioni di Archiviazione di Azure per ML Services su HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage).

È anche possibile ricorrere a [File di Azure](../../storage/files/storage-how-to-use-files-linux.md) come opzione di archiviazione per l'uso sul nodo perimetrale. File di Azure consente di montare una condivisione file creata nell'Archiviazione di Azure sul file system Linux. Per altre informazioni su queste opzioni di archiviazione dei dati per ML Services su cluster HDInsight, vedere [Opzioni di Archiviazione di Azure per ML Services su HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Accedere al nodo perimetrale di ML Services

È possibile connettersi a Microsoft ML Server sul nodo perimetrale tramite un browser. Viene installato per impostazione predefinita durante la creazione del cluster. Per altre informazioni, vedere [Introduzione all'uso di ML Services in HDInsight](r-server-get-started.md). Per accedere alla console di R, è anche possibile connettersi al nodo perimetrale del cluster dalla riga di comando tramite SSH/PuTTY.

## <a name="develop-and-run-r-scripts"></a>Sviluppare ed eseguire script R

Gli script R che vengono creati ed eseguiti possono sfruttare gli oltre 8000 pacchetti R open source disponibili nonché le routine parallelizzate e distribuite disponibili nella libreria ScaleR. In generale, uno script eseguito con ML Services sul nodo perimetrale viene eseguito all'interno dell'interprete R su tale nodo. Sono esclusi i passaggi che devono richiamare una delle funzioni ScaleR con un contesto di calcolo impostato su Hadoop MapReduce (RxHadoopMR) o Spark (RxSpark). In questo caso la funzione viene eseguita in modo distribuito su tutti i nodi dati (attività) del cluster associati con i dati a cui si fa riferimento. Per altre informazioni sulle diverse opzioni relative al contesto di calcolo, vedere [Opzioni del contesto di calcolo per ML Services su HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Rendere operativo un modello

Dopo aver completato la modellazione dei dati, è possibile rendere operativo il modello per generare previsioni sui nuovi dati, sia in Azure sia in locale. Questo processo è noto come assegnazione di punteggi. L'assegnazione dei punteggi può essere eseguita in HDInsight, in Azure Machine Learning o in locale.

### <a name="score-in-hdinsight"></a>Assegnare punteggi in HDInsight

Per assegnare un punteggio in HDInsight, scrivere una funzione R che chiama il modello al fine di eseguire previsioni su un nuovo file di dati caricato nell'account di archiviazione. Salvare quindi le previsioni sull'account di archiviazione. È possibile eseguire la routine su richiesta sul nodo perimetrale del cluster o usando un processo pianificato.

### <a name="score-in-azure-machine-learning-aml"></a>Assegnare punteggi in Azure Machine Learning (AML)

Per assegnare punteggi tramite Azure Machine Learning, usare il pacchetto R open source di Azure Machine Learning noto come [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) per pubblicare il modello come servizio Web di Azure. Per comodità, questo pacchetto è già installato nel nodo perimetrale. Usare quindi le funzioni di Azure Machine Learning per creare un'interfaccia utente per il servizio Web e poi richiamare il servizio Web quando necessario per l'assegnazione dei punteggi.

Se si sceglie questa opzione, è necessario convertire gli oggetti del modello ScaleR in oggetti equivalenti del modello open source per l'uso con il servizio Web. Per tale conversione usare le funzioni di coercizione di ScaleR, come `as.randomForest()` per i modelli basati su insiemi.

### <a name="score-on-premises"></a>Assegnare punteggi in locale

Per assegnare punteggi in locale dopo aver creato il modello, si potrebbe serializzare il modello in R, scaricarlo, deserializzarlo e poi usarlo per assegnare punteggi ai nuovi dati. È possibile assegnare punteggi ai nuovi dati usando l'approccio descritto in precedenza in [Assegnare punteggi in HDInsight](#score-in-hdinsight) o tramite i [servizi Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Eseguire la manutenzione del cluster

### <a name="install-and-maintain-r-packages"></a>Installare e gestire i pacchetti R

Molti dei pacchetti R usati sono necessari sul nodo perimetrale, in quanto la maggior parte dei passaggi degli script R viene eseguita qui. Per installare altri pacchetti R nel nodo perimetrale è possibile usare il metodo `install.packages()` in R.

Di norma non è necessario installare altri pacchetti R sui nodi dati se si usano solo routine della libreria ScaleR in tutto il cluster. Potrebbero invece essere necessari pacchetti aggiuntivi per supportare l'uso dell'esecuzione **rxExec** o **RxDataStep** nei nodi dati.

In questi casi, i pacchetti aggiuntivi possono essere installati con un'azione script dopo la creazione del cluster. Per altre informazioni, vedere [Gestire ML Services nel cluster HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Modificare le impostazioni di memoria per Apache Hadoop MapReduce

È possibile modificare un cluster per cambiare la quantità di memoria disponibile per ML Services quando si esegue un processo MapReduce. A tale scopo, usare l'interfaccia utente Apache Ambari disponibile nel pannello del Portale di Azure per il cluster. Per le istruzioni su come accedere all'interfaccia utente Ambari per il cluster, vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](../hdinsight-hadoop-manage-ambari.md).

È anche possibile modificare la quantità di memoria disponibile per ML Services tramite gli switch Hadoop nella chiamata di **RxHadoopMR**, come illustrato di seguito:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Applicare la scalabilità al cluster

È possibile aumentare o ridurre le prestazioni di un cluster ML Services esistente in HDInsight tramite il portale. Aumentando le prestazioni è possibile ottenere funzionalità aggiuntive necessarie per le attività di elaborazione più consistenti oppure è possibile ridimensionare il cluster quando inutilizzato. Per istruzioni su come ridimensionare un cluster, vedere l'articolo relativo alla [gestione di cluster HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Eseguire la manutenzione del sistema

La manutenzione viene eseguita sulle macchine virtuali Linux sottostanti di un cluster HDInsight durante gli orari di minore attività per applicare patch del sistema operativo e altri aggiornamenti. In genere, la manutenzione viene eseguita alle 3:30 AM (ora locale della macchina virtuale) ogni lunedì e giovedì. Gli aggiornamenti vengono eseguiti in modo tale da non compromettere più di un quarto del cluster per volta.

Poiché i nodi head sono ridondanti e non tutti i nodi dati sono influenzati dalla procedura, i processi che vengono eseguiti in questo intervallo di tempo potrebbero essere rallentati, ma comunque completati. Gli eventuali software personalizzati o i dati locali di cui si dispone vengono preservati durante tutti questi eventi di manutenzione, a meno che si verifichi un errore catastrofico che richiede la ricompilazione del cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opzioni IDE per ML Services su HDInsight

Il nodo perimetrale Linux di un cluster HDInsight è la destinazione delle analisi basate su R. Le versioni recenti di HDInsight includono un'installazione predefinita di RStudio Server nel nodo perimetrale come ambiente IDE basato su browser. L'uso di RStudio Server come IDE per lo sviluppo e l'esecuzione di script R può essere notevolmente più produttivo rispetto all'uso esclusivo della console di R.

Inoltre, è possibile installare un IDE desktop e usarlo per accedere al cluster tramite l'uso di un contesto di calcolo remoto MapReduce o Spark. Le opzioni includono [R Tools per Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) Microsoft, RStudio e [StatET](http://www.walware.de/goto/statet) Walware basato su Eclipse.

In aggiunta è possibile accedere alla console di R nel nodo perimetrale digitando **R** nel prompt dei comandi di Linux dopo aver stabilito la connessione tramite SSH o PuTTY. L'uso della console di interfaccia è più semplice se si esegue un editor di testo per lo sviluppo di script R in un'altra finestra e si tagliano e incollano le sezioni dello script nella console R.

## <a name="pricing"></a>Prezzi

I prezzi associati a un cluster HDInsight di ML Services sono strutturati in modo analogo ai prezzi per altri tipi di cluster HDInsight. Sono basati sulle dimensioni delle VM sottostanti in nodi nome, dati e perimetrali, con una maggiorazione per le ore core. Per altre informazioni, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di ML Services nei cluster HDInsight, vedere gli argomenti seguenti:

* [Introduzione all'uso di cluster ML Services in HDInsight](r-server-get-started.md)
* [Opzioni del contesto di calcolo per un cluster ML Services su HDInsight](r-server-compute-contexts.md)
* [Opzioni di archiviazione per un cluster ML Services su HDInsight](r-server-storage.md)
