---
title: Differenze principali per i servizi di Machine Learning (anteprima)Key differences for Machine Learning Services (preview)
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453150"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Differenze principali tra I servizi di Machine Learning nel database SQL di Azure (anteprima) e SQL ServerKey differences between Machine Learning Services in Azure SQL Database (preview) and SQL Server

La funzionalità di Azure SQL Database Machine Learning Services (con R) in (anteprima) è simile a Servizi di Apprendimento automatico di [SQL Server.](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) Di seguito sono riportate alcune differenze fondamentali.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Lingue supportate

SQL Server include il supporto per R e Python tramite il [framework di estendibilità](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). Il database SQL non supporta entrambi i linguaggi. Le differenze principali sono le seguenti:

- Nel database SQL, R è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python.
- La versione di R è 3.4.4.
- Non è necessario configurare `external scripts enabled` tramite `sp_configure`.

## <a name="package-management"></a>Gestione dei pacchetti

L'installazione e la gestione dei pacchetti R funziona in modo diverso nel database SQL e in SQL Server. Le differenze sono le seguenti:

- I pacchetti R vengono installati tramite [sqlmlutils](https://github.com/Microsoft/sqlmlutils) o [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- I pacchetti non possono eseguire chiamate di rete in uscita. Questa limitazione è simile alle [regole del firewall predefinite per i servizi](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) di Machine Learning in SQL Server, ma non può essere modificata nel database SQL.
- Non è disponibile il supporto per i pacchetti che dipendono da runtime esterni, ad esempio Java, o richiedono l'accesso alle API del sistema operativo per l'installazione o l'utilizzo.

## <a name="writing-to-a-temporary-table"></a>Scrittura in una tabella temporanea

Se si usa RODBC nel database SQL di Azure, non è possibile scrivere in una tabella `sp_execute_external_script` temporanea, indipendentemente dal fatto che venga creata all'interno o all'esterno della sessione. La soluzione consiste nell'utilizzare [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) e [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (con overwrite, FALSE e append" `sp_execute_external_script` per scrivere in una tabella temporanea globale creata prima della query.

## <a name="resource-governance"></a>Governance delle risorse

Non è possibile limitare le risorse R mediante [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e pool di risorse esterni.

Durante l'anteprima pubblica, le risorse R vengono impostate su un massimo del 20% delle risorse del database SQL e dipendono dal livello di servizio scelto. Per altre informazioni, vedere [Modelli di acquisto del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Errore di memoria insufficiente

Se la memoria disponibile per R è insufficiente, verrà visualizzato un messaggio di errore. I messaggi di errore comuni sono:

- Impossibile comunicare con il runtime per lo script 'R' per l'ID della richiesta: . Verificare i requisiti del runtime 'R'Please check the requirements of 'R' runtime
- Errore di script 'R' si è verificato durante l'esecuzione di 'sp_execute_external_script' con HRESULT 0x80004004. ... si è verificato un errore di script esterno: ".. impossibile allocare memoria (0 Mb) nella funzione C 'R_AllocStringBuffer'"
- Si è verificato un errore di script esterno: errore: impossibile allocare vettore di dimensione.

L'utilizzo della memoria dipende dalla quantità utilizzata negli script R e dal numero di query parallele in esecuzione. Se si ricevono gli errori precedenti, è possibile ridimensionare il database a un livello di servizio superiore per risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la panoramica, Servizi di Apprendimento automatico del database SQL di [Azure con R (anteprima).](sql-database-machine-learning-services-overview.md)
- Per informazioni su come usare R per eseguire query sui servizi di Azure SQL Database Machine Learning Services (anteprima), vedere la [Guida introduttiva](sql-database-connect-query-r.md).
- Per iniziare con alcuni semplici script R, vedere [Creare ed eseguire semplici script R in Azure SQL Database Machine Learning Services (anteprima).](sql-database-quickstart-r-create-script.md)
