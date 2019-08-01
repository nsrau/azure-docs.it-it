---
title: Strumenti per la visualizzazione e l'esplorazione dei dati in Azure | Microsoft Docs
description: Strumenti per la visualizzazione e l'esplorazione dei dati per la macchina virtuale per data science.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 31b05ec4fa68c3d4804000caee94b62432bdaed9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557759"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Strumenti per la visualizzazione e l'esplorazione dei dati nella macchina virtuale per data science

Un passaggio fondamentale del data science consiste nel comprendere i dati. Gli strumenti di visualizzazione ed esplorazione dei dati consentono di velocizzare la comprensione dei dati. Ecco alcuni strumenti disponibili in DSVM che rendono più semplice questo passaggio chiave. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Informazioni   | Motore di query SQL open source su Big data    |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux  |
| Come viene configurata o installata sulla macchina virtuale per data science?      |  Installato in `/dsvm/tools/drill*` solo in modalità incorporata   |
| Usi tipici      |  Esplorazione dei dati locali senza la necessità di usare ETL. Esecuzione di query su origini dati e formati diversi tra cui CSV, JSON, tabelle relazionali, Hadoop     |
| Come usarla o eseguirla?      | Collegamento sul desktop  <br/> [Introduzione a Drill in 10 minuti](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Strumenti correlati nella macchina virtuale per data science      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Informazioni   |  Weka è una raccolta di algoritmi per machine learning per attività di data mining. Gli algoritmi possono essere applicati direttamente a un set di dati o chiamati dal codice Java. Weka contiene strumenti per la pre-elaborazione, la classificazione, la regressione, il clustering, le regole di associazione e la visualizzazione dei dati. |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Strumento ML generale     |
| Come usarla o eseguirla?      | In Windows cercare Weka nel menu Start. In Linux accedere con X2Go, quindi passare ad Applications-> Development-> weka. |
| Collegamenti agli esempi      | [Esempi di Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Strumenti correlati nella macchina virtuale per data science      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Informazioni   |   Un'interfaccia utente grafica per data mining con R   |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Strumento di data mining dell'interfaccia utente generale per R    |
| Come usarla o eseguirla?      | Strumento dell'interfaccia utente. In Windows avviare un prompt dei comandi, eseguire R, quindi all'interno di R eseguire `rattle()`. In Linux connettersi con X2Go, avviare un terminale, eseguire R, quindi all'interno di R eseguire `rattle()`. |
| Collegamenti agli esempi      | [Rattle](https://togaware.com/onepager/) |
| Strumenti correlati nella macchina virtuale per data science      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Informazioni   | Strumento di visualizzazione dei dati interattivo e di BI    |
| Versioni supportate della macchina virtuale per data science      | Windows  |
| Usi tipici      |  Visualizzazione dei dati e creazione di dashboard   |
| Come usarla o eseguirla?      | Collegamento sul desktop (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2019, Visual Studio Code, Juno      |

