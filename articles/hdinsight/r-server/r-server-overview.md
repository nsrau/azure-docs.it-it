---
title: Introduzione a ML Services in Azure HDInsight
description: Informazioni su come usare ML Services in HDInsight per creare applicazioni per l'analisi di Big Data.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: b3a6fc2ff45d61d63118edc23f40f69f16db9131
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536148"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Informazioni su ML Services in Azure HDInsight

Microsoft Machine Learning Server è disponibile come opzione di distribuzione quando si creano i cluster HDInsight in Azure. Il tipo di cluster che offre questa opzione è denominato **ML Services** . Questa funzionalità offre l'accesso su richiesta a metodi di analisi distribuiti e adattabili in HDInsight.

ML Services su HDInsight offre le più recenti funzionalità di analisi basate su R per set di dati praticamente di qualsiasi dimensione. I set di dati possono essere caricati in archiviazione BLOB di Azure o Data Lake. Le applicazioni basate su R possono usare gli oltre 8000 pacchetti R open source disponibili. Sono disponibili anche le routine di ScaleR, il pacchetto di analisi di Big Data di Microsoft.

Il nodo perimetrale offre una posizione pratica per connettersi al cluster ed eseguire gli script R. Il nodo perimetrale consente di eseguire le funzioni distribuite parallelizzate di ScaleR tra i core del server. È anche possibile eseguirle tra i nodi del cluster usando Hadoop Map Reduce di ScaleR. È inoltre possibile usare i contesti di calcolo Apache Spark.

I modelli o le previsioni che risultano dalle analisi possono essere scaricati per l'uso locale o anche essere impostati come `operationalized` in altre parti di Azure, in particolare, tramite [Azure Machine Learning Studio (versione classica)](https://studio.azureml.net) e il [servizio Web](../../machine-learning/classic/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Introduzione all'uso di ML Services in HDInsight

Per creare un cluster ML Services in HDInsight, selezionare il tipo di cluster **ML Services** . Il tipo di cluster ML Services include ML Server nei nodi dati e nel nodo perimetrale. Il nodo perimetrale funge da zona di destinazione per l'analisi basata su ML Services. Vedere [Creare cluster Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) per una procedura dettagliata su come creare il cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Perché scegliere ML Services in HDInsight?

ML Services in HDInsight offre i vantaggi seguenti:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Le opzioni di intelligenza artificiale innovative Microsoft e open source

  ML Services include un set di algoritmi distribuiti e altamente adattabili, ad esempio [RevoscaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) e [microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package). Questi algoritmi funzionano con dimensioni dei dati maggiori rispetto a quelle della memoria fisica. Vengono inoltre eseguiti anche in un'ampia gamma di piattaforme in modalità distribuita. Altre informazioni sulla raccolta di [pacchetti R](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) e [pacchetti Python](/machine-learning-server/python-reference/introducing-python-package-reference) personalizzata da Microsoft inclusa nel prodotto.
  
  ML Services offre queste innovazioni Microsoft e i contributi della community open source, ovvero R, Python e i toolkit di intelligenza artificiale. Il tutto su un'unica piattaforma di livello aziendale. Qualsiasi pacchetto per l'apprendimento automatico open source Python o R può operare in affiancamento alle innovative opzioni proprietarie Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Funzionalità di amministrazione e operazionalizzazione semplici, sicure e su vasta scala

  Le aziende che fanno affidamento su ambienti e paradigmi tradizionali investono molto tempo ed energie nell'operazionalizzazione. Questa azione comporta aumenti dei costi e ritardi, che includono il tempo di conversione per i modelli, le iterazioni per mantenerli validi e aggiornati, l'approvazione dei requisiti normativi e la gestione delle autorizzazioni tramite.

  ML Services offre [operazionalizzazione](/machine-learning-server/what-is-operationalization) di livello aziendale. Dopo il completamento di un modello di Machine Learning, sono necessari pochi clic per generare le API dei servizi Web. Questi [servizi Web](/machine-learning-server/operationalize/concept-what-are-web-services) sono ospitati in una griglia di server nel cloud e possono essere integrati con applicazioni line-of-business. La distribuzione in una griglia elastica consente una perfetta scalabilità con le esigenze aziendali, per l'assegnazione dei punteggi in batch e in tempo reale. Per istruzioni, vedere [Rendere operativo un cluster ML Services in HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Il tipo di cluster ML Services in HDInsight è supportato solo in HDInsight 3.6. HDInsight 3.6 verrà ritirato il 31 dicembre 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Funzionalità principali di ML Services su HDInsight

