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
ms.openlocfilehash: 68c48bd4288b80d6cab515aa981d0f72461c0849
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035273"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Quali strumenti sono inclusi in Azure Data Science Virtual Machine?

Di seguito è riportato un elenco aggiornato degli strumenti inclusi nella Data Science Virtual Machine, oltre ai collegamenti per comprendere la configurazione di ogni strumento.


| **Strumento**                                                           | **DSVM di Windows** | **DSVM Linux** | **Note sull'utilizzo** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) con i pacchetti più diffusi pre-installati   |<span class='green-check'>&#9989;</span></br> (3.4.3)                    |<span class='green-check'>&#9989;</span></br> (3.4.3)  | [R nella DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition include: <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler): framework a elevate prestazioni parallelo e distribuito (R e Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package): nuovi algoritmi di Machine Learning all'avanguardia di Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp; [Operazionalizzazione R e Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus con attivazione condivisa: Excel, Word e PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2.7 e 3.5 con i pacchetti più diffusi preinstallati    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4.2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) con i pacchetti più diffusi pre-installati per il linguaggio di programmazione Julia                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [Julia nella DSVM](./dsvm-languages.md#julia) |
| Database relazionali                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server nella DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Strumenti del database                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Driver ODBC/JDBC|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (strumento di query), <br />  bcp, sqlcmd <br />  Driver ODBC/JDBC| |
| Analisi in database scalabile con i servizi di Machine Learning di SQL Server (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Server Jupyter Notebook](https://jupyter.org/) con i kernel seguenti:                                  |<span class='green-check'>&#9989;</span></br> (5.5.0)   |<span class='green-check'>&#9989;</span> | [Esempi di Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span></br> (3.4.3) |<span class='green-check'>&#9989;</span></br> (3.4.3) | [Esempi di R Jupyter](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span></br> (3) |<span class='green-check'>&#9989;</span> | [Esempi di Python Jupyter](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Esempi di Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Esempi di PySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (solo Ubuntu) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (server notebook multiutente)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (server notebook multiutente) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| Strumenti di sviluppo, IDE ed editor di codice:| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) con plug-in Git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) e [R Tools per Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio nella DSVM](./dsvm-tools-development.md#visual-studio-2017) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1.31) | [Visual Studio Code nella DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.1.463) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio Desktop nella DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio Server nella DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (2018.1.4)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [PyCharm nella DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno nella DSVM](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> (8.1.5) |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Git](https://git-scm.com/) e Git Bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0_201) |<span class='green-check'>&#9989;</span></br> (1.8.0_222) | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span></br> (4.7.2) |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/en-us/) |<span class='green-check'>&#9989;</span></br> (2.65.5313.1381) |<span class='red-x'>&#10060;</span>   |
| SDK per accedere alla suite di servizi di Cortana Intelligence e di Azure |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI Desktop nella DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Strumenti di gestione e spostamento dati: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> (1.6.2) |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [Azcopy nella DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Driver BLOB FUSE](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.0.2) | [blobfuse nella DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [Adlcopy nella DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Utilità di migrazione dati Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB nella DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; Strumenti da riga di comando Unix/Linux |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Apache Drill](https://drill.apache.org) per l'esplorazione dei dati |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [Apache Drill nella DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Strumenti di Machine Learning: ||||
| &nbsp;&nbsp;&nbsp;&nbsp; Integrazione con [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0.81) |<span class='green-check'>&#9989;</span></br> (0.80) | [XGBoost nella DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8.1) | [Vowpal Wabbit nella DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3.8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> (5.1.0) |<span class='green-check'>&#9989;</span></br> (5.2.2) | [Weka nella DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (2.1.2) | [LightGBM nella DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 nella DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Strumenti di Deep Learning compatibili con GPU o CPU: |  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe](https://github.com/BVLC/caffe) e [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) e [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) nella DSVM |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (5.2.0) | [Chainer nella DSVM](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Driver CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [CUDA, cuDNN, NVIDIA Driver nella DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod nella DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (2.2.4) | [Keras nella DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/en-us/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (2.5.1) | [CNTK nella DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.3.0) | [MXNet nella DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.0.1) | [MXNet Model Server nella DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [nvidia-smi nella DSVM](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.2.0) | [PyTorch nella DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1.13) |<span class='green-check'>&#9989;</span></br> (1.13) | [TensorFlow nella DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.12.0) | [TensorFlow Serving nella DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (1.0.3) | [Theano nella DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

