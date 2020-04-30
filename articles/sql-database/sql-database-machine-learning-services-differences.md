---
title: Differenze principali per Machine Learning Services (anteprima)
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
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453150"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Differenze principali tra Machine Learning Services nel database SQL di Azure (anteprima) e SQL Server

La funzionalità del database SQL di Azure Machine Learning Services (con R) in (anteprima) è simile a [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Di seguito sono riportate alcune differenze principali.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Supporto delle lingue

SQL Server include il supporto per R e Python tramite il [framework di estendibilità](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). Il database SQL non supporta entrambi i linguaggi. Le differenze principali sono le seguenti:

- Nel database SQL, R è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python.
- La versione di R è 3.4.4.
- Non è necessario configurare `external scripts enabled` tramite `sp_configure`.

## <a name="package-management"></a>Gestione dei pacchetti

L'installazione e la gestione dei pacchetti R funziona in modo diverso nel database SQL e in SQL Server. Le differenze sono le seguenti:

- I pacchetti R vengono installati tramite [sqlmlutils](https://github.com/Microsoft/sqlmlutils) o [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- I pacchetti non possono eseguire chiamate di rete in uscita. Questa limitazione è simile alle [regole del firewall predefinite per Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) SQL Server, ma non può essere modificata nel database SQL.
- Non è disponibile il supporto per i pacchetti che dipendono da runtime esterni, ad esempio Java, o richiedono l'accesso alle API del sistema operativo per l'installazione o l'utilizzo.

## <a name="writing-to-a-temporary-table"></a>Scrittura in una tabella temporanea

Se si usa RODBC nel database SQL di Azure, non è possibile scrivere in una tabella temporanea, indipendentemente dal fatto che sia stato creato all' `sp_execute_external_script` interno o all'esterno della sessione. La soluzione alternativa consiste nell'usare [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) e [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (with overwrite = false e Append = "Rows") per scrivere in una tabella temporanea globale creata `sp_execute_external_script` prima della query.

## <a name="resource-governance"></a>Governance delle risorse

Non è possibile limitare le risorse R mediante [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e pool di risorse esterni.

Durante l'anteprima pubblica, le risorse R sono impostate su un massimo del 20% delle risorse del database SQL e dipendono dal livello di servizio scelto. Per altre informazioni, vedere [Modelli di acquisto del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Errore di memoria insufficiente

Se la memoria disponibile per R non è sufficiente, verrà ricevuto un messaggio di errore. I messaggi di errore comuni sono:

- Non è possibile comunicare con il runtime per lo script ' R ' per l'ID richiesta: * * * * * * *. Verificare i requisiti del runtime ' R '
- Si è verificato un errore di script ' R ' durante l'esecuzione di ' sp_execute_external_script ' con HRESULT 0x80004004. ... si è verificato un errore di script esterno: ".. non è stato possibile allocare memoria (0 MB) nella funzione C'R_AllocStringBuffer ' "
- Si è verificato un errore di script esterno: errore: Impossibile allocare un vettore di dimensioni.

L'utilizzo della memoria dipende dalla quantità utilizzata negli script R e dal numero di query parallele in esecuzione. Se si ricevono gli errori precedenti, è possibile ridimensionare il database a un livello di servizio superiore per risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi

- Vedere Panoramica, [database SQL di Azure Machine Learning Services con R (anteprima)](sql-database-machine-learning-services-overview.md).
- Per informazioni su come usare R per eseguire query sul database SQL di Azure Machine Learning Services (anteprima), vedere la [Guida introduttiva](sql-database-connect-query-r.md).
- Per iniziare a usare alcuni semplici script R, vedere [creare ed eseguire script r semplici nel database SQL di Azure Machine Learning Services (anteprima)](sql-database-quickstart-r-create-script.md).
