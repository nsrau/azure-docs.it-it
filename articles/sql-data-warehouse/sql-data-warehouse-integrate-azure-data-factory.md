---
title: Usare Azure Data Factory con SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per l&quot;uso di Data factory di Azure con Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 427a1e8c6bfb11ace38fcf84b7cda89d2d01d091


---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Usare Data factory di Azure con SQL Data Warehouse
Data factory di Azure offre un metodo completamente gestito per l'orchestrazione del trasferimento di dati e l'esecuzione di stored procedure in SQL Data Warehouse.  In questo modo è possibile configurare e pianificare più facilmente procedure ETL (Extract, Transform, Load) complesse con SQL Data Warehouse. Per una panoramica più completa di Azure Data Factory, vedere [Documentazione di Azure Data Factory][Documentazione di Azure Data Factory].

## <a name="data-movement"></a>Spostamento dei dati
Data factory di Azure consente lo spostamento di dati tra origini locali e diversi servizi di Azure.  In generale, l'attuale integrazione con Data factory di Azure supporta lo spostamento dei dati da e verso le posizioni seguenti:

* Archivio BLOB di Azure
* Database SQL di Azure
* Server SQL locale
* SQL Server in IaaS

Per informazioni su come impostare un'attività di copia, vedere [Copiare i dati con Data factory di Azure][Copiare i dati con Data factory di Azure]

## <a name="stored-procedures"></a>Stored procedure
 Così come può essere usato per pianificare il trasferimento dei dati, Data factory di Azure può essere usato anche per orchestrare l'esecuzione di stored procedure.  Ciò consente di creare pipeline più complesse ed estende la capacità di Data factory di Azure per sfruttare la potenza di elaborazione di SQL Data Warehouse.

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica dell'integrazione, vedere [Panoramica dell'integrazione di SQL Data Warehouse][Panoramica dell'integrazione di SQL Data Warehouse].
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][Panoramica sullo sviluppo per SQL Data Warehouse].

<!--Image references-->

<!--Article references-->

[Copiare i dati con Data factory di Azure]: ../data-factory/data-factory-data-movement-activities.md
[Panoramica sullo sviluppo per SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Panoramica dell'integrazione di SQL Data Warehouse]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentazione di Azure Data Factory]:https://azure.microsoft.com/documentation/services/data-factory/




<!--HONumber=Nov16_HO3-->


