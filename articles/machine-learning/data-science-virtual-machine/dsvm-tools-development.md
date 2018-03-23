---
title: Strumenti di sviluppo della macchina virtuale per data science in Azure | Microsoft Docs
description: Strumenti di sviluppo della macchina virtuale per data science.
keywords: strumenti di data science, macchina virtuale per data science, strumenti per data science, data science per Linux
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
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 0d7ee9a54c2c11f814ce041c5c7e5bd9f2e605e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Strumenti di sviluppo nella macchina virtuale per data science

La macchina virtuale per data science offre un ambiente di produzione per lo sviluppo, raggruppando diversi strumenti e IDE comuni. Ecco alcuni strumenti offerti dalla macchina virtuale per data science. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE di uso generale      |
| Versioni supportate della macchina virtuale per data science      | Windows      |
| Usi tipici      | Sviluppo di software    |
| Come viene configurato o installato sulla macchina virtuale per data science?      | Carico di lavoro di data science (strumenti Python e R), carico di lavoro di Azure (Hadoop e Data Lake), Node.js, strumenti di SQL Server, [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai)    |
| Come usarlo o eseguirlo?      | Collegamento sul desktop (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Strumenti correlati nella macchina virtuale per data science      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 
|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE di uso generale      |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Usi tipici      | Editor di codice e integrazione di Git   |
| Come usarlo o eseguirlo?      | Collegamento sul desktop (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) in Windows, collegamento sul desktop o terminale (`code`) in Linux    |
| Strumenti correlati nella macchina virtuale per data science      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 
|    |           |
| ------------- | ------------- |
| Che cos'è?   | Client IDE per R    |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarlo o eseguirlo?      | Collegamento sul desktop (`C:\Program Files\RStudio\bin\rstudio.exe`) in Windows, collegamento sul desktop (`/usr/bin/rstudio`) in Linux      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 
|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE basato sul Web per R    |
| Versioni supportate della macchina virtuale per data science      | Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarlo o eseguirlo?      | Abilitare il servizio con _systemctl enable rstudio-server_, quindi avviare il servizio con _systemctl start rstudio-server_. È quindi possibile accedere al server RStudio all'indirizzo http://your-vm-ip:8787.       |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio Julia   |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux      |
| Usi tipici      |  Sviluppo di Julia     |
| Come usarlo o eseguirlo?      | Collegamento sul desktop (`C:\JuliaPro-0.5.1.1\Juno.bat`) in Windows, collegamento sul desktop (`/opt/JuliaPro-VERSION/Juno`) in Linux      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| Che cos'è?   | IDE client per il linguaggio Python    |
| Versioni supportate della macchina virtuale per data science      | Linux      |
| Usi tipici      |  Sviluppo R     |
| Come usarlo o eseguirlo?      | Collegamento sul desktop (`/usr/bin/pycharm`) in Linux      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento di visualizzazione dei dati interattivo e di BI    |
| Versioni supportate della macchina virtuale per data science      | Windows  |
| Usi tipici      |  Visualizzazione dei dati e creazione di dashboard   |
| Come usarlo o eseguirlo?      | Collegamento sul desktop (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Strumenti correlati nella macchina virtuale per data science      |   Visual Studio 2017, Visual Studio Code, Juno      |

