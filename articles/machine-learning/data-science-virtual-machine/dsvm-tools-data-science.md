---
title: Strumenti per machine learning e data science
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni sugli strumenti e i framework di apprendimento automatico preinstallati nella macchina virtuale di data science.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c414087270558e21340e50114c0563ff7e50064c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282308"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Strumenti di apprendimento automatico e data science nelle macchine virtuali di analisi scientifica dei dati di AzureMachine learning and data science tools on Azure Data Science Virtual Machines
Azure Data Science Virtual Machines (DSVMs) have a rich set of tools and libraries for machine learning available in popular languages, such as Python, R, and Julia.

Ecco alcuni degli strumenti e delle librerie di apprendimento automatico nelle DSVM.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK per Python

Vedere il riferimento completo per [Azure Machine Learning SDK per Python.](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)

|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Azure Machine Learning è un servizio cloud che è possibile usare per sviluppare e distribuire modelli di apprendimento automatico. È possibile tenere traccia dei modelli durante la compilazione, il training, il ridimensionamento e la gestione con Python SDK. Distribuire modelli come contenitori ed eseguirli nel cloud, in locale o in Azure IoT Edge.   |
| Edizioni supportate     | Windows (ambiente Conda: AzureML), Linux (ambiente Conda: py36)    |
| Usi tipici      | Piattaforma generale di apprendimento automatico      |
| Come viene configurata o installata?      |  Installata con supporto GPU   |
| Come usarla o eseguirla      | Come Python SDK e nell'interfaccia della riga di comando di Azure.As a Python SDK and in the Azure CLI. Attivare per l'ambiente Conda `AzureML` nell'edizione per Windows *o* per `py36` nell'edizione per Linux.      |
| Collegamenti agli esempi      | I notebook di Jupyter di esempio sono inclusi nella directory `AzureML` in notebook.  |
| Strumenti correlati      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Piattaforma di iaaformazione open source che supporta l'apprendimento automatico in memoria, distribuito, veloce e scalabile.  |
| Versioni supportate      | Linux   |
| Usi tipici      | Apprendimento automatico distribuito e scalabile generico   |
| Come viene configurata o installata?      | H2O è installato in `/dsvm/tools/h2o`.      |
| Come usarla o eseguirla      | Connettersi alla macchina virtuale con X2Go. Avviare un nuovo terminale ed eseguire `java -jar /dsvm/tools/h2o/current/h2o.jar`. Quindi avviare un Web browser e connettersi a `http://localhost:54321`.      |
| Collegamenti agli esempi      | Gli esempi sono disponibili nella macchina virtuale in Jupyter nella directory `h2o`.      |
| Strumenti correlati      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Esistono diverse altre librerie di apprendimento automatico nelle DSVM, ad esempio il pacchetto popolare `scikit-learn` che fa parte della distribuzione Anaconda Python per DSVm. Per estrarre l'elenco dei pacchetti disponibili in Python, R e Julia, eseguire i rispettivi strumenti di gestione dei pacchetti.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework veloce, distribuito e ad alte prestazioni con gradient-boosting (GBDT, GBRT, GBM o MART) basato su algoritmi dell'albero delle decisioni. Viene usato per la classificazione, la classificazione e molte altre attività di apprendimento automatico.    |
| Versioni supportate      | Windows, Linux    |
| Usi tipici      | Quadro generale per l'aumento dei gradienti      |
| Come viene configurata o installata?      | In Windows LightGBM viene installato come pacchetto di Python. In Linux il file eseguibile da riga di comando si trova in `/opt/LightGBM/lightgbm`, viene installato il pacchetto R e i pacchetti di Python.     |
| Collegamenti agli esempi      | [Guida LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Strumenti correlati      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Interfaccia utente grafica per il data mining tramite R.   |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Strumento generale di data mining dell'interfaccia utente per RGeneral UI data-mining tool for R    |
| Come usarla o eseguirla      | Come strumento dell'interfaccia utente. In Windows, avviare un prompt dei comandi, `rattle()`eseguire R e quindi all'interno di R, eseguire . In Linux, connettersi con X2Go, avviare un terminale, eseguire R, quindi all'interno di R, eseguire `rattle()`. |
| Collegamenti agli esempi      | [Rattle](https://togaware.com/onepager/) |
| Strumenti correlati      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Una libreria di sistemi di apprendimento veloce, open source e out-of-core    |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Libreria generale di apprendimento automatico      |
| Come viene configurata o installata?      |  Windows: programma di installazione msi<br/>Linux: apt-get |
| Come usarla o eseguirla      | Come strumento della riga di comando nel percorso (`C:\Program Files\VowpalWabbit\vw.exe` in Windows, `/usr/bin/vw` in Linux)    |
| Collegamenti agli esempi      | [Esempi di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Strumenti correlati      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Che cos'è?   |  Raccolta di algoritmi di apprendimento automatico per le attività di data mining. Gli algoritmi possono essere applicati direttamente a un set di dati o chiamati dal codice Java. Weka contiene strumenti per la pre-elaborazione, la classificazione, la regressione, il clustering, le regole di associazione e la visualizzazione dei dati. |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Strumento generale di apprendimento automatico     |
| Come usarla o eseguirla      | In Windows, cercare Weka nel menu **Start.** Su Linux, accedi con X2Go, quindi vai a **Sviluppo applicazioni** > **Development** > **Weka**. |
| Collegamenti agli esempi      | [Esempi di Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Strumenti correlati      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Una libreria veloce, portatile e distribuita con gradient-boosting (GBDT, GBRT o GBM) per Python, R, Java, Scala, C, e altro ancora. Funziona su una singola macchina, e su Apache Hadoop e Spark.    |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Libreria generale di apprendimento automatico      |
| Come viene configurata o installata?      |  Installata con supporto GPU   |
| Come usarla o eseguirla      | Come libreria Python (2.7 e 3.5), pacchetto R e strumento`C:\dsvm\tools\xgboost\bin\xgboost.exe` da `/dsvm/tools/xgboost/xgboost` riga di comando on-path (per Windows e per Linux)    |
| Collegamenti agli esempi      | Gli esempi sono inclusi nella macchina virtuale, in `/dsvm/tools/xgboost/demo` su Linux e `C:\dsvm\tools\xgboost\demo` su Windows.   |
| Strumenti correlati      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Che cos'è?   | Motore di query SQL open source sui Big DataOpen-source SQL query engine on big data    |
| Versioni DSVM supportate      | Windows 2019, Linux  |
| Come viene configurato e installato in DSVM?      |  Installato in `/dsvm/tools/drill*` solo in modalità incorporata   |
| Usi tipici      |  Per l'esplorazione dei dati sul posto senza richiedere estrazione, trasformazione, caricamento (ETL). Eseguire query su origini dati e formati diversi, tra cui CSV, JSON, tabelle relazionali e Hadoop.     |
| Come usarlo ed eseguirlo      | Collegamento sul desktop  <br/> [Introduzione a Drill in 10 minuti](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Strumenti correlati su DSVM      |   Rattle, Weka, SQL Server Management Studio      |


