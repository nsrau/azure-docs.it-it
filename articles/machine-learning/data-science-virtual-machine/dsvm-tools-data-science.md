---
title: Strumenti per machine learning e data science
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni sugli strumenti e i Framework di apprendimento automatico preinstallati nella Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 15c3f3994b9d519a4144fa2c2e1418e92cdbd6ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87012407"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Strumenti di machine learning e data science sulle macchine virtuali di Data Science di Azure
Le macchine virtuali di Data Science di Azure (Dsvm) hanno un ampio set di strumenti e librerie per machine learning disponibili nei linguaggi più diffusi, come Python, R e Julia.

Ecco alcuni strumenti e librerie di apprendimento automatico in Dsvm.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK per Python

Vedere le informazioni di riferimento complete per [Azure Machine Learning SDK per Python](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   |   Azure Machine Learning è un servizio cloud che è possibile usare per sviluppare e distribuire modelli di machine learning. È possibile tenere traccia dei modelli durante la compilazione, il training, il ridimensionamento e la gestione con Python SDK. Distribuire modelli come contenitori ed eseguirli nel cloud, in locale o in Azure IoT Edge.   |
| Edizioni supportate     | Windows (ambiente Conda: AzureML), Linux (ambiente Conda: py36)    |
| Usi tipici      | Piattaforma di apprendimento automatico generale      |
| Come viene configurata o installata?      |  Installata con supporto GPU   |
| Come usarla o eseguirla      | Come Python SDK e nell'interfaccia della riga di comando di Azure. Attivare per l'ambiente Conda `AzureML` nell'edizione per Windows *o* per `py36` nell'edizione per Linux.      |
| Collegamenti agli esempi      | I notebook di Jupyter di esempio sono inclusi nella directory `AzureML` in notebook.  |
| Strumenti correlati      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Piattaforma di intelligenza artificiale open source che supporta Machine Learning in memoria, distribuita, veloce e scalabile.  |
| Versioni supportate      | Linux   |
| Usi tipici      | Apprendimento automatico scalabile e distribuito per utilizzo generico   |
| Come viene configurata o installata?      | H2O è installato in `/dsvm/tools/h2o`.      |
| Come usarla o eseguirla      | Connettersi alla macchina virtuale con X2Go. Avviare un nuovo terminale ed eseguire `java -jar /dsvm/tools/h2o/current/h2o.jar`. Quindi avviare un Web browser e connettersi a `http://localhost:54321`.      |
| Collegamenti agli esempi      | Gli esempi sono disponibili nella macchina virtuale in Jupyter nella directory `h2o`.      |
| Strumenti correlati      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Sono disponibili diverse altre librerie di Machine Learning in Dsvm, ad esempio il popolare `scikit-learn` pacchetto che fa parte della distribuzione di Anaconda Python per dsvm. Per estrarre l'elenco dei pacchetti disponibili in Python, R e Julia, eseguire i rispettivi strumenti di gestione dei pacchetti.

## <a name="lightgbm"></a>LightGBM

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Un Framework rapido, distribuito e ad alte prestazioni (Gradient, boosting, GBM o MART) basato su algoritmi di albero delle decisioni. Viene usato per il ranking, la classificazione e molte altre attività di machine learning.    |
| Versioni supportate      | Windows, Linux    |
| Usi tipici      | Framework per l'incremento delle sfumature per utilizzo generico      |
| Come viene configurata o installata?      | In Windows LightGBM viene installato come pacchetto di Python. In Linux il file eseguibile da riga di comando si trova in `/opt/LightGBM/lightgbm`, viene installato il pacchetto R e i pacchetti di Python.     |
| Collegamenti agli esempi      | [Guida a LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Strumenti correlati      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   |   Interfaccia utente grafica per data mining usando R.   |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Strumento di data mining generale dell'interfaccia utente per R    |
| Come usarla o eseguirla      | Come strumento dell'interfaccia utente. In Windows avviare un prompt dei comandi, eseguire R, quindi all'interno di R eseguire `rattle()` . In Linux connettersi con X2Go, avviare un terminale, eseguire R, quindi all'interno di R eseguire `rattle()` . |
| Collegamenti agli esempi      | [Rattle](https://togaware.com/onepager/) |
| Strumenti correlati      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   |   Una libreria di sistema di formazione rapida, open source e out-of-core    |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Libreria di Machine Learning generale      |
| Come viene configurata o installata?      |  Windows: programma di installazione MSI<br/>Linux: apt-get |
| Come usarla o eseguirla      | Come strumento della riga di comando nel percorso (`C:\Program Files\VowpalWabbit\vw.exe` in Windows, `/usr/bin/vw` in Linux)    |
| Collegamenti agli esempi      | [Esempi di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Strumenti correlati      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   |  Raccolta di algoritmi di machine learning per le attività di data mining. Gli algoritmi possono essere applicati direttamente a un set di dati o chiamati dal codice Java. Weka contiene strumenti per la pre-elaborazione, la classificazione, la regressione, il clustering, le regole di associazione e la visualizzazione dei dati. |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Strumento di apprendimento automatico generale     |
| Come usarla o eseguirla      | In Windows cercare WEKA nel menu **Start** . In Linux accedere con x2go e quindi passare ad **Applications**  >  **Development**  >  **WEKA**. |
| Collegamenti agli esempi      | [Esempi di Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Strumenti correlati      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   |   Una libreria Fast, Portable e Distributed Gradient boosting (Gradient, boosting o GBM) per Python, R, Java, scala, C++ e altro ancora. Viene eseguito in un singolo computer e in Apache Hadoop e Spark.    |
| Edizioni supportate     | Windows, Linux     |
| Usi tipici      | Libreria di Machine Learning generale      |
| Come viene configurata o installata?      |  Installata con supporto GPU   |
| Come usarla o eseguirla      | Come libreria Python (2,7 e 3,5), pacchetto R e strumento da riga di comando su percorso ( `C:\dsvm\tools\xgboost\bin\xgboost.exe` per Windows e `/dsvm/tools/xgboost/xgboost` per Linux)    |
| Collegamenti agli esempi      | Gli esempi sono inclusi nella macchina virtuale, in `/dsvm/tools/xgboost/demo` su Linux e `C:\dsvm\tools\xgboost\demo` su Windows.   |
| Strumenti correlati      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Motore di query SQL Open Source su Big Data    |
| Versioni di DSVM supportate      | Windows 2019, Linux  |
| Come viene configurato e installato in DSVM?      |  Installato in `/dsvm/tools/drill*` solo in modalità incorporata   |
| Usi tipici      |  Per l'esplorazione dei dati sul posto senza richiedere l'estrazione, la trasformazione e il caricamento (ETL). Eseguire query su diverse origini dati e formati, tra cui CSV, JSON, tabelle relazionali e Hadoop.     |
| Come usarlo ed eseguirlo      | Collegamento sul desktop  <br/> [Introduzione a Drill in 10 minuti](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Strumenti correlati in DSVM      |   Rattle, Weka, SQL Server Management Studio      |


