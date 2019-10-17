---
title: Strumenti per la visualizzazione e l'esplorazione dei dati
titleSuffix: Azure Data Science Virtual Machine
description: Strumenti per la visualizzazione e l'esplorazione dei dati per la macchina virtuale per data science.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: a18c374df0cad7994af855089f1096d52fe30ce2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330697"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Strumenti per l'esplorazione e la visualizzazione dei dati nel Data Science Virtual Machine di Azure

In data science, la chiave consiste nel comprendere i dati. Gli strumenti di visualizzazione ed esplorazione dei dati consentono di velocizzare la comprensione dei dati. Gli strumenti seguenti, disponibili nella Data Science Virtual Machine (DSVM), rendono più semplice questo passaggio chiave.

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Che cos'è?   | Motore di query SQL Open Source su Big Data    |
| Versioni di DSVM supportate      | Windows, Linux  |
| Come viene configurato e installato in DSVM?      |  Installato in `/dsvm/tools/drill*` solo in modalità incorporata   |
| Usi tipici      |  Per l'esplorazione dei dati sul posto senza richiedere l'estrazione, la trasformazione e il caricamento (ETL). Eseguire query su diverse origini dati e formati, tra cui CSV, JSON, tabelle relazionali e Hadoop.     |
| Come usarlo ed eseguirlo      | Collegamento sul desktop  <br/> [Introduzione a Drill in 10 minuti](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Strumenti correlati in DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Che cos'è?   | Visualizzazione interattiva dei dati e strumento BI    |
| Versioni di DSVM supportate      | Windows  |
| Usi tipici      |  Visualizzazione dei dati e creazione di dashboard   |
| Come usarlo ed eseguirlo      | Collegamento al desktop (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`) o semplicemente eseguito dal menu **Start** .      |
| Strumenti correlati in DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

> [!NOTE]
> Per accedere a Power BI, è necessario un account Microsoft Office 365.


## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Che cos'è?   |   Interfaccia utente grafica (GUI) per data mining usando R   |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Strumento di data mining dell'interfaccia utente generale per R    |
| Come usarlo ed eseguirlo      | Strumento dell'interfaccia utente. In Windows aprire un prompt dei comandi, eseguire R, quindi all'interno di R eseguire `rattle()`. In Linux connettersi con X2Go, avviare un terminale, eseguire R, quindi all'interno di R eseguire `rattle()`. |
| Collegamenti agli esempi      | [Rattle](https://togaware.com/onepager/) |
| Strumenti correlati in DSVM      |LightGBM, Weka, Xgboost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Che cos'è?   |  Raccolta di algoritmi di machine learning per le attività data mining. Questi algoritmi possono essere applicati direttamente a un set di dati o chiamati dal codice Java. WEKA contiene strumenti per la pre-elaborazione dei dati, la classificazione, la regressione, il clustering, le regole di associazione e la visualizzazione. |
| Edizioni supportate della macchina virtuale per data science     | Windows, Linux     |
| Usi tipici      | Strumento di apprendimento automatico generale     |
| Come usarlo ed eseguirlo      | In Windows cercare Weka nel menu Start. In Linux accedere con X2Go e quindi fare clic su Applications > Development > weka. |
| Collegamenti a esempi      | [Esempi di Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Strumenti correlati in DSVM      |LightGBM, Rattle, Xgboost   |




