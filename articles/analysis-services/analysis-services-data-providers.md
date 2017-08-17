---
title: Librerie client necessarie per la connessione ad Azure Analysis Services | Microsoft Docs
description: Vengono illustrate le librerie client necessarie per la connessione di applicazioni e strumenti client ad Azure Analysis Services
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/14/2016
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 949f34158f4fc8ee6d86cf3dd408c3c903234109
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Librerie client per la connessione ad Azure Analysis Services

Le librerie client sono necessarie per la connessione di applicazioni e strumenti client ai server di Analysis Services. 

Analysis Services usa tre librerie client. ADOMD.NET e Analysis Services Management Objects (AMO) sono librerie client gestite. Il provider Analysis Services OLE DB (MSOLAP DLL) è una libreria client nativa. In genere, vengono installate tutte e tre nello stesso momento. Azure Analysis Services richiede le versioni più recenti. 

Le applicazioni client di Microsoft, come ad esempio Power BI Desktop ed Excel, installano tutte e tre le librerie client. A seconda della versione o della frequenza degli aggiornamenti, le librerie client potrebbero tuttavia non essere le versioni più recenti richieste da Azure Analysis Services. Lo stesso vale per le applicazioni personalizzate o per le altre interfacce come AsCmd, TOM e ADOMD.NET. Queste applicazioni richiedono l'installazione manuale delle librerie. Le librerie client per l'installazione manuale sono incluse nei Feature Pack di SQL Server come pacchetti distribuibili. Queste librerie client sono tuttavia collegate alla versione di SQL Server e potrebbero non essere le più recenti.  

Le librerie client per le connessioni client sono diverse dai provider di dati necessari per connettersi da un server di Azure Analysis Services a un'origine dati. Per ulteriori informazioni sulle connessioni alle origini dati, vedere [Connessioni alle origini dati](analysis-services-datasource.md).

## <a name="download-the-latest-client-libraries"></a>Scaricare le librerie client più recenti  
Usare le librerie client seguenti se ci si trova in un ambiente di produzione e sono richieste versioni completamente rilasciate e supportate.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Passaggi successivi
[Connettersi con Excel](analysis-services-connect-excel.md)    
[Stabilire la connessione con Power BI](analysis-services-connect-pbi.md)

