---
title: Strumenti di sviluppo
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni sugli strumenti e sugli ambienti di sviluppo integrati disponibili nella macchina virtuale di data science.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282682"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Strumenti di sviluppo nella macchina virtuale di analisi scientifica dei dati di AzureDevelopment tools on the Azure Data Science Virtual Machine

Data Science Virtual Machine (DSVM) include diversi strumenti popolari in un ambiente di sviluppo integrato (IDE) altamente produttivo. Ecco alcuni strumenti offerti dalla macchina virtuale per data science.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE generico      |
| Versioni DSVM supportate      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Usi tipici      | Sviluppo software    |
| Come viene configurato e installato in DSVM?      | Carico di lavoro di data science (strumenti Python e R), carico di lavoro di Azure (Hadoop e Data Lake), Node.js, strumenti di SQL Server, [Azure Machine Learning per Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Come usarlo ed eseguirlo      | Collegamento sul`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`desktop ( ). Graficamente, aprire Visual Studio utilizzando l'icona del desktop o il menu **Start.** Cercare i programmi premendo il tasto WINDOWS + S e poi **Visual Studio**. È quindi possibile creare progetti in linguaggi come C#, Python, R e Node.js.   |
| Strumenti correlati su DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Potrebbe essere visualizzato un messaggio indicante che il periodo di valutazione è scaduto. Immettere le credenziali dell'account Microsoft oppure creare un nuovo account gratuito per poter accedere a Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE generico      |
| Versioni DSVM supportate      | Windows, Linux     |
| Usi tipici      | Editor di codice e integrazione di Git   |
| Come usarlo ed eseguirlo      | Collegamento sul`C:\Program Files (x86)\Microsoft VS Code\Code.exe`desktop ( ) in`code`Windows, collegamento sul desktop o terminale ( ) in Linux    |
| Strumenti correlati su DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per la lingua R   |
| Versioni DSVM supportate      | Windows, Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarlo ed eseguirlo      | Collegamento sul`C:\Program Files\RStudio\bin\rstudio.exe`desktop ( )`/usr/bin/rstudio`su Windows, collegamento sul desktop ( ) su Linux      |
| Strumenti correlati su DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per la lingua R   |
| Che cos'è?   | IDE basato sul Web per R    |
| Versioni DSVM supportate      | Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarlo ed eseguirlo      | Abilitare il servizio con _systemctl enable rstudio-server_, quindi avviare il servizio con _systemctl start rstudio-server_. Accedere quindi a RStudio Server\/all'indirizzo http: /your-vm-ip:8787.       |
| Strumenti correlati su DSVM      |   Visual Studio, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio Julia   |
| Versioni DSVM supportate      | Windows, Linux      |
| Usi tipici      |  Sviluppo di Julia     |
| Come usarlo ed eseguirlo      | Collegamento sul`C:\JuliaPro-0.5.1.1\Juno.bat`desktop ( )`/opt/JuliaPro-VERSION/Juno`su Windows, collegamento sul desktop ( ) su Linux      |
| Strumenti correlati su DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio Python    |
| Versioni DSVM supportate      | Windows 2019, Linux      |
| Usi tipici      |  Sviluppo Python     |
| Come usarlo ed eseguirlo      | Collegamento sul`C:\Program Files\tk`desktop ( ) in Windows. Collegamento sul`/usr/bin/pycharm`desktop ( ) su Linux      |
| Strumenti correlati su DSVM      |   Visual Studio, Visual Studio Code, RStudio      |
