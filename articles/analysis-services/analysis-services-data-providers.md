---
title: Provider di dati necessari per le connessioni client ad Azure Analysis Services | Microsoft Docs
description: Descrive i provider di dati necessari per connettere i client ad Azure Analysis Services
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
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 197e32058908533bc46b2ea2e38ff521a4a1fa3f
ms.lasthandoff: 04/07/2017


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Provider di dati per la connessione ad Azure Analysis Services

Le applicazioni client richiedono provider di dati, chiamati anche librerie client, per connettersi ai server di Analysis Services. 

Analysis Services usa tre provider di dati. ADOMD.NET e Analysis Services Management Objects (AMO) sono provider di dati gestiti. Il provider Analysis Services OLE DB (MSOLAP DLL) è un provider di dati nativo. In genere, tutti e tre i provider vengono installati nello stesso momento. Azure Analysis Services richiede le versioni più recenti dei provider di dati. 

Le applicazioni client di Microsoft, ad esempio Power BI Desktop ed Excel, installano tutti e tre i provider di dati. Tuttavia, a seconda della versione di Excel o a seconda se ogni mese siano aggiornate versioni più recenti di Excel e Power BI Desktop, i provider di dati installati potrebbero non essere aggiornati alle versioni più recenti richieste da Azure Analysis Services. Lo stesso vale per le applicazioni personalizzate o per le altre interfacce come AsCmd, TOM e ADOMD.NET. Queste applicazioni richiedono l'installazione manuale dei provider. I provider di dati per l'installazione manuale sono inclusi nei Feature Pack di SQL Server come pacchetti distribuibili; tuttavia, questi vengono associati alla versione di SQL Server e potrebbero non essere quelli più recenti.  

I provider di dati per le connessioni client sono diversi da quelli necessari per connettersi da un server di Azure Analysis Services a un'origine dati. Per ulteriori informazioni sulle connessioni alle origini dati, vedere [Connessioni alle origini dati](analysis-services-datasource.md).

## <a name="download-the-latest-preview-data-providers"></a>Scaricare i provider di dati **in anteprima** più recenti  
Usare il provider di dati seguenti per ottenere le correzioni di bug e gli aggiornamenti più recenti. Questi provider di dati sono consigliati per la connessione all'anteprima di Azure Analysis Services o a SQL Server vNext Analysis Services.

[Anteprima di MSOLAP (amd64)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[Anteprima di MSOLAP (x86)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[Anteprima AMO](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[Anteprima ADOMD](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-data-providers"></a>Scaricare i provider di dati **RTM** più recenti  
Usare i seguenti provider di dati se ci si trova in un ambiente di produzione e si richiedono versioni completamente rilasciate e supportate.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Passaggi successivi
Con i provider di dati più recenti installati, l'applicazione client è pronta a connettersi a un server. Per altre informazioni sulla connessione da un client, vedere [Ottenere dati da Azure Analysis Services](analysis-services-connect.md).

