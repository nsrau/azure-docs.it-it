---
title: Istruzioni T-SQL
description: Collegamenti alla documentazione per le istruzioni T-SQL supportate in ANALISI SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 53609049f69f0a3d50430ef512a5bf4a8ffd6d82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351360"
---
# <a name="t-sql-statements-supported-in-sql-analytics"></a>Istruzioni T-SQL supportate in SQL Analytics
Collegamenti alla documentazione per le istruzioni T-SQL supportate in ANALISI SQL.

## <a name="data-definition-language-ddl-statements"></a>Istruzioni DDL (Data Definition Language)
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [ALTER MATERIALIZED VIEW](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (Anteprima) 
* [ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
* [ALTER SCHEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER A TABELLA](https://msdn.microsoft.com/library/ms190273.aspx)
* [CREATE COLUMNSTORE INDEX](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREA DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
* [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREA FORMATO DI FILE ESTERNO](https://msdn.microsoft.com/library/dn935026.aspx)
* [CREA TABELLA ESTERNA](https://msdn.microsoft.com/library/dn935021.aspx)
* [CREATE (FUNZIONE)](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (Anteprima) 
* [CREATE PROCEDURE](https://msdn.microsoft.com/library/ms187926.aspx)
* [CREATE SCHEMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREARE STATISTICHE](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREA TABELLA](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [CREA VISTA](https://msdn.microsoft.com/library/ms187956.aspx)
* [CREARE IL CLASSIFICATORE DEL CARICO DI LAVOROCREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/mt146367.aspx)
* [DROP EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/mt146379.aspx)
* [DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [DROP PROCEDURE](https://msdn.microsoft.com/library/ms174969.aspx)
* [DROP STATISTICS](https://msdn.microsoft.com/library/ms175075.aspx)
* [TABELLA DI RILASCIO](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [DROP VIEW](https://msdn.microsoft.com/library/ms173492.aspx)
* [CLASSIFICATORE DEL CARICO DI LAVORO DI ELIMINAZIONEDROP WORKLOAD CLASSIFIER](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [RENAME](https://msdn.microsoft.com/library/mt631611.aspx)
* [SET RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TRUNCATE TABELLA](https://msdn.microsoft.com/library/ms177570.aspx)
* [STATISTICHE DI AGGIORNAMENTO](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Istruzioni DML (Data Manipulation Language)
* [Elimina](https://msdn.microsoft.com/library/ms189835.aspx)
* [Inserire](https://msdn.microsoft.com/library/ms174335.aspx)
* [Aggiornamento](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Database Console Command
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (anteprima)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (anteprima)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Istruzioni di query
* [Selezionare](https://msdn.microsoft.com/library/ms189499.aspx)
* [WITH common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT e INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [Spiegare](https://msdn.microsoft.com/library/mt631615.aspx)
* [Da](https://msdn.microsoft.com/library/ms177634.aspx)
* [Utilizzo di PIVOT e UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [GROUP BY](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDINA PER](https://msdn.microsoft.com/library/ms188385.aspx)
* [Opzione](https://msdn.microsoft.com/library/ms190322.aspx)
* [Unione](https://msdn.microsoft.com/library/ms180026.aspx)
* [Dove](https://msdn.microsoft.com/library/ms188047.aspx)
* [Torna all'inizio](https://msdn.microsoft.com/library/ms189463.aspx)
* [Aliasing](https://msdn.microsoft.com/library/mt631614.aspx)
* [Condizione di ricerca](https://msdn.microsoft.com/library/ms173545.aspx)
* [Sottoquery:](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Istruzioni per la sicurezza
* Autorizzazioni: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ALTER AUTHORIZATION](https://msdn.microsoft.com/library/ms187359.aspx)
* [ALTER CERTIFICATE](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTER DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630389.aspx)
* [ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [ALTER MASTER KEY](https://msdn.microsoft.com/library/ms186937.aspx)
* [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
* [BACKUP CERTIFICATE](https://msdn.microsoft.com/library/ms178578.aspx)
* [CLOSE MASTER KEY](https://msdn.microsoft.com/library/ms188387.aspx)
* [CREATE CERTIFICATE](https://msdn.microsoft.com/library/ms187798.aspx)
* [CREATE DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb677241.aspx)
* [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx)
* [CREA CHIAVE MASTER](https://msdn.microsoft.com/library/ms174382.aspx)
* [CREA RUOLO](https://msdn.microsoft.com/library/ms187936.aspx)
* [CREA UTENTE](https://msdn.microsoft.com/library/ms173463.aspx)
* [DROP CERTIFICATE](https://msdn.microsoft.com/library/ms179906.aspx)
* [DROP DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630256.aspx)
* [DROP LOGIN](https://msdn.microsoft.com/library/ms188012.aspx)
* [DROP MASTER KEY](https://msdn.microsoft.com/library/ms180071.aspx)
* [DROP ROLE](https://msdn.microsoft.com/library/ms174988.aspx)
* [DROP USER](https://msdn.microsoft.com/library/ms189438.aspx)
* [OPEN MASTER KEY](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni di riferimento, vedere Elementi del [linguaggio T-SQL in SQL Analytics](sql-data-warehouse-reference-tsql-language-elements.md)e [Visualizzazioni di sistema in SQL Analytics](sql-data-warehouse-reference-tsql-system-views.md).
