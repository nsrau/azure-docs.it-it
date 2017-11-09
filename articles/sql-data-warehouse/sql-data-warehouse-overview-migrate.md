---
title: Eseguire la migrazione della soluzione in SQL Data Warehouse | Microsoft Docs
description: Indicazioni sulla migrazione per spostare la soluzione nella piattaforma Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/27/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 771b9456e66b8a1e41f72340b695b19e2adaf793
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Eseguire la migrazione della soluzione in Azure SQL Data Warehouse
Scoprire cosa comporta la migrazione di una soluzione di database esistente ad Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>Profilatura del carico di lavoro
Prima di eseguire la migrazione, è opportuno assicurarsi che SQL Data Warehouse sia la soluzione ideale per il carico di lavoro specifico. SQL Data Warehouse è un sistema distribuito progettato per eseguire analisi su dati di grandi dimensioni.  La migrazione a SQL Data Warehouse richiede alcune modifiche di progettazione non troppo difficili da comprendere, ma la cui implementazione potrebbe richiedere del tempo. Se l'azienda richiede un data warehouse di classe enterprise, i vantaggi valgono la pena. Tuttavia, se non è necessaria la potenza di SQL Data Warehouse, è più conveniente usare SQL Server o il database SQL di Azure.

Prendere in considerazione l'uso di SQL Data Warehouse nei casi seguenti:
- Si devono gestire uno o più terabyte di dati
- Si prevede di eseguire operazioni di analisi su grandi quantità di dati
- È necessario avere la possibilità di ridimensionare le capacità di calcolo e archiviazione 
- Si vuole risparmiare sui costi tramite la sospensione delle risorse di calcolo quando non sono necessarie.

Non usare SQL Data Warehouse per carichi di lavoro operativi (OLTP) con queste esigenze:
- Operazioni di lettura e scrittura molto frequenti
- Numeri elevati di selezioni singleton
- Volumi elevati di inserimenti di righe singole
- Elaborazioni riga per riga
- Formati non compatibili (JSON, XML)


## <a name="plan-the-migration"></a>Pianificare la migrazione

Dopo aver deciso di eseguire la migrazione di una soluzione esistente a SQL Data Warehouse, è importante pianificare la migrazione prima di iniziare. 

Uno degli obiettivi di pianificazione è assicurarsi che i dati, gli schemi di tabella e il codice siano compatibili con SQL Data Warehouse. Esistono alcune differenze di compatibilità da risolvere tra il sistema corrente e SQL Data Warehouse. Inoltre, la migrazione di grandi quantità di dati in Azure richiede del tempo. Una pianificazione attenta consente di velocizzare il trasferimento dei dati in Azure. 

Un altro obiettivo della pianificazione è apportare alcune modifiche di progettazione per garantire che la soluzione possa sfruttare le prestazioni elevate per le query per cui è progettato SQL Data Warehouse. La progettazione di data warehouse ai fini della scalabilità introduce modelli di progettazione diversi, pertanto gli approcci tradizionali non si rivelano sempre ottimali. Anche se è possibile apportare alcune modifiche di progettazione dopo la migrazione, introdurre le modifiche il prima possibile nel processo consentirà di risparmiare tempo in un secondo momento.

Per eseguire correttamente una migrazione, è necessario eseguire la migrazione di schemi di tabella, codice e dati. Per linee guida su questi aspetti della migrazione, vedere:

-  [Eseguire la migrazione degli schemi](sql-data-warehouse-migrate-schema.md)
-  [Eseguire la migrazione del codice](sql-data-warehouse-migrate-code.md)
-  [Eseguire la migrazione dei dati](sql-data-warehouse-migrate-data.md). 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Passaggi successivi
Anche il team CAT (Customer Advisory Team) offre indicazioni molto utili per SQL Data Warehouse, pubblicate tramite blog.  Per altre istruzioni sulla migrazione, vedere l'articolo [Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice] (Migrazione dei dati in Azure SQL Data Warehouse in pratica).

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
