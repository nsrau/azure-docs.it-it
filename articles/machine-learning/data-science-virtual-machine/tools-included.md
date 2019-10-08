---
title: Strumenti inclusi nella DSVM
titleSuffix: Azure Data Science Virtual Machine tools
description: Elenco di strumenti inclusi nelle immagini DSVM di Windows e Ubuntu
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 09/27/2019
ms.openlocfilehash: 79de3406e47b84a6120496acce5bba948fbb1a6d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803547"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Quali strumenti sono inclusi in Azure Data Science Virtual Machine?

Di seguito è riportato un elenco aggiornato degli strumenti inclusi nella Data Science Virtual Machine, oltre ai collegamenti per comprendere la configurazione di ogni strumento.


| **Strumento**                                                           | **DSVM di Windows** | **DSVM Linux** | **Note sull'utilizzo** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) con i pacchetti più diffusi pre-installati   |<span class='green-check'>&#9989;</span>                     |<span class='green-check'>&#9989;</span>  | [Uso di R nella DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition include: <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler): framework a elevate prestazioni parallelo e distribuito (R e Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package): nuovi algoritmi di Machine Learning all'avanguardia di Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp; [Operazionalizzazione R e Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus con attivazione condivisa: Excel, Word e PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2.7 e 3.5 con i pacchetti più diffusi preinstallati    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) con i pacchetti più diffusi pre-installati per il linguaggio di programmazione Julia                         |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | [Uso di Julia nella DSVM](./dsvm-languages.md#julia) |
| Database relazionali                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | (./dsvm-data-platforms#sql-server-2016-developer-edition.md) |
| Strumenti del database                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Driver ODBC/JDBC|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (strumento di query), <br />  bcp, sqlcmd <br />  Driver ODBC/JDBC| |
| Analisi in database scalabile con i servizi di Machine Learning di SQL Server (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Server Jupyter Notebook](https://jupyter.org/) con i kernel seguenti:                                  |<span class='green-check'>&#9989;</span>     |<span class='green-check'>&#9989;</span> | [Esempi di Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Esempi di R Jupyter](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Esempi di Python Jupyter](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Esempi di Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Esempi di PySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (solo Ubuntu) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (server notebook multiutente)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (server notebook multiutente) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (solo Ubuntu) | |
| Strumenti di sviluppo, IDE ed editor di codice:| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) con plug-in Git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) e [R Tools per Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio 2019 nella DSVM](./dsvm-tools-development.md#visual-studio-2019) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Visual Studio Code nella DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [RStudio Desktop nella DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio Server nella DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [PyCharm nella DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Juno nella DSVM](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; Vim ed Emacs |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Git e Git Bash |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| Power BI Desktop |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   |
| SDK per accedere alla suite di servizi di Cortana Intelligence e di Azure |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI Desktop nella DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Strumenti di gestione e spostamento dati: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure Storage Explorer |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Azcopy nella DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Driver BLOB FUSE](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [blobfuse nella DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Adlcopy nella DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Utilità di migrazione dati Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB nella DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Gateway di gestione dati Microsoft](https://msdn.microsoft.com/library/dn879362.aspx): spostamento di dati tra ambiente locale e cloud |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Utilizzo della DSVM](./dsvm-tools-ingestion.md#microsoft-data-management-gateway) |
| &nbsp;&nbsp;&nbsp;&nbsp; Strumenti da riga di comando Unix/Linux |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Apache Drill](https://drill.apache.org) per l'esplorazione dei dati |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Apache Drill nella DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Strumenti di Machine Learning: ||||
| &nbsp;&nbsp;&nbsp;&nbsp; Integrazione con [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [XGBoost nella DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Vowpal Wabbit nella DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Weka nella DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (solo Ubuntu) | [LightGBM nella DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (solo Ubuntu) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (solo Ubuntu) | [H20 nella DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Strumenti di Deep Learning compatibili con GPU o CPU: |  |  | Per altre informazioni sull'utilizzo, fare clic sul nome dello strumento di seguito |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) (Windows 2016) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |<span class='green-check'>&#9989;</span> (Windows 2016) |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe e Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](./dsvm-deep-learning-ai-frameworks.md#chainer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](./dsvm-deep-learning-ai-frameworks.md#keras)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Driver CUDA, cuDNN, NVIDIA](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
