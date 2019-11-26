---
title: Key differences for Machine Learning Services (preview)
description: Questo argomento descrive le differenze principali tra Machine Learning Services nel database SQL di Azure (con R) ed SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: 533e2b9e50a92cce1419da521d8cebc4955e4df6
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462132"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Key differences between Machine Learning Services in Azure SQL Database (preview) and SQL Server

The functionality of Azure SQL Database Machine Learning Services (with R) in  (preview) is similar to [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Below are some key differences.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Supporto delle lingue

SQL Server include il supporto per R e Python tramite il [framework di estendibilità](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). Il database SQL non supporta entrambi i linguaggi. Le differenze principali sono le seguenti:

- Nel database SQL, R è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python.
- La versione di R è 3.4.4.
- Non è necessario configurare `external scripts enabled` tramite `sp_configure`. Dopo aver [effettuato la registrazione](sql-database-machine-learning-services-overview.md#signup) Machine Learning è abilitato per il database SQL.

## <a name="package-management"></a>Gestione pacchetti

L'installazione e la gestione dei pacchetti R funziona in modo diverso nel database SQL e in SQL Server. Le differenze sono le seguenti:

- I pacchetti R vengono installati tramite [sqlmlutils](https://github.com/Microsoft/sqlmlutils) o [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- I pacchetti non possono eseguire chiamate di rete in uscita. This limitation is similar to the [default firewall rules for Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) in SQL Server, but can't be changed in SQL Database.
- Non è disponibile il supporto per i pacchetti che dipendono da runtime esterni, ad esempio Java, o richiedono l'accesso alle API del sistema operativo per l'installazione o l'utilizzo.

## <a name="writing-to-a-temporary-table"></a>Writing to a temporary table

If you're using RODBC in Azure SQL Database, then you can't write to a temporary table, whether it's created inside or outside of the `sp_execute_external_script` session. The workaround is to use [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) and [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (with overwrite=FALSE and append="rows") to write to a global temporary table created before the `sp_execute_external_script` query.

## <a name="resource-governance"></a>Governance delle risorse

Non è possibile limitare le risorse R mediante [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e pool di risorse esterni.

During the public preview, R resources are set to a maximum of 20% of the SQL Database resources, and depend on which service tier you choose. Per altre informazioni, vedere [Modelli di acquisto del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Insufficient memory error

If there is insufficient memory available for R, you will get an error message. Common error messages are:

- Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime
- 'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"
- An external script error occurred: Error: cannot allocate vector of size.

Memory usage depends on how much is used in your R scripts and the number of parallel queries being executed. If you receive the errors above, you can scale your database to a higher service tier to resolve this.

## <a name="next-steps"></a>Passaggi successivi

- See the overview, [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md).
- To learn how to use R to query Azure SQL Database Machine Learning Services (preview), see the [Quickstart guide](sql-database-connect-query-r.md).
- To get started with some simple R scripts, see [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md).
