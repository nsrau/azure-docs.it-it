---
title: Strumenti di sviluppo della macchina virtuale per data science in Azure | Microsoft Docs
description: Informazioni su strumenti e ambienti di sviluppo integrato preinstallati in Data Science Virtual Machine.
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
ms.openlocfilehash: 3b8eaae63f0e316d82dd5a1238a802eefd756d9e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557799"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Strumenti di sviluppo nella macchina virtuali per data science

La macchina virtuale per data science offre un ambiente di produzione per lo sviluppo, raggruppando diversi strumenti e IDE comuni. Ecco alcuni strumenti offerti dalla macchina virtuale per data science. 

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Informazioni   | IDE di uso generale      |
| Versioni supportate della macchina virtuale per data science      | Windows      |
| Usi tipici      | Sviluppo di software    |
| Come viene configurata o installata sulla macchina virtuale per data science?      | Carico di lavoro di data science (strumenti Python e R), carico di lavoro di Azure (Hadoop e Data Lake), Node.js, strumenti di SQL Server, [Azure Machine Learning per Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Come usarla o eseguirla?      | Collegamento sul desktop (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`)    |
| Strumenti correlati nella macchina virtuale per data science      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Informazioni   | IDE di uso generale      |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Usi tipici      | Editor di codice e integrazione di Git   |
| Come usarla o eseguirla?      | Collegamento sul desktop (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) in Windows, collegamento sul desktop o terminale (`code`) in Linux    |
| Strumenti correlati nella macchina virtuale per data science      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Informazioni   | Client IDE per R    |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarla o eseguirla?      | Collegamento sul desktop (`C:\Program Files\RStudio\bin\rstudio.exe`) in Windows, collegamento sul desktop (`/usr/bin/rstudio`) in Linux      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 

|    |           |
| ------------- | ------------- |
| Informazioni   | IDE basato sul Web per R    |
| Versioni supportate della macchina virtuale per data science      | Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarla o eseguirla?      | Abilitare il servizio con _systemctl enable rstudio-server_, quindi avviare il servizio con _systemctl start rstudio-server_. È quindi possibile accedere a rstudio server all'indirizzo http:\//your-VM-IP: 8787.       |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Informazioni   | IDE client per il linguaggio Julia   |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux      |
| Usi tipici      |  Sviluppo di Julia     |
| Come usarla o eseguirla?      | Collegamento sul desktop (`C:\JuliaPro-0.5.1.1\Juno.bat`) in Windows, collegamento sul desktop (`/opt/JuliaPro-VERSION/Juno`) in Linux      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Informazioni   | IDE client per il linguaggio Python    |
| Versioni supportate della macchina virtuale per data science      | Linux      |
| Usi tipici      |  Sviluppo Python     |
| Come usarla o eseguirla?      | Collegamento sul desktop (`/usr/bin/pycharm`) in Linux      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 

|    |           |
| ------------- | ------------- |
| Informazioni   | Strumento di visualizzazione dei dati interattivo e di BI    |
| Versioni supportate della macchina virtuale per data science      | Windows  |
| Usi tipici      |  Visualizzazione dei dati e creazione di dashboard   |
| Come usarla o eseguirla?      | Collegamento sul desktop (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2019, Visual Studio Code, Juno      |

