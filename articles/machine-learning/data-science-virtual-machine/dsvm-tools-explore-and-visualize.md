---
title: Strumenti per la visualizzazione e l'esplorazione dei dati in Azure | Microsoft Docs
description: Strumenti per la visualizzazione e l'esplorazione dei dati per la macchina virtuale per data science.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma;bradsev
ms.openlocfilehash: 96f8d1bcf5a7e51823ed55a1b924d1397eba9da5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Strumenti per la visualizzazione e l'esplorazione dei dati nella macchina virtuale per data science

Un passaggio fondamentale del data science consiste nel comprendere i dati. Gli strumenti di visualizzazione ed esplorazione dei dati consentono di velocizzare la comprensione dei dati. Ecco alcuni strumenti offerti dalla macchina virtuale per data science che semplificano questo passaggio fondamentale. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Che cos'è?   | Motore di query SQL open source su Big data    |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux  |
| Come viene configurata o installata sulla macchina virtuale per data science?      |  Installato in `/dsvm/tools/drill*` solo in modalità incorporata   |
| Usi tipici      |  Esplorazione dei dati locali senza la necessità di usare ETL. Esecuzione di query su origini dati e formati diversi tra cui CSV, JSON, tabelle relazionali, Hadoop     |
| Come usarla o eseguirla?      | Collegamento sul desktop  <br/> [Introduzione a Drill in 10 minuti](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Strumenti correlati nella macchina virtuale per data science      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Che cos'è?   |  Weka è una raccolta di algoritmi per machine learning per attività di data mining. Gli algoritmi possono essere applicati direttamente a un set di dati o chiamati dal codice Java. Weka contiene strumenti per la pre-elaborazione, la classificazione, la regressione, il clustering, le regole di associazione e la visualizzazione dei dati. |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Strumento ML generale     |
| Come usarla o eseguirla?      | In Windows cercare Weka nel menu Start. In Linux accedere con X2Go, quindi passare ad Applications (Applicazioni) -> Development (Sviluppo) -> Weka. |
| Collegamenti agli esempi      | [Esempi di Weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Strumenti correlati nella macchina virtuale per data science      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Un'interfaccia utente grafica per data mining con R   |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Strumento di data mining dell'interfaccia utente generale per R    |
| Come usarla o eseguirla?      | Strumento dell'interfaccia utente. In Windows avviare un prompt dei comandi, eseguire R, quindi all'interno di R eseguire `rattle()`. In Linux connettersi con X2Go, avviare un terminale, eseguire R, quindi all'interno di R eseguire `rattle()`. |
| Collegamenti agli esempi      | [Rattle](https://togaware.com/onepager/) |
| Strumenti correlati nella macchina virtuale per data science      |LightGBM, Weka, Xgboost   |

## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento di visualizzazione dei dati interattivo e di BI    |
| Versioni supportate della macchina virtuale per data science      | Windows  |
| Usi tipici      |  Visualizzazione dei dati e creazione di dashboard   |
| Come usarla o eseguirla?      | Collegamento sul desktop (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2017, Visual Studio Code, Juno      |

