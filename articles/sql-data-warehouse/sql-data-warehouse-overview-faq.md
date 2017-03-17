---
title: Domande frequenti su SQL Data Warehouse di Azure | Microsoft Docs
description: In questo articolo sono elencate le domande frequenti su SQL Data Warehouse di Azure poste dai clienti e dagli sviluppatori
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: 812CA525-3BF3-49DF-8DF3-FB4342464F4F
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 3/1/2017
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 7a752bfb349d2730537538f6856fe431204d3329
ms.lasthandoff: 03/04/2017


---

# <a name="sql-data-warehouse-frequently-asked-questions"></a>Domande frequenti su SQL Data Warehouse di Azure

## <a name="general"></a>Generale

D: Cosa offre SQL DW per la protezione dei dati?

R. SQL DW offre diverse soluzioni per la protezione dei dati, ad esempio TDE e il controllo. Per altre informazioni, vedere [Sicurezza].

D: Dove è possibile trovare gli standard legali o aziendali a cui SQL DW è conforme?

R. Visitare la pagina [Conformità di Microsoft] per le diverse offerte di conformità per prodotto, ad esempio SOC e ISO. Scegliere prima in base al titolo di conformità, quindi espandere Azure nella sezione servizi cloud nell'ambito di Microsoft sul lato destro della pagina per visualizzare i servizi conformi ai servizi di Azure.
 
D: È possibile connettersi a PowerBI?

R. È possibile usarlo. Anche se PowerBI supporta la query diretta con SQL DW, non è pensato per un numero elevato di utenti o di dati in tempo reale. Per l'uso della produzione di PowerBI, è consigliabile usare PowerBI su Azure Analysis Services o IaaS di Analysis Service. 

D: Quali sono i limiti di capacità di SQL Data Warehouse?

R. Vedere la pagina relativa ai [limiti di capacità] correnti. 

## <a name="user-support"></a>Supporto per l'utente

D: Ho una richiesta di funzionalità, dove posso inviarla?

R. Se si dispone di una richiesta di funzionalità, inviarla alla pagina [UserVoice]

D: Come posso fare?

R. Per assistenza nello sviluppo con SQL Data Warehouse, è possibile porre domande alla pagina [Overflow dello stack]. 

D: Come posso inviare un ticket di supporto?

R. [I ticket di supporto] possono essere inviati tramite il Portale di Azure.

## <a name="sql-languagefeature-support"></a>Supporto per le funzionalità/linguaggio SQL 

D: Quali tipi di dati sono supportati da SQL Data Warehouse?

R. Vedere i [tipi di dati] di SQL Data Warehouse.

D: Quali funzionalità delle tabelle sono supportate?

R. Sebbene SQL Data Warehouse supporti molte funzionalità, alcune non sono supportate. Queste sono documentate in [Funzionalità non supportate delle tabelle].

## <a name="tooling-and-administration"></a>Strumenti e amministrazione

D: I progetti di Database sono supportati in Visual Studio.

R. Attualmente i progetti di Database non sono supportati in Visual Studio per SQL Data Warehouse. Se si desidera eseguire il cast di un voto per ottenere questa funzionalità, visitare il forum [richiesta di funzionalità per progetti di Database].

D: SQL Data Warehouse supporta le API REST?

R. Sì. La maggior parte delle funzionalità REST che può essere usata con il Database SQL è disponibile anche con SQL Data Warehouse. È possibile trovare informazioni sulle API nelle pagine di documentazione REST o in [MSDN].


## <a name="loading"></a>Caricamento

D: Quali driver client sono supportati?

R. Il supporto dei driver per DW è reperibile nella pagina delle[stringhe di connessione]

D: Quali formati di file sono supportati da PolyBase con SQL Data Warehouse?

R: Orc, RC, Parquet e testo delimitato semplice

D: A cosa è possibile collegarsi da SQL Data Warehouse usando PolyBase? 

R: [Azure Data Lake Store] e [BLOB del servizio di archiviazione di Azure]

D: È possibile impostare il calcolo per la connessione al BLOB del servizio di archiviazione di Azure o ad ADLS? 

R: No, PolyBase di SQL DW interagisce solo con i componenti di archiviazione. 

D: È possibile connettersi ad HDI?

R: HDI può usare ADLS o WASB come livello HDFS. Se si dispone di un livello HDFS, è possibile caricare i dati in SQL DW. Tuttavia, non è possibile generare il calcolo di distribuzione per l'istanza HDI. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su SQL Data Warehouse nel complesso, vedere la pagina [Panoramica].


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[stringhe di connessione]: ./sql-data-warehouse-connection-strings.md
[Overflow dello stack]: http://stackoverflow.com/questions/tagged/azure-sqldw
[I ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Sicurezza]: ./sql-data-warehouse-overview-manage-security.md
[Conformità di Microsoft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[limiti di capacità]: ./sql-data-warehouse-service-capacity-limits.md
[tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Funzionalità non supportate delle tabelle]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md 
[BLOB del servizio di archiviazione di Azure]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[richiesta di funzionalità per progetti di Database]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/en-us/library/azure/mt163685.aspx
[Panoramica]: ./sql-data-warehouse-overview-faq.md
