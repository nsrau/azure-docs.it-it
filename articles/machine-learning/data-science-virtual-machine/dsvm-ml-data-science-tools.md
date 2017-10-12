---
title: Strumenti per data science e machine learning in Azure | Microsoft Docs
description: Strumenti per machine learning e data science
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: c772fa16b94d09e0fc0450ce86503553c26f8a24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-and-data-science-tools"></a>Strumenti per machine learning e data science
La macchina virtuale per data science offre un ampio set di strumenti e librerie per machine learning disponibili nei linguaggi più diffusi, ad esempio Python, R, Julia. 

Ecco alcuni strumenti di machine learning e librerie nella macchina virtuale per data science. 

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Che cos'è?   |    Libreria di gradient boosting veloce, portatile e distribuita per Python, R, Java, Scala, C++ e altro ancora. Viene eseguita su un solo computer, Hadoop, Spark    |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Libreria ML generale      |
| Come viene configurata o installata sulla macchina virtuale per data science?      |  Installata con supporto GPU   |
| Come usarla o eseguirla?      | Come pacchetto R della libreria di Python 2.7 e 3.5 e sullo strumento della riga di comando del percorso (`C:\dsvm\tools\xgboost\bin\xgboost.exe` per Windows, `/dsvm/tools/xgboost/xgboost` per Linux)    |
| Collegamenti agli esempi      | Gli esempi sono inclusi nella macchina virtuale, in `/dsvm/tools/xgboost/demo` su Linux e `C:\dsvm\tools\xgboost\demo` su Windows   |
| Strumenti correlati nella macchina virtuale per data science      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Vowpal Wabbit, noto anche come "VW", è una libreria del sistema di apprendimento esterno rapido e open source    |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Libreria ML generale      |
| Come viene configurata o installata sulla macchina virtuale per data science?      |  Windows: programma di installazione msi, Linux: apt-get |
| Come usarla o eseguirla?      | Come strumento della riga di comando nel percorso (`C:\Program Files\VowpalWabbit\vw.exe` in Windows, `/usr/bin/vw` in Linux)    |
| Collegamenti agli esempi      | [Esempi di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Strumenti correlati nella macchina virtuale per data science      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Che cos'è?   |  Weka è una raccolta di algoritmi per machine learning per attività di data mining. Gli algoritmi possono essere applicati direttamente a un set di dati o chiamati dal codice Java. Weka contiene strumenti per la pre-elaborazione, la classificazione, la regressione, il clustering, le regole di associazione e la visualizzazione dei dati. |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Strumento ML generale     |
| Come usarla o eseguirla?      | In Windows cercare Weka nel menu Start. In Linux accedere con X2Go, quindi passare ad Applications (Applicazioni) -> Development (Sviluppo) -> Weka. |
| Collegamenti agli esempi      | [Esempi di Weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Strumenti correlati nella macchina virtuale per data science      |LightGBM, Rattle, XGBooost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Un'interfaccia utente grafica per data mining con R   |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Strumento di data mining dell'interfaccia utente generale per R    |
| Come usarla o eseguirla?      | Strumento dell'interfaccia utente. In Windows avviare un prompt dei comandi, eseguire R, quindi all'interno di R eseguire `rattle()`. In Linux connettersi con X2Go, avviare un terminale, eseguire R, quindi all'interno di R eseguire `rattle()`. |
| Collegamenti agli esempi      | [Rattle](https://togaware.com/onepager/) |
| Strumenti correlati nella macchina virtuale per data science      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Che cos'è?   | Un framework di gradient boosting veloce, distribuito e ad alte prestazioni che si basa sugli algoritmi dell'albero delle decisioni, usati per la collocazione, la classificazione e molte altre attività di machine learning.    |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux    |
| Usi tipici      | Framework di gradient boosting generico      |
| Come viene configurata o installata sulla macchina virtuale per data science?      | In Windows LightGBM viene installato come pacchetto di Python. In Linux il file eseguibile da riga di comando si trova in `/opt/LightGBM/lightgbm`, viene installato il pacchetto R e i pacchetti di Python.     |
| Collegamenti agli esempi      | [Guida a LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Strumenti correlati nella macchina virtuale per data science      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Che cos'è?   | Una piattaforma IA open source che supporta machine learning in memoria, distribuito, veloce e scalabile  |
| Versioni supportate della macchina virtuale per data science      | Linux   |
| Usi tipici      | ML scalabile e distribuito generico   |
| Come viene configurata o installata sulla macchina virtuale per data science?      | H2O è installato in `/dsvm/tools/h2o`.      |
| Come usarla o eseguirla?      | Connettersi alla macchina virtuale con X2Go. Avviare un nuovo terminale ed eseguire `java -jar /dsvm/tools/h2o/current/h2o.jar`. Quindi avviare un Web browser e connettersi a `http://localhost:54321`.      |
| Collegamenti agli esempi      | Gli esempi sono disponibili nella macchina virtuale in Jupyter nella directory `h2o`.      |
| Strumenti correlati nella macchina virtuale per data science      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Esistono molte altre librerie ML sulla macchina virtuale per data science come il noto pacchetto `scikit-learn` incluso nella distribuzione Anaconda Python installata nella macchina virtuale per data science. Assicurarsi di controllare l'elenco dei pacchetti disponibili in Python, R e Julia eseguendo i rispettivi strumenti di gestione dei pacchetti. 
