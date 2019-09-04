---
title: Strumenti di & data science di Machine Learning
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni su strumenti per machine learning e framework preinstallati in Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: f97412bc3f8d8b046df1324c7e1a0ad568f5db52
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278773"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Strumenti di machine learning e data science sulle macchine virtuali di Data Science di Azure
Data Science Virtual Machine offre un ampio set di strumenti e librerie per machine learning (ML) disponibili nei linguaggi più diffusi, ad esempio Python, R, Julia. 

Ecco alcuni strumenti e librerie ML in Data Science Virtual Machine. 

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK per Python
Vedere le informazioni di riferimento complete per [Azure Machine Learning SDK per Python](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Che cos'è? |   Azure Machine Learning è un servizio cloud usato per sviluppare e distribuire modelli di Machine Learning. È possibile tenere traccia dei modelli durante la compilazione, il training, il ridimensionamento e la gestione con Python SDK. Distribuire modelli come contenitori ed eseguirli nel cloud, in locale o in Azure IoT Edge. |
| Edizioni supportate  | Windows (ambiente conda: Azure ML), Linux (ambiente conda: py36) |
| Usi tipici      | Piattaforma Machine Learning generale  |
| Come viene configurata o installata? |  Installata con supporto GPU |
| Come usarla o eseguirla | Come Python SDK e interfaccia della riga di comando di Azure. Attivare per l'ambiente Conda `AzureML` nell'edizione per Windows *o* per `py36` nell'edizione per Linux.  |
| Collegamenti agli esempi | I notebook di Jupyter di esempio sono inclusi nella directory `AzureML` in notebook.  |
| Strumenti correlati  | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Che cos'è?   |    XGBoost è una libreria di gradient boosting veloce, portatile e distribuita (GBDT, GBRT o GBM) per Python, R, Java, Scala, C++ e altro ancora. Viene eseguita su un solo computer, Hadoop, Spark.    |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Libreria ML generale      |
| Come viene configurata o installata?      |  Installata con supporto GPU   |
| Come usarla o eseguirla      | Come pacchetto R della libreria di Python 2.7 e 3.5 e sullo strumento della riga di comando del percorso (`C:\dsvm\tools\xgboost\bin\xgboost.exe` per Windows, `/dsvm/tools/xgboost/xgboost` per Linux)    |
| Collegamenti agli esempi      | Gli esempi sono inclusi nella macchina virtuale, in `/dsvm/tools/xgboost/demo` su Linux e `C:\dsvm\tools\xgboost\demo` su Windows.   |
| Strumenti correlati      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Vowpal Wabbit, noto anche come "VW", è una libreria del sistema di apprendimento esterno rapido e open source.    |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Libreria ML generale      |
| Come viene configurata o installata?      |  Windows: programma di installazione msi, Linux: apt-get |
| Come usarla o eseguirla      | Come strumento della riga di comando nel percorso (`C:\Program Files\VowpalWabbit\vw.exe` in Windows, `/usr/bin/vw` in Linux)    |
| Collegamenti agli esempi      | [Esempi di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Strumenti correlati      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Che cos'è?   |  Weka è una raccolta di algoritmi per Machine Learning per attività di data mining. Gli algoritmi possono essere applicati direttamente a un set di dati o chiamati dal codice Java. Weka contiene strumenti per la pre-elaborazione, la classificazione, la regressione, il clustering, le regole di associazione e la visualizzazione dei dati. |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Strumento Machine Learning generale     |
| Come usarla o eseguirla      | In Windows cercare Weka nel menu Start. In Linux accedere con X2Go e quindi andare a **Applications** > **Development** > **Weka**. |
| Collegamenti agli esempi      | [Esempi di Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Strumenti correlati      |LightGBM, Rattle, XGBoost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Rattle è un'interfaccia utente grafica per data mining con R.   |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Strumento di data mining dell'interfaccia utente generale per R    |
| Come usarla o eseguirla      | Strumento dell'interfaccia utente. In Windows avviare un prompt dei comandi, eseguire R, quindi all'interno di R eseguire `rattle()`. In Linux connettersi con X2Go, avviare un terminale, eseguire R, quindi all'interno di R eseguire `rattle()`. |
| Collegamenti agli esempi      | [Rattle](https://togaware.com/onepager/) |
| Strumenti correlati      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Che cos'è?   | LightGBM è un framework rapido, distribuito e a prestazioni elevate di gradient boosting (GBDT, GBRT, GBM, or MART) basato su algoritmi dell'albero delle decisioni. Viene usato per la classificazione e molte altre attività di Machine Learning.    |
| Versioni supportate      | Windows, Linux    |
| Usi tipici      | Framework di gradient boosting generico      |
| Come viene configurata o installata?      | In Windows LightGBM viene installato come pacchetto di Python. In Linux il file eseguibile da riga di comando si trova in `/opt/LightGBM/lightgbm`, viene installato il pacchetto R e i pacchetti di Python.     |
| Collegamenti agli esempi      | [Guida a LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Strumenti correlati      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Che cos'è?   | H2O è una piattaforma IA open source che supporta Machine Learning in memoria, distribuito, veloce e scalabile.  |
| Versioni supportate      | Linux   |
| Usi tipici      | ML scalabile e distribuito generico   |
| Come viene configurata o installata?      | H2O è installato in `/dsvm/tools/h2o`.      |
| Come usarla o eseguirla      | Connettersi alla macchina virtuale con X2Go. Avviare un nuovo terminale ed eseguire `java -jar /dsvm/tools/h2o/current/h2o.jar`. Quindi avviare un Web browser e connettersi a `http://localhost:54321`.      |
| Collegamenti agli esempi      | Gli esempi sono disponibili nella macchina virtuale in Jupyter nella directory `h2o`.      |
| Strumenti correlati      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Esistono molte altre librerie ML in Data Science Virtual Machine come il noto pacchetto `scikit-learn` incluso nella distribuzione Anaconda Python installata in Data Science Virtual Machine. Per estrarre l'elenco dei pacchetti disponibili in Python, R e Julia, eseguire i rispettivi strumenti di gestione dei pacchetti.
