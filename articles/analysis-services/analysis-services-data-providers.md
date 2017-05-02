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
ms.date: 04/14/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 71f85c483f6b87ac34c003ac3695a85396ca945b
ms.lasthandoff: 04/17/2017


---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Librerie client per la connessione ad Azure Analysis Services

Le librerie client sono necessarie per la connessione di applicazioni e strumenti client ai server di Analysis Services. 

Analysis Services usa tre librerie client. ADOMD.NET e Analysis Services Management Objects (AMO) sono librerie client gestite. Il provider Analysis Services OLE DB (MSOLAP DLL) è una libreria client nativa. In genere, vengono installate tutte e tre nello stesso momento. Azure Analysis Services richiede le versioni più recenti. 

Le applicazioni client di Microsoft, come ad esempio Power BI Desktop ed Excel, installano tutte e tre le librerie client. Tuttavia, a seconda della versione di Excel o a seconda del fatto che ogni mese siano aggiornate versioni più recenti di Excel e Power BI Desktop oppure no, le librerie client installate potrebbero non essere aggiornate alle versioni più recenti richieste da Azure Analysis Services. Lo stesso vale per le applicazioni personalizzate o per le altre interfacce come AsCmd, TOM e ADOMD.NET. Queste applicazioni richiedono l'installazione manuale delle librerie. Le librerie client per l'installazione manuale sono incluse nei Feature Pack di SQL Server come pacchetti distribuibili; tuttavia, tali pacchetti sono associati alla versione di SQL Server e potrebbero non essere quelli più recenti.  

Le librerie client per le connessioni client sono diverse dai provider di dati necessari per connettersi da un server di Azure Analysis Services a un'origine dati. Per ulteriori informazioni sulle connessioni alle origini dati, vedere [Connessioni alle origini dati](analysis-services-datasource.md).

## <a name="download-the-latest-preview-client-libraries"></a>Scaricare le librerie client **in anteprima** più recenti  
Usare le librerie client seguenti per ottenere le correzioni di bug e gli aggiornamenti più recenti. Queste librerie sono consigliate per la connessione all'anteprima di Azure Analysis Services o a SQL Server vNext Analysis Services.

[Anteprima di MSOLAP (amd64)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[Anteprima di MSOLAP (x86)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[Anteprima AMO](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[Anteprima ADOMD](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-client-libraries"></a>Scaricare le librerie client **RTM** più recenti  
Usare le librerie client seguenti se ci si trova in un ambiente di produzione e sono richieste versioni completamente rilasciate e supportate.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Passaggi successivi
[Connettersi a un server di Azure Analysis Services](analysis-services-connect.md).

