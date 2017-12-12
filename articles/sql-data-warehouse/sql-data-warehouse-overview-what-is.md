---
title: "Che cos'è SQL Data Warehouse di Azure? | Documentazione Microsoft"
description: "Database distribuito di livello aziendale, in grado di elaborare volumi di dati relazionali e non relazionali anche nell'ordine di petabyte. Si tratta del primo data warehouse basato sul cloud del settore con possibilità di aumento, riduzione e sospensione in pochi secondi."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jenniehubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 10/23/2017
ms.author: jrj
ms.openlocfilehash: 7fd17c29c413905084c6b3736018d111d3b28fea
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>Che cos'è SQL Data Warehouse di Azure?

SQL Data Warehouse è un data warehouse aziendale basato sul cloud che sfrutta l'elaborazione MPP (Massively Parallel Processing) per eseguire rapidamente query complesse su petabyte di dati. Usare SQL Data Warehouse come componente chiave di una soluzione per Big Data. Importare i Big Data in SQL Data Warehouse con semplici query T-SQL PolyBase e quindi sfruttare la potenza dell'elaborazione MPP per eseguire analisi ad alte prestazioni. Man mano che si procede con l'integrazione e l'analisi, il data warehouse diventerà il punto di riferimento dell'azienda da cui ricavare informazioni dettagliate.  


## <a name="key-component-of-big-data-solution"></a>Componente chiave della soluzione per Big Data
SQL Data Warehouse è un componente chiave di una soluzione per Big Data end-to-end nel cloud.

![Soluzione di data warehouse](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

In una soluzione cloud per i dati, i dati vengono inseriti in archivi di Big Data da numerose origini. Una volta inseriti i dati in un archivio di Big Data, Hadoop, Spark e gli algoritmi di apprendimento automatico preparano i dati e ne eseguono il training. Quando i dati sono pronti per le analisi complesse, SQL Data Warehouse usa PolyBase per eseguire query sugli archivi di Big Data. PolyBase usa query T-SQL standard per inserire i dati in SQL Data Warehouse.
 
SQL Data Warehouse archivia i dati in tabelle relazionali con archiviazione a colonne. Questo formato consente di ridurre notevolmente i costi di archiviazione dei dati e di migliorare le prestazioni di query. Una volta archiviati i dati in SQL Data Warehouse, è possibile eseguire analisi su larga scala. Rispetto ai sistemi di database tradizionali, le query di analisi vengono completate in secondi invece che in minuti oppure in ore invece che in giorni. 

I risultati delle analisi possono essere passati ad applicazioni o database di report in tutto il mondo. Gli analisti aziendali possono quindi ottenere informazioni dettagliate per prendere decisioni ben informate.

## <a name="optimization-choices"></a>Scelte di ottimizzazione

SQL Data Warehouse offre [livelli di prestazioni](performance-tiers.md) progettati per garantire flessibilità, in modo da soddisfare le esigenze in relazione ai dati di qualsiasi entità. È possibile scegliere un data warehouse ottimizzato per l'elasticità o per il calcolo. 

- Il **livello di prestazioni Ottimizzato per l'elasticità** separa i livelli di calcolo e di archiviazione nell'architettura. Questa opzione è ideale per i carichi di lavoro che possono sfruttare la separazione tra calcolo e archiviazione, con scalabilità frequente a supporto di brevi periodi di attività di picco. Questo livello di calcolo garantisce il costo più basso, con scalabilità sufficiente a supportare la maggior parte dei carichi di lavoro dei clienti.

- Il **livello di prestazioni ottimizzato per il calcolo** usa l'hardware di Azure più recente per introdurre una nuova cache SSD (Solid State Disk) NVMe che mantiene i dati usati più spesso vicino alla CPU, ovvero nella posizione migliore possibile. Grazie alla suddivisione automatica dei livelli di archiviazione, questo livello di prestazioni è straordinario per le query complesse, perché tutte le operazioni di I/O avvengono in locale nel livello di calcolo. Il columnstore è stato migliorato per archiviare una quantità di dati illimitata in SQL Data Warehouse. Il livello di prestazioni ottimizzato per il calcolo offre la massima scalabilità, consentendo di arrivare fino a 30.000 DWU a elevato utilizzo di calcolo. Scegliere questo livello per i carichi di lavoro che richiedono prestazioni continue ed estremamente veloci.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso alcune informazioni su SQL Data Warehouse, vedere come [creare un SQL Data Warehouse][create a SQL Data Warehouse] rapidamente e [caricare i dati di esempio][load sample data]. Se non si ha familiarità con Azure, il [glossario di Azure][Azure glossary] può essere utile quando si incontrano termini nuovi. Oppure vedere alcune delle altre risorse disponibili per SQL Data Warehouse.  

* [Casi di successo dei clienti]
* [Blog]
* [Richieste di funzionalità]
* [Video]
* [Blog Customer Advisory Team]
* [Creare un ticket di supporto]
* [Forum MSDN]
* [Forum su Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Casi di successo dei clienti]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/en-us/support/legal/sla/
