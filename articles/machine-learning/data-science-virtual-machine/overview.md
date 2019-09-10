---
title: Informazioni su Azure Data Science Virtual Machine
description: Scenari e componenti chiave dell'analisi per le macchine virtuali data science per Windows e Linux.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 3295a59ee4496d332f0d886c89ca900ab6b4bcd1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191903"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Cos'è la Azure Data Science Virtual Machine per Linux e Windows?

La Data Science Virtual Machine (DSVM) è un'immagine di VM personalizzata sulla piattaforma cloud di Azure creata specificamente per le attività di data science. Include diversi strumenti comuni per l'analisi scientifica dei dati e altri strumenti preinstallati e preconfigurati per implementare rapidamente la creazione di applicazioni intelligenti per l'analisi avanzata. 

Le configurazioni degli strumenti vengono rigorosamente testate da data scientist e sviluppatori Microsoft e dalla più ampia community di data science. Questi test aiutano a garantire stabilità e fattibilità in generale.

Data Science Virtual Machine è disponibile in:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16.04 LTS e CentOS 7.4

> [!NOTE]
> Nella Data Science Virtual Machine sono stati raggruppati tutti gli strumenti delle VM per Deep Learning. 


## <a name="what-can-i-do-with-the-dsvm"></a>Quali operazioni si possono eseguire con la DSVM?
L'obiettivo della Data Science Virtual Machine (DSVM) è offrire ai professionisti dei dati, con tutti i livelli di competenze e in tutti i settori, un ambiente di data science privo di problemi, preconfigurato e pienamente integrato. Invece di implementare un'area di lavoro paragonabile autonomamente, è possibile eseguire il provisioning di una DSVM. Questa scelta consente di risparmiare diversi giorni o persino _settimane_ nelle procedure di installazione, configurazione e gestione dei pacchetti. Dopo aver allocato la macchina virtuale per l'analisi scientifica, è possibile iniziare immediatamente a lavorare al progetto di analisi scientifica dei dati.

La DSVM è progettata e configurata per l'uso in un'ampia gamma di scenari di utilizzo. È possibile aumentare e ridurre le prestazioni dell'ambiente man mano che cambiano i requisiti del progetto. È anche possibile usare il linguaggio preferito per programmare le attività di data science e installare altri strumenti per personalizzare il sistema in base a specifiche esigenze.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Desktop di analisi preconfigurato nel cloud
La DSVM offre una configurazione di base per i team di data science che vogliono sostituire i desktop locali con un desktop cloud gestito. Questa configurazione di base garantisce che tutti gli scienziati dei dati presenti in un team abbiano una configurazione coerente mediante la quale verificare gli esperimenti e promuovere la collaborazione. Anche i costi diminuiscono, grazie alla riduzione del carico lavorativo per gli amministratori di sistema e del tempo necessario per valutare, installare e gestire i vari pacchetti software per l'analisi avanzata.

### <a name="data-science-training-and-education"></a>Preparazione e formazione sull'analisi scientifica dei dati
Gli istruttori e i formatori aziendali che tengono corsi di data science in genere forniscono un'immagine di macchina virtuale per garantire che gli studenti abbiano una configurazione coerente e che gli esempi abbiano un comportamento prevedibile. 

La DSVM consente di creare un ambiente su richiesta con una configurazione coerente che semplifica i problemi relativi a incompatibilità e supporto. Esistono vantaggi sostanziali per i casi in cui tali ambienti devono essere compilati di frequente, in particolare per i corsi di formazione più brevi.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacità elastica su richiesta per progetti su larga scala
Gli hackathon e i concorsi di data science o la modellazione e l'esplorazione di dati su larga scala richiedono una maggiore capacità hardware, in genere per brevi periodi di tempo. La DSVM consente di replicare rapidamente e su richiesta l'ambiente di data science su server con scalabilità orizzontale, che consentono di eseguire esperimenti con risorse di calcolo con potenza elevata.

### <a name="custom-compute-power-for-azure-notebooks"></a>Potenza di calcolo personalizzata per Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) è un servizio ospitato gratuito che consente di sviluppare, eseguire e condividere notebook di Jupyter nel cloud senza alcuna installazione. Il livello di servizio gratuito è limitato a 4 GB di memoria e 1 GB di dati. 

Per eliminare tutti i limiti, è possibile collegare un progetto di Notebooks a una DSVM o a qualsiasi altra macchina virtuale in esecuzione in un server Jupyter. Se si accede ad Azure Notebooks con un account tramite Azure Active Directory (ad esempio un account aziendale), Notebooks mostrerà automaticamente le DSVM incluse in qualsiasi sottoscrizione associata a tale account. È possibile [collegare una DSVM ad Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) per ampliare la potenza di calcolo disponibile.

### <a name="short-term-experimentation-and-evaluation"></a>Valutazione e sperimentazione a breve termine
È possibile usare la DSVM per valutare o esplorare strumenti come questi, con un minimo sforzo di installazione:

- Microsoft Machine Learning Server
- SQL Server
- Strumenti di Visual Studio
- Jupyter
- Toolkit di Deep Learning e Machine Learning
- Nuovi strumenti diffusi nella community 

Essendo facile da configurare, la DSVM può essere applicata in altri scenari di utilizzo a breve termine, come la replica di esperimenti pubblicati e l'esecuzione di demo e procedure dettagliate, in sessioni online ed esercitazioni tramite conferenza.

