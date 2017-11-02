---
title: Librerie client necessarie per la connessione ad Azure Analysis Services | Microsoft Docs
description: Vengono illustrate le librerie client necessarie per la connessione di applicazioni e strumenti client ad Azure Analysis Services
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 9c9ceea9cc7fa9b6e4a63660bfa988bf50c0f5ae
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
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
