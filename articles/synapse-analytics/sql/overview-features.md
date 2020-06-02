---
title: Differenze delle funzionalità T-SQL in Azure Synapse SQL
description: Elenco delle funzionalità di Transact-SQL disponibili in Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 556cce9e18c812759ccb6c4b8ee2c91c4cef2b5a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658891"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Funzionalità Transact-SQL supportate in Azure Synapse SQL

Azure Synapse SQL è un servizio di analisi di Big Data che consente di eseguire query e di analizzare i dati tramite il linguaggio T-SQL. Per l'analisi dei dati, è possibile usare il dialetto standard compatibile con ANSI del linguaggio SQL usato in SQL Server e in Database SQL di Azure. 

Il linguaggio Transact-SQL usato nel modello serverless e con provisioning di Synapse SQL può fare riferimento a oggetti diversi e presenta alcune differenze nel set di funzionalità supportate. Questa pagina illustra le differenze generali del linguaggio Transact-SQL tra i modelli di consumo di Synapse SQL.

## <a name="database-objects"></a>Oggetti di database

I modelli di consumo in Synapse SQL consentono di usare oggetti di database diversi. Il confronto dei tipi di oggetto supportati è illustrato nella tabella seguente:

|   | Sottoposto a provisioning | Senza server |
| --- | --- | --- |
| **Tabelle** | [Sì](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No, il modello serverless consente di eseguire query solo sui dati esterni posizionati in [Archiviazione di Azure](#storage-options) |
| **Visualizzazioni** | [Sì](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Le viste possono usare gli [elementi del linguaggio di query](#query-language) disponibili nel modello con provisioning. | [Sì](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Le viste possono usare gli [elementi del linguaggio di query](#query-language) disponibili nel modello serverless. |
| **Schemi** | [Sì](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sì](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Tabelle temporanee** | [Sì](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **Procedure** | [Sì](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **Funzioni** | [Sì](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **Trigger** | No | No |
| **Tabelle esterne** | [Sì](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Vedere i [formati di dati](#data-formats) supportati. | [Sì](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Vedere i [formati di dati](#data-formats) supportati. |
| **Memorizzazione nella cache delle query** | Sì, più modalità (memorizzazione nella cache basata su SSD, in memoria, memorizzazione nella cache del set di risultati). Sono inoltre supportate le viste materializzate | No |
| **Variabili di tabella** | [No](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), usare tabelle temporanee | No |
| **[Distribuzione di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Sì | No |
| **[Indicizzazione di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Sì | No |
| **[Partizioni di tabella](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Sì | No |
| **[Statistiche](develop-tables-statistics.md)**            | Sì | Sì |
| **[Classi di risorse e concorrenza](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sì    | No |

## <a name="query-language"></a>Linguaggio di query

I linguaggi di query usati in Synapse SQL possono supportare funzionalità diverse in base al modello di consumo. La tabella seguente illustra le differenze più importanti del linguaggio di query nei dialetti Transact-SQL:

|   | Sottoposto a provisioning | Senza server |
| --- | --- | --- |
| **Istruzione SELECT** | Sì. Le clausole di query Transact-SQL [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) non sono supportate. | Sì. Le clausole di query Transact-SQL [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e gli hint per la query non sono supportati. È possibile usare [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) e [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) solo per eseguire query sui dati nelle tabelle temporanee (non sui dati esterni). |
| **Istruzione INSERT** | Sì | No |
| **Istruzione UPDATE** | Sì | No |
| **Istruzione DELETE** | Sì | No |
| **Istruzione merge** | Sì | No |
| **[Transazioni](develop-transactions.md)** | Sì | No |
| **[Etichette](develop-label.md)** | Sì | No |
| **Caricamento dati** | Sì. L'utilità preferita è l'istruzione [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), ma il sistema supporta sia il caricamento BULK (BCP) sia [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per il caricamento dei dati. | No |
| **Esportazione dati** | Sì. Tramite [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Sì. Tramite [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| **Tipi** | Sì, tutti i tipi Transact-SQL ad eccezione di [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text, and image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sì, tutti i tipi Transact-SQL ad eccezione di [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text, and image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e Table |
| **Query tra database** | No | Sì, inclusa l'istruzione [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| **Funzioni predefinite (analisi)** | Sì, tutte le funzioni Transact-SQL di [analisi](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), conversione, [data e ora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logiche, [matematiche](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), ad eccezione di [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sì, tutte le funzioni Transact-SQL di [analisi](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), conversione [data e ora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logiche, [matematiche](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| **Funzioni predefinite (testo)** | Sì. Tutte le funzioni Transact-SQL di tipo [stringa](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e regole di confronto, ad eccezione di [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sì. Tutte le funzioni Transact-SQL di tipo [stringa](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e regole di confronto. |
| **Funzioni predefinite con valori di tabella** | Sì, le [funzioni Transact-SQL per i set di righe](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), ad eccezione di [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sì, le [funzioni Transact-SQL per i set di righe](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), ad eccezione di [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Aggregazioni** |  Aggregazioni predefinite di Transact-SQL, ad eccezione di [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Aggregazioni predefinite di Transact-SQL, ad eccezione di [STRING\_AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Operatori** | Sì, tutti gli [operatori Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), ad eccezione di [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sì, tutti gli [operatori Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Controllo di flusso** | Sì. Tutte le [istruzioni Transact-SQL per il controllo di flusso](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), ad eccezione di [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sì. Tutte le [istruzioni Transact-SQL per il controllo di flusso](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), ad eccezione di [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e della query SELECT nella condizione `WHILE (...)` |
| **Istruzioni DDL (CREATE, ALTER, DROP)** | Sì. Tutte le istruzioni DDL Transact-SQL applicabili ai tipi di oggetto supportati | Sì. Tutte le istruzioni DDL Transact-SQL applicabili ai tipi di oggetto supportati |

## <a name="security"></a>Sicurezza

Synapse SQL consente di usare le funzionalità di sicurezza predefinite per proteggere i dati e controllare l'accesso. Nella tabella seguente vengono confrontate le differenze generali tra i modelli di consumo di Synapse SQL.

|   | Sottoposto a provisioning | Senza server |
| --- | --- | --- |
| **Account di accesso** | N/A (sono supportati solo gli utenti contenuti nei database) | Sì |
| **Utenti** |  N/A (sono supportati solo gli utenti contenuti nei database) | Sì |
| **[Utenti indipendenti](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Sì. **Nota:** un solo utente di AAD può essere un amministratore senza restrizioni | Sì |
| **Autenticazione di Azure Active Directory (AAD)**| Sì, utenti di AAD | Sì, account di accesso e utenti di AAD |
| **Autenticazione pass-through di AAD per l'archiviazione** | Sì | Sì |
| **Autenticazione tramite token di firma di accesso condiviso per l'archiviazione** | No | Sì, tramite [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) oppure [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a livello di istanza. |
| **Autenticazione tramite chiave di accesso alle risorse di archiviazione** | Sì, tramite [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **Autenticazione tramite Identità gestita per l'archiviazione** | Sì, tramite [credenziali dell'identità del servizio gestita](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sì, tramite credenziale `Managed Identity`. |
| **Autenticazione tramite identità applicazione per l'archiviazione** | [Sì](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **Autorizzazioni a livello di oggetto** | Sì, inclusa la possibilità di concedere, negare e revocare le autorizzazioni agli utenti | Sì, inclusa la possibilità di concedere, negare e revocare le autorizzazioni a utenti/account di accesso per gli oggetti di sistema supportati |
| **Autorizzazioni a livello di schema** | Sì, inclusa la possibilità di concedere, negare e revocare le autorizzazioni a utenti/account di accesso per lo schema | Sì, inclusa la possibilità di concedere, negare e revocare le autorizzazioni a utenti/account di accesso per lo schema |
| **Autorizzazioni a [livello di database](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Sì | Sì |
| **Autorizzazioni a [livello di server](/sql/relational-databases/security/authentication-access/server-level-roles)** | No | Sì, sono supportati sysadmin e altri ruoli del server |
| **Ruoli/gruppi** | Sì (con ambito di database) | Sì (con ambito sia di server che di database) |
| **Funzioni di sicurezza e identità** | Alcune funzioni e operatori di sicurezza Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, `OPEN/CLOSE MASTER KEY` | Alcune funzioni e operatori di sicurezza Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` e `REVERT`. Le funzioni di sicurezza non possono essere usate per eseguire query sui dati esterni. Archiviare il risultato in una variabile che può essere usata nella query.  |
| **DATABASE SCOPED CREDENTIAL** | Sì | Sì |

Il pool SQL e SQL su richiesta usano il linguaggio Transact-SQL standard per eseguire query sui dati. Per informazioni dettagliate sulle differenze, vedere le [informazioni di riferimento sul linguaggio Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Strumenti

È possibile usare vari strumenti per connettersi a Synapse SQL per eseguire query sui dati.

|   | Sottoposto a provisioning | Senza server |
| --- | --- | --- |
| **Synapse Studio** | Sì, script SQL | Sì, script SQL |
| **Power BI** | Sì | [Sì](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | Sì | Sì |
| **Azure Data Studio** | Sì | Sì, versione 1.18.0 o successiva. Gli script SQL e i notebook SQL sono supportati. |
| **SQL Server Management Studio** | Sì | Sì, versione 18.5 o successiva |

> [!NOTE]
> È possibile utilizzare SSMS per connettersi a SQL su richiesta (anteprima) e query. SSMS è parzialmente supportato a partire dalla versione 18.5, è possibile usarlo solo per connettersi ed eseguire query.

La maggior parte delle applicazioni che usano il linguaggio Transact-SQL standard possono eseguire query sia sui modelli di consumo con provisioning che serverless di Synapse SQL.

## <a name="storage-options"></a>Opzioni di archiviazione

I dati analizzati possono essere archiviati in vari tipi di archiviazione. La tabella seguente elenca tutte le opzioni di archiviazione disponibili:

|   | Sottoposto a provisioning | Senza server |
| --- | --- | --- |
| **Archiviazione interna** | Sì | No |
| **Azure Data Lake v2** | Sì | Sì |
| **Archiviazione BLOB di Azure** | Sì | Sì |

## <a name="data-formats"></a>Formati di dati

I dati analizzati possono essere archiviati in vari formati di archiviazione. La tabella seguente elenca tutti i formati di dati disponibili che è possibile analizzare:

|   | Sottoposto a provisioning | Senza server |
| --- | --- | --- |
| **Delimitato** | [Sì](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sì](query-single-csv-file.md) |
| **CSV** | Sì, (i delimitatori a più caratteri non sono supportati) | [Sì](query-single-csv-file.md) |
| **Parquet** | [Sì](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sì](query-parquet-files.md), inclusi i file con [tipi annidati](query-parquet-nested-types.md) |
| **Hive ORC** | [Sì](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **Hive RC** | [Sì](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **JSON** | Sì | [Sì](query-json-files.md) |
| **[Delta-lake](https://delta.io/)** | No | No |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | No | No |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle procedure consigliate per il pool SQL e SQL su richiesta, vedere gli articoli seguenti:

- [Procedure consigliate per il pool SQL](best-practices-sql-pool.md)
- [Procedure consigliate per SQL su richiesta](best-practices-sql-on-demand.md)
