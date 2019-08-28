---
title: Strumenti di sviluppo della macchina virtuale per data science in Azure | Microsoft Docs
description: Informazioni sugli strumenti e gli ambienti di sviluppo integrati preinstallati nella Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 8f9dad0fb007945b69b75daadfdb12f61dc4defb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074294"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Strumenti di sviluppo nella macchina virtuali per data science

Il Data Science Virtual Machine (DSVM) raggruppa diversi strumenti diffusi in un ambiente IDE (highly produttivi Integrated Development Environment). Ecco alcuni strumenti offerti dalla macchina virtuale per data science.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE per utilizzo generico      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Sviluppo di software    |
| Come viene configurato e installato in DSVM?      | Carico di lavoro di data science (strumenti Python e R), carico di lavoro di Azure (Hadoop e Data Lake), Node.js, strumenti di SQL Server, [Azure Machine Learning per Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Come usarlo ed eseguirlo      | Collegamento sul desktop`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`()    |
| Strumenti correlati in DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE per utilizzo generico      |
| Versioni di DSVM supportate      | Windows, Linux     |
| Usi tipici      | Editor di codice e integrazione di Git   |
| Come usarlo ed eseguirlo      | Collegamento sul desktop`C:\Program Files (x86)\Microsoft VS Code\Code.exe`() in Windows, collegamento al desktop o`code`terminale () in Linux    |
| Strumenti correlati in DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio R   |
| Versioni di DSVM supportate      | Windows, Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarlo ed eseguirlo      | Collegamento sul desktop`C:\Program Files\RStudio\bin\rstudio.exe`() in Windows, collegamento sul`/usr/bin/rstudio`desktop () in Linux      |
| Strumenti correlati in DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio R   |
| Che cos'è?   | IDE basato sul Web per R    |
| Versioni di DSVM supportate      | Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarlo ed eseguirlo      | Abilitare il servizio con _systemctl enable rstudio-server_, quindi avviare il servizio con _systemctl Start rstudio-server_. Quindi accedere a rstudio server all'indirizzo http:\//your-VM-IP: 8787.       |
| Strumenti correlati in DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio Julia   |
| Versioni di DSVM supportate      | Windows, Linux      |
| Usi tipici      |  Sviluppo di Julia     |
| Come usarlo ed eseguirlo      | Collegamento sul desktop`C:\JuliaPro-0.5.1.1\Juno.bat`() in Windows, collegamento sul`/opt/JuliaPro-VERSION/Juno`desktop () in Linux      |
| Strumenti correlati in DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio Python    |
| Versioni di DSVM supportate      | Linux      |
| Usi tipici      |  Sviluppo Python     |
| Come usarlo ed eseguirlo      | Collegamento sul desktop`/usr/bin/pycharm`() in Linux      |
| Strumenti correlati in DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Visualizzazione interattiva dei dati e strumento BI    |
| Versioni di DSVM supportate      | Windows  |
| Usi tipici      |  Visualizzazione dei dati e creazione di dashboard   |
| Come usarlo ed eseguirlo      | Collegamento sul desktop`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| Strumenti correlati in DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

