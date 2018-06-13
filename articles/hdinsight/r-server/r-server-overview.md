---
title: Introduzione a R Server in HDInsight | Documentazione Microsoft
description: Informazioni su come usare R Server in HDInsight per creare applicazioni per l'analisi dei Big Data.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 19c286db9a8a2aa537badc83d98a1b74b73e9873
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414719"
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introduzione a R Server e alle funzionalità R open source in HDInsight

Microsoft R Server (noto anche come Microsoft Machine Learning Server) è disponibile come opzione di distribuzione quando si creano cluster HDInsight in Azure. Il tipo di cluster che fornisce questa opzione è denominato **R Server**. Questa funzionalità offre ai data scientist, agli statistici e ai programmatori in R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

R Server su HDInsight offre le più recenti funzioni di analisi basate su R per set di dati praticamente di qualsiasi dimensione caricati in Archiviazione BLOB di Azure o Azure Data Lake. Poiché il cluster R Server si basa su R open source, le applicazioni basate su R compilate dall'utente possono sfruttare gli oltre 8000 pacchetti R open source. Sono disponibili anche le routine di ScaleR, il pacchetto di analisi dei Big Data di Microsoft incluso in R Server.

Il nodo perimetrale di un cluster offre una posizione pratica per connettersi al cluster ed eseguire gli script R. Con un nodo perimetrale è possibile eseguire le funzioni distribuite parallelizzate di ScaleR nei core del server del nodo perimetrale. È anche possibile eseguire tali funzioni tra i nodi del cluster usando contesti di calcolo Hadoop MapReduce o Spark di ScaleR.

I modelli o le previsioni che risultano dalle analisi possono essere scaricate per l'uso locale o impiegate altrove in Azure, in particolare tramite il [servizio Web](../../machine-learning/studio/publish-a-machine-learning-web-service.md) di [Azure Machine Learning Studio](http://studio.azureml.net).

## <a name="get-started-with-r-server-on-hdinsight"></a>Introduzione a R Server in HDInsight

