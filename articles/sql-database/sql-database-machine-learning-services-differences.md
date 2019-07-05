---
title: Differenze principali per Azure SQL Database servizi Machine Learning (anteprima)
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
ms.date: 03/01/2019
ms.openlocfilehash: ee92b598625b1346cf87c661d1867cc1cb012b60
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486000"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Differenze principali tra SQL Server e servizi di Machine Learning nel Database SQL di Azure (anteprima)

La funzionalità di Azure SQL Database Machine Learning Services (con R) (anteprima) è simile a [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Di seguito sono riportate alcune differenze fondamentali.

> [!IMPORTANT]
> Machine Learning Services nel database SQL di Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Supporto per le lingue

SQL Server include il supporto per R e Python tramite il [framework di estendibilità](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). Il database SQL non supporta entrambi i linguaggi. Le differenze principali sono le seguenti:

- Nel database SQL, R è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python.
- La versione di R è 3.4.4.
- Non è necessario configurare `external scripts enabled` tramite `sp_configure`. Dopo aver [effettuato la registrazione](sql-database-machine-learning-services-overview.md#signup) Machine Learning è abilitato per il database SQL.

## <a name="package-management"></a>Gestione dei pacchetti

L'installazione e la gestione dei pacchetti R funziona in modo diverso nel database SQL e in SQL Server. Le differenze sono le seguenti:

- I pacchetti R vengono installati tramite [sqlmlutils](https://github.com/Microsoft/sqlmlutils) o [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- I pacchetti non possono eseguire chiamate di rete in uscita. Questa limitazione è simile al [predefinito le regole del firewall per servizi di Machine Learning](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) in SQL Server, ma non può essere modificato nel Database SQL.
- Non è disponibile il supporto per i pacchetti che dipendono da runtime esterni, ad esempio Java, o richiedono l'accesso alle API del sistema operativo per l'installazione o l'utilizzo.

## <a name="writing-to-a-temporary-table"></a>La scrittura in una tabella temporanea

Se si usa RODBC nel Database SQL di Azure, quindi non è possibile scrivere in una tabella temporanea, se viene creato all'interno o all'esterno del `sp_execute_external_script` sessione. La soluzione alternativa consiste nell'usare [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) e [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (con sovrascrittura = FALSE e aggiungere = "rows") per scrivere in una tabella temporanea globale creata prima il `sp_execute_external_script` query.

## <a name="resource-governance"></a>Governance delle risorse

Non è possibile limitare le risorse R mediante [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e pool di risorse esterni.

Durante l'anteprima pubblica, vengono impostate su un massimo di 20% delle risorse del Database SQL, risorse R e dipendono dal quale livello di servizio scelto. Per altre informazioni, vedere [Modelli di acquisto del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Errore di memoria insufficiente

Se la memoria insufficiente è disponibile per R, si otterrà un messaggio di errore. Messaggi di errore comuni sono:

- Impossibile comunicare con il runtime per lo script 'R' per l'id richiesta: * * *. Verificare i requisiti del runtime di 'R'
- Errore di script 'R' si è verificato durante l'esecuzione di 'sp_execute_external_script' con HRESULT 0x80004004. ... si è verificato un errore dello script esterno: ".. Impossibile allocare memoria (0 Mb) nella funzione C 'R_AllocStringBuffer' "
- Si è verificato un errore dello script esterno: Errore: Impossibile allocare vettore di dimensioni.

Utilizzo dipende dalla quantità di memoria viene utilizzata negli script R e il numero di query parallele in esecuzione. Se si ricevano gli errori sopra indicati, è possibile scalare il database a un livello di servizio superiore per risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la panoramica [servizi Azure SQL Database Machine Learning con R (anteprima)](sql-database-machine-learning-services-overview.md).
- Per informazioni su come usare R per eseguire query SQL Database servizi Azure Machine Learning (anteprima), vedere la [Guida introduttiva](sql-database-connect-query-r.md).
- Per iniziare a usare alcuni script R semplici, vedere [creare ed eseguire script R semplici in Azure SQL Database servizi Machine Learning (anteprima)](sql-database-quickstart-r-create-script.md).
