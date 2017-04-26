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
ms.custom: integrate
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 12743b3594a0a62b0b974fb6dd1c39972c1bd3fa
ms.lasthandoff: 04/03/2017


---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Usare Data factory di Azure con SQL Data Warehouse
Data factory di Azure offre un metodo completamente gestito per l'orchestrazione del trasferimento di dati e l'esecuzione di stored procedure in SQL Data Warehouse.  In questo modo è possibile configurare e pianificare più facilmente procedure ETL (Extract, Transform, Load) complesse con SQL Data Warehouse. Per una panoramica più completa di Azure Data Factory, vedere la [documentazione di Azure Data Factory][Azure Data Factory documentation].

## <a name="data-movement"></a>Spostamento dei dati
Data factory di Azure consente lo spostamento di dati tra origini locali e diversi servizi di Azure.  In generale, l'attuale integrazione con Data factory di Azure supporta lo spostamento dei dati da e verso le posizioni seguenti:

* Archivio BLOB di Azure
* Database SQL di Azure
* Server SQL locale
* SQL Server in IaaS

Per informazioni su come configurare un'attività di copia, vedere [Copiare i dati con Azure Data Factory][Copy data with Azure Data Factory]

## <a name="stored-procedures"></a>Stored procedure
 Così come può essere usato per pianificare il trasferimento dei dati, Data factory di Azure può essere usato anche per orchestrare l'esecuzione di stored procedure.  Ciò consente di creare pipeline più complesse ed estende la capacità di Data factory di Azure per sfruttare la potenza di elaborazione di SQL Data Warehouse.

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica dell'integrazione, vedere [Panoramica dell'integrazione di SQL Data Warehouse][SQL Data Warehouse integration overview].
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/data-factory-data-movement-activities.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/


