---
title: Strumenti di sviluppo
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni sugli strumenti e gli ambienti di sviluppo integrati disponibili nell'Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 76a550e95de24bf65b9b6097dd332e535da5b1c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330723"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Strumenti di sviluppo nel Data Science Virtual Machine di Azure

Il Data Science Virtual Machine (DSVM) raggruppa diversi strumenti diffusi in un ambiente IDE (highly produttivi Integrated Development Environment). Ecco alcuni strumenti offerti dalla macchina virtuale per data science.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE per utilizzo generico      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Sviluppo di software    |
| Come viene configurato e installato in DSVM?      | Carico di lavoro di data science (strumenti Python e R), carico di lavoro di Azure (Hadoop e Data Lake), Node.js, strumenti di SQL Server, [Azure Machine Learning per Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Come usarlo ed eseguirlo      | Collegamento sul desktop (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`). Graficamente, aprire Visual Studio usando l'icona del desktop o il menu **Start** . Cercare i programmi premendo il tasto WINDOWS + S e poi **Visual Studio**. È quindi possibile creare progetti in linguaggi come C#, Python, R e Node.js.   |
| Strumenti correlati in DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Potrebbe essere visualizzato un messaggio indicante che il periodo di valutazione è scaduto. Immettere le credenziali dell'account Microsoft oppure creare un nuovo account gratuito per poter accedere a Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE per utilizzo generico      |
| Versioni di DSVM supportate      | Windows, Linux     |
| Usi tipici      | Editor di codice e integrazione di Git   |
| Come usarlo ed eseguirlo      | Collegamento sul desktop (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) in Windows, collegamento al desktop o terminale (`code`) in Linux    |
| Strumenti correlati in DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio R   |
| Versioni di DSVM supportate      | Windows, Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarlo ed eseguirlo      | Collegamento sul desktop (`C:\Program Files\RStudio\bin\rstudio.exe`) in Windows, collegamento sul desktop (`/usr/bin/rstudio`) in Linux      |
| Strumenti correlati in DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio R   |
| Che cos'è?   | IDE basato sul Web per R    |
| Versioni di DSVM supportate      | Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarlo ed eseguirlo      | Abilitare il servizio con _systemctl enable rstudio-server_, quindi avviare il servizio con _systemctl Start rstudio-server_. Quindi accedere a RStudio server all'indirizzo http: \//your-VM-IP: 8787.       |
| Strumenti correlati in DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio Julia   |
| Versioni di DSVM supportate      | Windows, Linux      |
| Usi tipici      |  Sviluppo di Julia     |
| Come usarlo ed eseguirlo      | Collegamento sul desktop (`C:\JuliaPro-0.5.1.1\Juno.bat`) in Windows, collegamento sul desktop (`/opt/JuliaPro-VERSION/Juno`) in Linux      |
| Strumenti correlati in DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio Python    |
| Versioni di DSVM supportate      | Linux      |
| Usi tipici      |  Sviluppo Python     |
| Come usarlo ed eseguirlo      | Collegamento sul desktop (`/usr/bin/pycharm`) in Linux      |
| Strumenti correlati in DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Visualizzazione interattiva dei dati e strumento BI    |
| Versioni di DSVM supportate      | Windows  |
| Usi tipici      |  Visualizzazione dei dati e creazione di dashboard   |
| Come usarlo ed eseguirlo      | Collegamento sul desktop (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Strumenti correlati in DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