### <a name="deep-learning"></a>Apprendimento avanzato
Nella DSVM i modelli di training possono usare algoritmi di Deep Learning su hardware basato su GPU (Graphics Processing Unit). Sfruttando il vantaggio della scalabilità delle VM della piattaforma Azure, la DSVM consente di usare hardware basato su GPU nel cloud secondo specifiche esigenze. È possibile passare a una VM basata su GPU durante il training di modelli di grandi dimensioni o quando sono necessari calcoli ad alta velocità, mantenendo lo stesso disco del sistema operativo.  

Nell'edizione per Windows Server 2016 della DSVM sono preinstallati i driver GPU, i framework e la versione GPU dei framework di Deep Learning. Nell'edizione per Linux, il Deep Learning su GPU è abilitato sulle DSVM CentOS e Ubuntu. 

È anche possibile distribuire l'edizione Ubuntu, CentOS o Windows 2016 della DSVM in una macchina virtuale di Azure non basata su GPU. In questo caso tutti i framework di Deep Learning eseguiranno il fallback alla modalità CPU.
 
[Altre informazioni sui framework di Deep Learning e IA disponibili](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Quali funzionalità sono incluse nella DSVM?
La macchina virtuale per l'analisi scientifica dei dati ha già installati e configurati numerosi strumenti comuni per l'analisi scientifica dei dati e l'apprendimento avanzato. Include inoltre strumenti che semplificano l'uso di vari prodotti di Azure per l'analisi e per i dati. Questi prodotti includono Microsoft Machine Learning Server (R, Python) per la creazione di modelli predittivi e SQL Server 2017 per l'esplorazione su larga scala dei set di dati. La DSVM include altri strumenti della community open source e di Microsoft, nonché [esempi di codice e notebook](dsvm-samples-and-walkthroughs.md). 

Ecco un elenco di strumenti e piattaforme:
+ [Linguaggi di programmazione supportati](dsvm-languages.md)

+ [Piattaforme dati supportate](dsvm-data-platforms.md)

+ [IDE e strumenti di sviluppo](dsvm-tools-development.md)

+ [Framework di Deep Learning e IA](dsvm-deep-learning-ai-frameworks.md)

+ [Strumenti per apprendimento automatico e data science](dsvm-ml-data-science-tools.md)

+ [Strumenti per l'inserimento di dati](dsvm-tools-ingestion.md)

+ [Strumenti per la visualizzazione e l'esplorazione dei dati](dsvm-tools-explore-and-visualize.md)

La tabella seguente indica in modo dettagliato e confronta i componenti principali inclusi nelle edizioni per Windows e Linux della macchina virtuale per l'analisi scientifica dei dati.

| **Strumento**                                                           | **Edizione per Windows** | **Edizione per Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) con i pacchetti più diffusi pre-installati   |S                      | S             |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition include: <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler): framework a elevate prestazioni parallelo e distribuito (R e Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package): nuovi algoritmi di Machine Learning all'avanguardia di Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp; [Operazionalizzazione R e Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |S                      | S |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus con attivazione condivisa: Excel, Word e PowerPoint   |S                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7 e 3.5 con i pacchetti più diffusi preinstallati    |S                      |S              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) con i pacchetti più diffusi pre-installati per il linguaggio di programmazione Julia                         |S                      |S              |
| Database relazionali                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Strumenti del database                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Driver ODBC/JDBC|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (strumento di query), <br />  bcp, sqlcmd <br />  Driver ODBC/JDBC|
| Analisi in database scalabile con i servizi di Machine Learning di SQL Server (R, Python) | S     |N              |
| [Server Jupyter Notebook](https://jupyter.org/) con i kernel seguenti:                                  | S     | S |
|     &nbsp;&nbsp;&nbsp;&nbsp; R | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (soltanto Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     | N | S |
| JupyterHub (server notebook multiutente)| N | S |
| JupyterLab (server notebook multiutente) | N | Y (soltanto Ubuntu) |
| Strumenti di sviluppo, IDE ed editor di codice:| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) con plug-in Git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) e [R Tools per Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)| S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; Vim ed Emacs | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; Git e Git Bash | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework | S | N |
| Power BI Desktop | S | N |
| SDK per accedere alla suite di servizi di Cortana Intelligence e di Azure | S | S |
| Strumenti di gestione e spostamento dati: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure Storage Explorer | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Driver BLOB FUSE](https://github.com/Azure/azure-storage-fuse) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Utilità di migrazione dati Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Gateway di gestione dati Microsoft](https://msdn.microsoft.com/library/dn879362.aspx): spostamento di dati tra ambiente locale e cloud | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp; Strumenti da riga di comando Unix/Linux | S | S |
| [Apache Drill](https://drill.apache.org) per l'esplorazione dei dati | S | S |
| Strumenti di Machine Learning: |||
| &nbsp;&nbsp;&nbsp;&nbsp; Integrazione con [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (soltanto Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) | N | Y (soltanto Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | Y (soltanto Ubuntu) |
| Strumenti di Deep Learning compatibili con GPU o CPU: |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org/) | S (Windows 2016) | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/uber/horovod) | N | S (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.io/) | S (Windows 2016) | S|
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe e Caffe2](https://github.com/caffe2/caffe2) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Torch](http://torch.ch/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorRT](https://developer.nvidia.com/tensorrt) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp; [Driver CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit) | S | S |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

+ Windows:
  + [Configurare una Data Science Virtual Machine Windows](provision-vm.md)
  + [Dieci cose da fare con una Data Science Virtual Machine Windows](vm-do-ten-things.md)

+ Linux:
  + [Configurare una Data Science Virtual Machine Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Configurare una Data Science Virtual Machine Linux (CentOS)](linux-dsvm-intro.md)
  + [Data science in una Data Science Virtual Machine Linux](linux-dsvm-walkthrough.md)