Per creare un cluster R Server in Azure HDInsight, selezionare il tipo di cluster **R Server** quando si crea un cluster HDInsight usando il portale di Azure. Il tipo di cluster R Server include R Server sui nodi dati del cluster e su un nodo perimetrale, che funge da destinazione per le analisi basate su R Server. Per la procedura di creazione di un cluster, vedere [Introduzione all'uso di R Server su HDInsight](r-server-get-started.md) .

## <a name="why-choose-r-server-in-hdinsight"></a>Perché scegliere R Server in HDInsight?

R Server in HDInsight offre la possibilità di usare R Server in Azure. R Server include:

+ **Le opzioni di intelligenza artificiale più innovative Microsoft e open source**

  Microsoft è impegnata a rendere accessibili a ogni utente e organizzazione opzioni di intelligenza artificiale. R Server include un set completo di algoritmi altamente scalabili e distribuiti, ad esempio [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) e [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package), in grado di operare su dimensioni di dati più ampie rispetto a quelle della memoria fisica ed eseguiti in un'ampia gamma di piattaforme in modalità distribuita. Altre informazioni sulla raccolta di [pacchetti R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) e [pacchetti Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) personalizzata da Microsoft inclusa nel prodotto.
  
  R Server offre queste innovazioni Microsoft e quelle della community open source (R, Python e i toolkit di intelligenza artificiale) su un'unica piattaforma aziendale. Qualsiasi pacchetto per l'apprendimento automatico open source Python o R può operare in affiancamento alle innovative opzioni proprietarie Microsoft. 

+ **Funzionalità di gestione e operazionalizzazione semplici, sicure e su vasta scala**

  Le aziende che fanno affidamento su ambienti e paradigmi di operazionalizzazione di tipo tradizionale finiscono con l'investire molto tempo ed energie in quest'area. I punti deboli di questo approccio, che spesso comportano aumenti dei costi e ritardi, includono: il tempo di conversione per i modelli, le iterazioni per mantenerli validi e aggiornati, l'approvazione dei requisiti normativi e la gestione delle autorizzazioni tramite l'operazionalizzazione.

  R Server offre le migliori opzioni di [operazionalizzazione](https://docs.microsoft.com/machine-learning-server/what-is-operationalization): dal momento in cui un modello di apprendimento automatico viene completato, sono sufficienti pochi clic per generare le API di servizi Web. Questi [servizi Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) sono ospitati in una griglia di server in locale o nel cloud e possono essere integrati con applicazioni line-of-business. La distribuzione in una griglia elastica consente una perfetta scalabilità con le esigenze aziendali, per l'assegnazione dei punteggi in batch e in tempo reale. Per istruzioni, vedere [Operationalize R Server on HDInsight](r-server-operationalize.md) (Rendere operativo R Server in HDInsight).

+ **Interventi all'intero ecosistema per garantire la soddisfazione dei clienti con un costo totale di proprietà ottimale**

  Per intraprendere un percorso volto a rendere intelligenti le proprie applicazioni o semplicemente per conoscere il nuovo mondo dell'intelligenza artificiale e dell'apprendimento automatico, sono necessarie le risorse appropriate. Oltre a questa documentazione, Microsoft fornisce varie risorse di apprendimento e ha impiegato numerosi partner per la formazione per consentire ai clienti di ampliare le proprie conoscenze e aumentare rapidamente la produttività.


## <a name="key-features-of-r-server-on-hdinsight"></a>Funzionalità principali di R Server e HDInsight

Le funzionalità seguenti sono incluse in R Server su HDInsight.

| Categoria funzionalità | DESCRIZIONE |
|------------------|-------------|
| Abilitazione per R | [Pacchetti R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) per soluzioni scritte in R, con una distribuzione open source di R e un'infrastruttura di runtime per l'esecuzione di script. |
| Abilitazione per Python | [Moduli Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) per soluzioni scritte in Python, con una distribuzione open source di Python e un'infrastruttura di runtime per l'esecuzione di script.  
| [Modelli con training preliminare](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Per analisi visiva e analisi del sentiment in messaggi di testo, idonei all'assegnazione dei punteggi ai dati immessi. |
| [Distribuzione e utilizzo](r-server-operationalize.md) | Rendere operativo il server e distribuire soluzioni come servizio Web. |
| [Esecuzione remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-server-or-client) | Iniziare sessioni remote su R Server nella rete dalla propria workstation client. |


## <a name="data-storage-options-for-r-server-on-hdinsight"></a>Opzioni di archiviazione di dati per R Server su HDInsight

La risorsa di archiviazione predefinita per il file system HDFS dei cluster HDInsight può essere associata a un account di Archiviazione di Azure o ad Azure Data Lake Store. Questa associazione assicura che i dati caricati nella risorsa di archiviazione del cluster durante l'analisi siano resi persistenti e che i dati siano disponibili anche dopo l'eliminazione del cluster. Sono disponibili vari strumenti per gestire il trasferimento dei dati alla risorsa di archiviazione scelta, inclusi lo strumento di caricamento basato sul portale dell'account di archiviazione e l'utilità [AzCopy](../../storage/common/storage-use-azcopy.md).

Indipendentemente dall'opzione di archiviazione primaria in uso, è possibile aggiungere l'accesso ad altri archivi BLOB o di Data Lake durante il processo di provisioning del cluster. Vedere [Introduzione a R Server su HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) per informazioni sull'aggiunta di accesso per gli account aggiuntivi. Per altre informazioni sull'uso di più account di archiviazione, vedere l'articolo supplementare [Opzioni di Archiviazione di Azure per R Server su HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage).

È anche possibile ricorrere a [File di Azure](../../storage/files/storage-how-to-use-files-linux.md) come opzione di archiviazione per l'uso sul nodo perimetrale. File di Azure consente di montare una condivisione file creata nell'Archiviazione di Azure sul file system Linux. Per altre informazioni su queste opzioni di archiviazione dei dati per R Server su cluster HDInsight, vedere [Soluzioni di Archiviazione di Azure per R Server su HDInsight](r-server-storage.md).

## <a name="access-machine-learning-server-on-the-cluster"></a>Accedere a Machine Learning Server nel cluster

È possibile connettersi a Microsoft Machine Learning Server nel nodo perimetrale tramite un browser. Viene installato per impostazione predefinita durante la creazione del cluster. Per altre informazioni, vedere [Introduzione all'uso di R Server su HDInsight](r-server-get-started.md). Per accedere alla console di R, è anche possibile connettersi a ML Server dalla riga di comando tramite SSH/PuTTY. 

## <a name="develop-and-run-r-scripts"></a>Sviluppare ed eseguire script R

Gli script R che vengono creati ed eseguiti possono sfruttare gli oltre 8000 pacchetti R open source disponibili nonché le routine parallelizzate e distribuite disponibili nella libreria ScaleR. In generale, uno script eseguito con R Server sul nodo perimetrale viene eseguito all'interno dell'interprete R su tale nodo. Sono esclusi i passaggi che devono richiamare una delle funzioni ScaleR con un contesto di calcolo impostato su Hadoop MapReduce (RxHadoopMR) o Spark (RxSpark). In questo caso la funzione viene eseguita in modo distribuito su tutti i nodi dati (attività) del cluster associati con i dati a cui si fa riferimento. Per altre informazioni sulle diverse opzioni relative al contesto di calcolo, vedere l'articolo sulle [opzioni del contesto di calcolo per R Server in HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Rendere operativo un modello

Dopo aver completato la modellazione dei dati, è possibile mettere in funzione il modello per generare previsioni sui nuovi dati, sia in Azure sia in locale. Questo processo è noto come assegnazione di punteggi. L'assegnazione dei punteggi può essere eseguita in HDInsight, in Azure Machine Learning o in locale.

### <a name="score-in-hdinsight"></a>Assegnare punteggi in HDInsight
Per assegnare un punteggio in HDInsight, scrivere una funzione R che chiama il modello al fine di eseguire previsioni su un nuovo file di dati caricato nell'account di archiviazione. Salvare quindi le previsioni sull'account di archiviazione. È possibile eseguire la routine su richiesta sul nodo perimetrale del cluster o usando un processo pianificato.

### <a name="score-in-azure-machine-learning-aml"></a>Assegnare punteggi in Azure Machine Learning (AML)
Per assegnare punteggi tramite Azure Machine Learning, usare il pacchetto R open source di Azure Machine Learning noto come [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) per pubblicare il modello come servizio Web di Azure. Per comodità, questo pacchetto è già installato nel nodo perimetrale. Usare quindi le funzioni di Azure Machine Learning per creare un'interfaccia utente per il servizio Web e poi richiamare il servizio Web quando necessario per l'assegnazione dei punteggi.

Se si sceglie questa opzione, è necessario convertire gli oggetti del modello ScaleR in oggetti equivalenti del modello open source per l'uso con il servizio Web. Per tale conversione usare le funzioni di coercizione di ScaleR, come `as.randomForest()` per i modelli basati su insiemi.

### <a name="score-on-premises"></a>Assegnare punteggi in locale
Per assegnare punteggi in locale dopo aver creato il modello, si potrebbe serializzare il modello in R, scaricarlo, deserializzarlo e poi usarlo per assegnare punteggi ai nuovi dati. È possibile assegnare punteggi ai nuovi dati usando l'approccio descritto in precedenza in [Assegnare punteggi in HDInsight](#scoring-in-hdinsight) o con [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Eseguire la manutenzione del cluster

### <a name="install-and-maintain-r-packages"></a>Installare e gestire i pacchetti R

Molti dei pacchetti R usati sono necessari sul nodo perimetrale, in quanto la maggior parte dei passaggi degli script R viene eseguita qui. Per installare altri pacchetti R nel nodo perimetrale è possibile usare il consueto metodo `install.packages()` in R.

Di norma non è necessario installare altri pacchetti R sui nodi dati se si usano solo routine della libreria ScaleR in tutto il cluster. Potrebbero invece essere necessari pacchetti aggiuntivi per supportare l'uso dell'esecuzione **rxExec** o **RxDataStep** nei nodi dati.

In questi casi, i pacchetti aggiuntivi possono essere installati con un'azione script dopo la creazione del cluster. Per altre informazioni, vedere [Gestire R Server in cluster HDInsight](r-server-hdinsight-manage.md).

### <a name="change-hadoop-mapreduce-memory-settings"></a>Modificare le impostazioni di memoria per Hadoop MapReduce

È possibile modificare un cluster per cambiare la quantità di memoria disponibile per R Server quando si esegue un processo MapReduce. A tale scopo, usare l'interfaccia utente Apache Ambari disponibile nel pannello del Portale di Azure per il cluster. Per le istruzioni su come accedere all'interfaccia utente Ambari per il cluster, vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](../hdinsight-hadoop-manage-ambari.md).

È anche possibile modificare la quantità di memoria disponibile per R Server tramite gli switch Hadoop nella chiamata di **RxHadoopMR**, come illustrato di seguito:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Applicare la scalabilità al cluster

È possibile aumentare o ridurre le prestazioni di un cluster R Server esistente in HDInsight tramite il portale. Aumentando le prestazioni è possibile ottenere funzionalità aggiuntive necessarie per le attività di elaborazione più consistenti oppure è possibile ridimensionare il cluster quando inutilizzato. Per istruzioni su come ridimensionare un cluster, vedere l'articolo relativo alla [gestione di cluster HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Eseguire la manutenzione del sistema

La manutenzione viene eseguita sulle macchine virtuali Linux sottostanti di un cluster HDInsight durante gli orari di minore attività per applicare patch del sistema operativo e altri aggiornamenti. In genere, la manutenzione viene eseguita alle 3:30 AM (ora locale della macchina virtuale) ogni lunedì e giovedì. Gli aggiornamenti vengono eseguiti in modo tale da non compromettere più di un quarto del cluster per volta.  

Poiché i nodi head sono ridondanti e non tutti i nodi dati sono influenzati dalla procedura, i processi che vengono eseguiti in questo intervallo di tempo potrebbero essere rallentati, ma comunque completati. Gli eventuali software personalizzati o i dati locali di cui si dispone vengono preservati durante tutti questi eventi di manutenzione, a meno che si verifichi un errore catastrofico che richiede la ricompilazione del cluster.

## <a name="ide-options-for-r-server-on-an-hdinsight-cluster"></a>Opzioni IDE per R Server su un cluster HDInsight

Il nodo perimetrale Linux di un cluster HDInsight è la destinazione delle analisi basate su R. Le versioni recenti di HDInsight includono un'installazione predefinita di RStudio Server nel nodo perimetrale come ambiente IDE basato su browser. L'uso di RStudio Server come IDE per lo sviluppo e l'esecuzione di script R può essere notevolmente più produttivo rispetto all'uso esclusivo della console di R.

Inoltre, è possibile installare un IDE desktop e usarlo per accedere al cluster tramite l'uso di un contesto di calcolo remoto MapReduce o Spark. Le opzioni includono [R Tools per Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) Microsoft, RStudio e [StatET](http://www.walware.de/goto/statet) Walware basato su Eclipse.

Infine, è possibile accedere alla console di R nel nodo perimetrale digitando **R** al prompt dei comandi di Linux dopo aver stabilito la connessione tramite SSH o PuTY. L'uso della console di interfaccia è più semplice se si esegue un editor di testo per lo sviluppo di script R in un'altra finestra e si tagliano e incollano le sezioni dello script nella console R.

## <a name="pricing"></a>Prezzi

I prezzi associati a un cluster HDInsight con R Server sono strutturati in modo analogo ai prezzi per i cluster HDInsight standard. Sono basati sulle dimensioni delle VM sottostanti in nodi nome, dati e perimetrali, con una maggiorazione per le ore core. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di R Server in cluster HDInsight, vedere gli argomenti seguenti:

* [Introduzione a R Server in HDInsight](r-server-get-started.md)
* [Opzioni del contesto di calcolo per R Server su HDInsight](r-server-compute-contexts.md)
* [Opzioni di Archiviazione di Azure per il cluster R Server in HDInsight](r-server-storage.md)