Le funzionalità seguenti sono incluse in ML Services su HDInsight.

| Categoria funzionalità | Descrizione |
|------------------|-------------|
| Abilitazione per R | [Pacchetti R](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) per soluzioni scritte in R, con una distribuzione open source di R e un'infrastruttura di runtime per l'esecuzione di script. |
| Abilitazione per Python | [Moduli Python](/machine-learning-server/python-reference/introducing-python-package-reference) per soluzioni scritte in Python, con una distribuzione open source di Python e un'infrastruttura di runtime per l'esecuzione di script.
| [Modelli con training preliminare](/machine-learning-server/install/microsoftml-install-pretrained-models) | Per analisi visiva e analisi del sentiment in messaggi di testo, idonei all'assegnazione dei punteggi ai dati immessi. |
| [Distribuzione e utilizzo](r-server-operationalize.md) | Impostare il server con `Operationalize` e distribuire soluzioni come servizio Web. |
| [Esecuzione remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Iniziare sessioni remote sul cluster ML Services nella rete dalla propria workstation client. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opzioni di archiviazione dei dati per ML Services su HDInsight

La risorsa di archiviazione predefinita per il file system HDFS può essere un account di Archiviazione di Azure o Azure Data Lake Storage. I dati caricati nell'archiviazione cluster durante l'analisi vengono resi persistenti e sono disponibili anche sopo l'eliminazione del cluster. Diversi strumenti possono gestire il trasferimento dei dati nell'archiviazione, tra cui la funzionalità basata sul portale di caricamento dell'account di archiviazione e l'utilità AzCopy.

È possibile abilitare l'accesso ad altri archivi BLOB e data lake durante la creazione del cluster. L'opzione di archiviazione primaria in uso non costituisce un limite.  Per altre informazioni sull'uso di più account di archiviazione, vedere l'articolo [Opzioni di Archiviazione di Azure per ML Services su HDInsight](./r-server-storage.md).

È anche possibile ricorrere a File di Azure come opzione di archiviazione per l'uso sul nodo perimetrale. File di Azure rende disponibili nel file system Linux le condivisioni file create in Archiviazione di Azure. Per altre informazioni, vedere [Opzioni di Archiviazione di Azure per ML Services in HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Accedere al nodo perimetrale di ML Services

È possibile connettersi a Microsoft ML Server sul nodo perimetrale tramite un browser o SSH/PuTTY. La console R viene installata per impostazione predefinita durante la creazione del cluster.  

## <a name="develop-and-run-r-scripts"></a>Sviluppare ed eseguire script R

Gli script R possono usare uno degli oltre 8000 pacchetti R open source disponibili. È anche possibile usare le routine distribuite e parallelizzate della libreria ScaleR. Nel nodo perimetrale gli script vengono eseguiti nel rispettivo interprete R. Fanno eccezione i passaggi che chiamano le funzioni ScaleR con un contesto di calcolo Map Reduce (RxHadoopMR) o Spark (RxSpark). Le funzioni vengono eseguite in modo distribuito tra i nodi dati associati ai dati. Per altre informazioni sulle opzioni relative al contesto, vedere [Opzioni del contesto di calcolo per ML Services su HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Impostare un modello con `Operationalize`

Dopo aver completato la modellazione dei dati, è possibile impostare un modello con `operationalize` per generare previsioni per i nuovi dati, da Azure o in locale. Questo processo è noto come assegnazione di punteggi. L'assegnazione dei punteggi può essere eseguita in HDInsight, in Azure Machine Learning o in locale.

### <a name="score-in-hdinsight"></a>Assegnare punteggi in HDInsight

Per assegnare un punteggio in HDInsight, scrivere una funzione R. La funzione chiama il modello al fine di eseguire previsioni su un nuovo file di dati caricato nell'account di archiviazione. Salvare quindi le previsioni sull'account di archiviazione. È possibile eseguire la routine su richiesta sul nodo perimetrale del cluster o usando un processo pianificato.

### <a name="score-in-azure-machine-learning-aml"></a>Assegnare punteggi in Azure Machine Learning (AML)

Per assegnare punteggi tramite Azure Machine Learning, usare il pacchetto R open source di Azure Machine Learning noto come [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) per pubblicare il modello come servizio Web di Azure. Per comodità, questo pacchetto è già installato nel nodo perimetrale. Usare quindi le funzioni di Azure Machine Learning per creare un'interfaccia utente per il servizio Web e poi richiamare il servizio Web quando necessario per l'assegnazione dei punteggi. Quindi convertire gli oggetti del modello ScaleR in oggetti equivalenti del modello open source per l'uso con il servizio Web. Per tale conversione usare le funzioni di coercizione di ScaleR, come `as.randomForest()` per i modelli basati su insiemi.

### <a name="score-on-premises"></a>Assegnare punteggi in locale

Per assegnare punteggi in locale dopo aver creato il modello, serializzare il modello in R, scaricarlo, deserializzarlo e poi usarlo per assegnare punteggi ai nuovi dati. È possibile assegnare punteggi ai nuovi dati usando l'approccio descritto in precedenza in Assegnare punteggi in HDInsight o tramite i [servizi Web](/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Eseguire la manutenzione del cluster

### <a name="install-and-maintain-r-packages"></a>Installare e gestire i pacchetti R

Molti dei pacchetti R usati sono necessari sul nodo perimetrale, in quanto la maggior parte dei passaggi degli script R viene eseguita qui. Per installare altri pacchetti R nel nodo perimetrale è possibile usare il metodo `install.packages()` in R.

Se si usano solo routine della libreria ScaleR, in genere non sono necessari altri pacchetti R. Potrebbero invece essere necessari altri pacchetti per l'esecuzione di **rxExec** o **RxDataStep** nei nodi dati.

I pacchetti aggiuntivi possono essere installati con un'azione script dopo la creazione del cluster. Per altre informazioni, vedere [Gestire ML Services nel cluster HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Modificare le impostazioni di memoria per Apache Hadoop MapReduce

È possibile modificare la memoria disponibile per ML Services quando viene eseguito un processo MapReduce. Per modificare un cluster, usare l'interfaccia utente di Apache Ambari per il cluster. Per istruzioni sull'interfaccia utente di Ambari, vedere [Gestire i cluster HDInsight con l'interfaccia utente Web Ambari](../hdinsight-hadoop-manage-ambari.md).

È possibile cambiare la memoria disponibile per ML Services usando opzioni Hadoop nella chiamata a **RxHadoopMR** :

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>Applicare la scalabilità al cluster

È possibile aumentare o ridurre le prestazioni di un cluster ML Services esistente in HDInsight tramite il portale. Grazie alla scalabilità verticale, si ottiene capacità aggiuntiva per attività di elaborazione estese. È possibile ridimensionare un cluster quando è inattivo. Per istruzioni su come ridimensionare un cluster, vedere l'articolo relativo alla [gestione di cluster HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Eseguire la manutenzione del sistema

La manutenzione del sistema operativo viene eseguita fuori orario sulle macchine virtuali Linux sottostanti di un cluster HDInsight. In genere, la manutenzione viene eseguita alle 3:30 (ora locale della VM) ogni lunedì e giovedì. Gli aggiornamenti non interessano più di un quarto del cluster alla volta.

I processi in esecuzione potrebbero rallentare durante la manutenzione. ma comunque completati. Gli eventuali software personalizzati o i dati locali vengono preservati durante questi eventi di manutenzione, a meno che non si verifichi un errore irreversibile che richiede la ricompilazione del cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opzioni IDE per ML Services su HDInsight

Il nodo perimetrale Linux di un cluster HDInsight è la destinazione delle analisi basate su R. Le versioni recenti di HDInsight includono un IDE basato su browser di RStudio Server nel nodo perimetrale. RStudio Server è più produttivo della console R per lo sviluppo e l'esecuzione.

Un IDE desktop può accedere al cluster tramite un contesto di calcolo MapReduce o Spark remoto. Le opzioni includono: Microsoft [R Tools per Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio e StatET Walware basato su Eclipse.

Accedere alla console di R nel nodo perimetrale digitando **R** al prompt dei comandi. Quando si usa l'interfaccia della console, è consigliabile sviluppare uno script R in un editor di testo. Quindi tagliare e incollare le sezioni dello script nella console di R in base alle esigenze.

## <a name="pricing"></a>Prezzi

I prezzi associati a un cluster HDInsight di ML Services sono strutturati in modo analogo a quelli per altri tipi di cluster HDInsight. Sono basati sulle dimensioni delle VM sottostanti in nodi nome, dati e perimetrali, con una maggiorazione per le ore core. Per altre informazioni, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di ML Services nei cluster HDInsight, vedere gli articoli seguenti:

* [Eseguire uno script R in un cluster ML Services in Azure HDInsight con RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Opzioni del contesto di calcolo per un cluster ML Services su HDInsight](r-server-compute-contexts.md)
* [Opzioni di archiviazione per un cluster ML Services su HDInsight](r-server-storage.md)