---
title: Differenze delle funzionalità T-SQL in Synapse SQL
description: Elenco delle funzionalità di Transact-SQL disponibili in Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 03995166df5d40f7f8be7054aed0727be254ed73
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376894"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Funzionalità Transact-SQL supportate in Azure Synapse SQL

Azure Synapse SQL è un servizio di analisi di Big Data che consente di eseguire query e di analizzare i dati tramite il linguaggio T-SQL. Per l'analisi dei dati, è possibile usare il dialetto standard compatibile con ANSI del linguaggio SQL usato in SQL Server e in Database SQL di Azure. 

Il linguaggio Transact-SQL usato nel modello serverless e dedicato di Synapse SQL può fare riferimento a oggetti diversi e presenta alcune differenze nel set di funzionalità supportate. Questa pagina illustra le differenze generali del linguaggio Transact-SQL tra i modelli di consumo di Synapse SQL.

## <a name="database-objects"></a>Oggetti di database

I modelli di consumo in Synapse SQL consentono di usare oggetti di database diversi. Il confronto dei tipi di oggetto supportati è illustrato nella tabella seguente:

|   | Dedicato | Senza server |
| --- | --- | --- |
| **Tabelle** | [Sì](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | No, il modello serverless consente di eseguire query solo sui dati esterni posizionati in [Archiviazione di Azure](#storage-options) |
| **Visualizzazioni** | [Sì](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Le viste possono usare gli [elementi del linguaggio di query](#query-language) disponibili nel modello dedicato. | [Sì](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Le viste possono usare gli [elementi del linguaggio di query](#query-language) disponibili nel modello serverless. |
| **Schemi** | [Sì](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Sì](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Tabelle temporanee** | [Sì](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **Procedure** | [Sì](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | No |
| **Funzioni** | [Sì](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sì, solo funzioni con valori di tabella inline. |
| **Trigger** | No | No |
| **Tabelle esterne** | [Sì](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Vedere i [formati di dati](#data-formats) supportati. | [Sì](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Vedere i [formati di dati](#data-formats) supportati. |
| **Memorizzazione nella cache delle query** | Sì, più modalità (memorizzazione nella cache basata su SSD, in memoria, memorizzazione nella cache del set di risultati). Sono inoltre supportate le viste materializzate | No |
| **Variabili di tabella** | [No](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), usare tabelle temporanee | No |
| **[Distribuzione di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Sì | No |
| **[Indicizzazione di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Sì | No |
| **[Partizioni di tabella](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Sì | No |
| **[Statistiche](develop-tables-statistics.md)**            | Sì | Sì |
| **[Gestione del carico di lavoro, classi di risorse e controllo della concorrenza](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sì    | No |

## <a name="query-language"></a>Linguaggio di query

I linguaggi di query usati in Synapse SQL possono supportare funzionalità diverse in base al modello di consumo. La tabella seguente illustra le differenze più importanti del linguaggio di query nei dialetti Transact-SQL:

|   | Dedicato | Senza server |
| --- | --- | --- |
| **Istruzione SELECT** | Sì. Le clausole di query Transact-SQL [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) non sono supportate. | Sì. Le clausole di query Transact-SQL [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e gli hint per la query non sono supportati. È possibile usare [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) e [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per eseguire query su oggetti di sistema (non sui dati esterni). |
| **Istruzione INSERT** | Sì | No |
| **Istruzione UPDATE** | Sì | No |
| **Istruzione DELETE** | Sì | No |
| **Istruzione merge** | No | No |
| **[Transazioni](develop-transactions.md)** | Sì | No |
| **[Etichette](develop-label.md)** | Sì | No |
| **Caricamento dati** | Sì. L'utilità preferita è l'istruzione [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), ma il sistema supporta sia il caricamento BULK (BCP) sia [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per il caricamento dei dati. | No |
| **Esportazione dati** | Sì. Tramite [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Sì. Tramite [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). |
| **Tipi** | Sì, tutti i tipi Transact-SQL ad eccezione di [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ntext, text, and image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sì, tutti i tipi Transact-SQL ad eccezione di [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ntext, text, and image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e Table |
| **Query tra database** | No | Sì, inclusa l'istruzione [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). |
| **Funzioni predefinite (analisi)** | Sì, tutte le funzioni Transact-SQL di [analisi](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), conversione, [data e ora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), logiche, [matematiche](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), ad eccezione di [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sì, tutte le funzioni Transact-SQL di [analisi](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), conversione [data e ora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), logiche, [matematiche](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). |
| **Funzioni predefinite (testo)** | Sì. Tutte le funzioni Transact-SQL di tipo [stringa](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e regole di confronto, ad eccezione di [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sì. Tutte le funzioni Transact-SQL di tipo [stringa](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e regole di confronto. |
| **Funzioni predefinite con valori di tabella** | Sì, le [funzioni Transact-SQL per i set di righe](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), ad eccezione di [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sì, le [funzioni Transact-SQL per i set di righe](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), ad eccezione di [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **Aggregazioni** |  Aggregazioni predefinite di Transact-SQL, ad eccezione di [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Aggregazioni predefinite di Transact-SQL, ad eccezione di [STRING\_AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Operatori** | Sì, tutti gli [operatori Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), ad eccezione di [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sì, tutti gli [operatori Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **Controllo di flusso** | Sì. Tutte le [istruzioni Transact-SQL per il controllo di flusso](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), ad eccezione di [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sì. Tutte le [istruzioni Transact-SQL per il controllo di flusso](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), ad eccezione di [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e della query SELECT nella condizione `WHILE (...)` |
| **Istruzioni DDL (CREATE, ALTER, DROP)** | Sì. Tutte le istruzioni DDL Transact-SQL applicabili ai tipi di oggetto supportati | Sì. Tutte le istruzioni DDL Transact-SQL applicabili ai tipi di oggetto supportati |

## <a name="security"></a>Sicurezza

Synapse SQL consente di usare le funzionalità di sicurezza predefinite per proteggere i dati e controllare l'accesso. Nella tabella seguente vengono confrontate le differenze generali tra i modelli di consumo di Synapse SQL.

|   | Dedicato | Senza server |
| --- | --- | --- |
| **Account di accesso** | N/A (sono supportati solo gli utenti contenuti nei database) | Sì |
| **Utenti** |  N/A (sono supportati solo gli utenti contenuti nei database) | Sì |
| **[Utenti indipendenti](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Sì. **Nota:** un solo utente di Azure AD può essere un amministratore senza restrizioni | Sì |
| **Autenticazione con nome utente e password SQL**| Sì | Sì |
| **Autenticazione di Azure Active Directory (Azure AD)**| Sì, utenti di Azure AD | Sì, account di accesso e utenti di Azure AD |
| **Autenticazione pass-through di Azure Active Directory (Azure AD) per l'archiviazione** | Sì | Sì |
| **Autenticazione tramite token di firma di accesso condiviso per l'archiviazione** | No | Sì, tramite [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) oppure [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a livello di istanza. |
| **Autenticazione tramite chiave di accesso alle risorse di archiviazione** | Sì, tramite [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | No |
| **Autenticazione tramite [Identità gestita](../security/synapse-workspace-managed-identity.md) per l'archiviazione** | Sì, tramite [credenziali dell'identità del servizio gestita](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sì, tramite credenziale `Managed Identity`. |
| **Autenticazione tramite identità applicazione per l'archiviazione** | [Sì](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | No |
| **Autorizzazioni a livello di oggetto** | Sì, inclusa la possibilità di concedere, negare e revocare le autorizzazioni agli utenti | Sì, inclusa la possibilità di concedere, negare e revocare le autorizzazioni a utenti/account di accesso per gli oggetti di sistema supportati |
| **Autorizzazioni a livello di schema** | Sì, inclusa la possibilità di concedere, negare e revocare le autorizzazioni a utenti/account di accesso per lo schema | Sì, inclusa la possibilità di concedere, negare e revocare le autorizzazioni a utenti/account di accesso per lo schema |
| **Autorizzazioni a [livello di database](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Sì | Sì |
| **Autorizzazioni a [livello di server](/sql/relational-databases/security/authentication-access/server-level-roles)** | No | Sì, sono supportati sysadmin e altri ruoli del server |
| **Autorizzazioni - [Sicurezza a livello di colonna](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sì | Sì |
| **Ruoli/gruppi** | Sì (con ambito di database) | Sì (con ambito sia di server che di database) |
| **Funzioni di sicurezza e identità** | Alcune funzioni e operatori di sicurezza Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, `OPEN/CLOSE MASTER KEY` | Alcune funzioni e operatori di sicurezza Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` e `REVERT`. Le funzioni di sicurezza non possono essere usate per eseguire query sui dati esterni. Archiviare il risultato in una variabile che può essere usata nella query.  |
| **DATABASE SCOPED CREDENTIAL** | Sì | Sì |
| **SERVER SCOPED CREDENTIAL** | No | Sì |
| **Sicurezza a livello di riga** | [Sì](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | No |
| **Transparent Data Encryption (TDE)** | [Sì](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | No | 
| **Individuazione dati e classificazione** | [Sì](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **Valutazione della vulnerabilità** | [Sì](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **Advanced Threat Protection** | [Sì](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Controllo** | [Sì](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **[Regole del firewall](../security/synapse-workspace-ip-firewall.md)**| Sì | Sì |
| **[Endpoint privato](../security/synapse-workspace-managed-private-endpoints.md)**| Sì | Sì |

Il pool SQL dedicato e il pool SQL serverless usano il linguaggio Transact-SQL per eseguire query sui dati. Per informazioni dettagliate sulle differenze, vedere le [informazioni di riferimento sul linguaggio Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Strumenti

È possibile usare vari strumenti per connettersi a Synapse SQL per eseguire query sui dati.

|   | Dedicato | Senza server |
| --- | --- | --- |
| **Synapse Studio** | Sì, script SQL | Sì, script SQL |
| **Power BI** | Sì | [Sì](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | Sì | Sì |
| **Azure Data Studio** | Sì | Sì, versione 1.18.0 o successiva. Gli script SQL e i notebook SQL sono supportati. |
| **SQL Server Management Studio** | Sì | Sì, versione 18.5 o successiva |

> [!NOTE]
> È possibile usare SSMS per connettersi al pool SQL serverless (anteprima) ed eseguire query. SSMS è parzialmente supportato a partire dalla versione 18.5, è possibile usarlo solo per connettersi ed eseguire query.

La maggior parte delle applicazioni che usano il linguaggio Transact-SQL standard possono eseguire query sia sui modelli di consumo dedicati che serverless di Synapse SQL.

## <a name="storage-options"></a>Opzioni di archiviazione

I dati analizzati possono essere archiviati in vari tipi di archiviazione. La tabella seguente elenca tutte le opzioni di archiviazione disponibili:

|   | Dedicato | Senza server |
| --- | --- | --- |
| **Archiviazione interna** | Sì | No |
| **Azure Data Lake v2** | Sì | Sì |
| **Archiviazione BLOB di Azure** | Sì | Sì |
| **Archiviazione analitica di Azure CosmosDB** | No | Sì con [Collegamento a Synapse](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (in [anteprima controllata](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)) |

## <a name="data-formats"></a>Formati di dati

I dati analizzati possono essere archiviati in vari formati di archiviazione. La tabella seguente elenca tutti i formati di dati disponibili che è possibile analizzare:

|   | Dedicato | Senza server |
| --- | --- | --- |
| **Delimitato** | [Sì](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Sì](query-single-csv-file.md) |
| **CSV** | Sì, (i delimitatori a più caratteri non sono supportati) | [Sì](query-single-csv-file.md) |
| **Parquet** | [Sì](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Sì](query-parquet-files.md), inclusi i file con [tipi annidati](query-parquet-nested-types.md) |
| **Hive ORC** | [Sì](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | No |
| **Hive RC** | [Sì](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | No |
| **JSON** | Sì | [Sì](query-json-files.md) |
| **Avro** | No | No |
| **[Delta-lake](https://delta.io/)** | No | No |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | No | No |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle procedure consigliate per il pool SQL dedicato e il pool SQL serverless, vedere gli articoli seguenti:

- [Procedure consigliate per il pool SQL dedicato](best-practices-sql-pool.md)
- [Procedure consigliate per il pool SQL serverless](best-practices-sql-on-demand.md)
