---
title: Consigli sulle prestazioni di Database Advisor per database singoli e in pool
description: Il database SQL di Azure offre suggerimenti per i database singoli e in pool che possono migliorare le prestazioni delle query nel database SQL di Azure.Azure SQL Database provides recommendations for single and pooled databases that can improve query performance in Azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: bd7473813722fd413947535413b98d493058634a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214146"
---
# <a name="database-advisor-performance-recommendations-for-single-and-pooled-databases"></a>Suggerimenti sulle prestazioni di Database Advisor per database singoli e in pool

Il database SQL di Azure è in grado di apprendere e di adattarsi all'applicazione Per i database singoli e in pool, il database SQL include numerosi consulenti di database che forniscono suggerimenti personalizzati che consentono di ottimizzare le prestazioni. Questi consulenti di database valutano e analizzano continuamente la cronologia di utilizzo e forniscono consigli basati su modelli di carico di lavoro che consentono di migliorare le prestazioni.

## <a name="performance-overview"></a>Panoramica delle prestazioni

La panoramica delle prestazioni fornisce un riepilogo delle prestazioni del database e consente l'ottimizzazione delle prestazioni e la risoluzione dei problemi.

![Panoramica delle prestazioni per il database SQL di AzurePerformance overview for Azure SQL Database](./media/sql-database-performance/performance-overview-annotated.png)

- Il riquadro **Raccomandazioni** offre un elenco di raccomandazioni di ottimizzazione per il database (vengono visualizzate le tre raccomandazioni principali se ne sono presenti di più). Facendo clic su questo riquadro si passa alle opzioni di **[raccomandazione Prestazioni](sql-database-advisor-portal.md#viewing-recommendations)**.
- Il riquadro **Attività di ottimizzazione** fornisce un riepilogo delle operazioni di ottimizzazione in corso e completate per il database e offre una vista rapida della cronologia delle attività di ottimizzazione. Se si fa clic su questo riquadro viene visualizzata la cronologia di ottimizzazione completa per il database.
- Il riquadro **Ottimizzazione automatica** mostra la **[configurazione di ottimizzazione automatica](sql-database-automatic-tuning-enable.md)** per il database (opzioni di ottimizzazione applicate automaticamente al database). Se si fa clic su questo riquadro viene visualizzata la finestra di dialogo di configurazione dell'automazione.
- Il riquadro **Query su database** visualizza un riepilogo delle prestazioni delle query per il database (uso complessivo di DTU e query con il maggior consumo di risorse). Se si fa clic su questo riquadro viene visualizzata la pagina **[Informazioni dettagliate prestazioni query](sql-database-query-performance.md)**.

## <a name="performance-recommendation-options"></a>Opzioni di raccomandazione delle prestazioniPerformance recommendation options

Le opzioni di raccomandazione delle prestazioni disponibili per i database singoli e in pool nel database SQL di Azure sono:Performance recommendation options available for single and pooled databases in Azure SQL Database are:

| Raccomandazione per le prestazioni | Supporto dei database singoli e in pool | Supporto del database dell'istanza |
| :----------------------------- | ----- | ----- |
| **Creare consigli sugli indici:** consiglia la creazione di indici che possono migliorare le prestazioni del carico di lavoro. | Sì | No |
| **Suggerimenti** per l'indice di eliminazione: è consigliabile rimuovere ogni giorno gli indici ridondanti e duplicati, ad eccezione degli indici univoci e degli indici non utilizzati per un periodo di tempo prolungato (>90 giorni). Si noti che questa opzione non è compatibile con le applicazioni che usano cambi di partizione e hint di indice. L'eliminazione degli indici inutilizzati non è supportata per i livelli di servizio Premium e Business Critical. | Sì | No |
| **Parametrizzare le query recommendations (anteprima):** consiglia la parametrizzazione forzata nei casi in cui si dispone di una o più query che vengono costantemente ricompilate ma finiscono con lo stesso piano di esecuzione delle query. | Sì | No |
| Correggere i suggerimenti relativi ai **problemi dello schema (anteprima):** quando il servizio Database SQL rileva un'anomalia nel numero di errori SQL correlati allo schema che si verificano nel database SQL. Le raccomandazioni relative alla correzione dei problemi di schema sono attualmente in fase di deprecazione. | Sì | No |

![Consigli sulle prestazioni per il database SQL di AzurePerformance recommendations for Azure SQL Database](./media/sql-database-performance/performance-recommendations-annotated.png)

Per applicare i suggerimenti sulle prestazioni, vedere [Applicazione di suggerimenti](sql-database-advisor-portal.md#applying-recommendations). Per visualizzare lo stato dei suggerimenti, vedere Operazioni di [monitoraggio](sql-database-advisor-portal.md#monitoring-operations).

È anche possibile trovare la cronologia completa delle azioni di ottimizzazione applicate in precedenza.

## <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici

Il database SQL monitora in modo continuo le query in esecuzione e identifica gli indici che potrebbero migliorare le prestazioni. Se la mancanza di un determinato indice è individuata con sufficiente attendibilità, viene creata una nuova raccomandazione **Crea indice**.

Il database SQL di Azure valuta l'attendibilità stimando il miglioramento delle prestazioni che l'indice potrebbe offrire nel tempo. In base al miglioramento delle prestazioni stimato, le raccomandazioni vengono classificate come alta, media o bassa.

Gli indici creati tramite raccomandazioni sono sempre contrassegnati come indici creati automaticamente. È possibile vedere quali indici vengono creati automaticamente esaminando la [vista sys.indexes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Gli indici creati automaticamente non bloccano i comandi ALTER/RENAME.

Se si tenta di eliminare la colonna associata a un indice creato automaticamente, il comando passa e l'indice creato automaticamente viene eliminato con il comando. Gli indici normali bloccano il comando ALTER/RENAME sulle colonne indicizzate.

Dopo l'applicazione delle raccomandazioni per l'indice, il database SQL di Azure confronta le prestazioni delle query con quelle di base. Se il nuovo indice migliora le prestazioni, la raccomandazione è contrassegnata come positiva e il report di impatto diventa disponibile. Se l'indice non ha migliorato le prestazioni, viene ripristinato automaticamente. Il database SQL Usa questo processo per garantire che le raccomandazioni migliorino le prestazioni del database.

Per ogni raccomandazione **crea indice**, esistono criteri di sicurezza che non consentono di applicare la raccomandazione se l'uso delle risorse di un database o di un pool è elevato. I criteri di sicurezza tengono conto di CPU, I/O dati, I/O log e spazio di archiviazione disponibile.

Se CPU, I/O dati o I/O di log è superiore all'80% nei 30 minuti precedenti, la raccomandazione di creazione dell'indice viene posticipata. Se lo spazio di archiviazione disponibile sarà inferiore del 10% dopo la creazione dell'indice, la raccomandazione entra in uno stato di errore. Se, dopo un paio di giorni, si ritiene che l'indice sia ancora utile per l'ottimizzazione automatica, il processo inizia nuovamente.

Questo processo viene ripetuto fino a quando non è presente spazio di archiviazione sufficiente per creare un indice o fino a quando l'indice non è più considerato utile.

## <a name="drop-index-recommendations"></a>Raccomandazioni relative all'eliminazione di indici

Oltre a rilevare indici mancanti, il database SQL analizza continuamente le prestazioni degli indici esistenti. Se un indice non viene usato, il database SQL di Azure consiglia di eliminarlo. L'eliminazione di un indice è consigliabile in due casi:

- L'indice è un duplicato di un altro indice (ovvero con colonna, schema di partizione e filtri identici)
- L'indice non è stato usato per un periodo prolungato (93 giorni).

Anche le raccomandazioni relative all'eliminazione di indici sono sottoposte a verifica dopo l'implementazione. Se le prestazioni migliorano, il report di impatto diventa disponibile. Se le prestazioni diminuiscono, la raccomandazione viene ripristinata.

## <a name="parameterize-queries-recommendations-preview"></a>Parametrizzare i suggerimenti per le query (anteprima)Parameterize queries recommendations (preview)

Le raccomandazioni relative alla *creazione di query con parametri* vengono visualizzate quando sono presenti una o più query che vengono ricompilate costantemente ma finiscono con lo stesso piano di esecuzione di query. Questa determina la possibilità di applicare la parametrizzazione forzata che consente a sua volta di memorizzare nella cache i piani di query e di usarli nuovamente in futuro al fine di migliorare le prestazioni e ridurre l'uso delle risorse.

Ogni query eseguita inizialmente su SQL Server deve essere compilata per generare un piano di esecuzione e ogni piano generato viene aggiunto alla cache dei piani. Nelle esecuzioni successive della stessa query è possibile usare nuovamente tale piano, condizione che elimina la necessità di un'altra compilazione.

Le query con valori senza parametri possono provocare un overhead delle prestazioni perché il piano di esecuzione viene ricompilato ogni volta che i valori senza parametri sono diversi. In molti casi, le stesse query con valori di parametro diversi generano gli stessi piani di esecuzione che tuttavia vengono aggiunti ancora separatamente alla cache dei piani.

Il processo di ricompilazione dei piani di esecuzione usa risorse del database, aumenta il tempo di durata delle query e provoca un overflow della cache dei piani. Questi eventi, a loro volta, provocano la rimozione dei piani dalla cache. Questo comportamento di SQL Server può essere modificato impostando l'opzione di parametrizzazione forzata nel database.

Per stimare l'impatto della raccomandazione, viene offerto un confronto tra l'uso effettivo e quello previsto della CPU (ovvero nel caso di applicazione della raccomandazione). Tale raccomandazione consente di ottenere risparmi in termini di CPU e di ridurre la durata delle query e l'overhead per la cache dei piani, in modo che più piani possano rimanere nella cache ed essere usati nuovamente. È possibile applicare questa raccomandazione rapidamente selezionando il comando **Applica**.

Dopo l'applicazione della raccomandazione, in pochi minuti nel database viene applicata la parametrizzazione forzata che avvia il processo di monitoraggio, che dura circa 24 ore. Dopo questo periodo, è possibile visualizzare il report di convalida. Il report mostra l'uso della CPU del database di 24 ore prima e dopo l'applicazione della raccomandazione. Advisor per database SQL include un meccanismo di sicurezza che ripristina automaticamente la raccomandazione applicata nel caso in cui venga rilevata una regressione delle prestazioni.

## <a name="fix-schema-issues-recommendations-preview"></a>Raccomandazioni relative alla correzione di problemi di schema (anteprima)

> [!IMPORTANT]
> Le raccomandazioni relative alla correzione dei problemi di schema sono attualmente in fase di deprecazione. È consigliabile usare [Intelligent Insights](sql-database-intelligent-insights.md) per monitorare i problemi di prestazioni del database, ad esempio i problemi di schema interessati in precedenza dalle raccomandazioni di correzione indicate.

Le raccomandazioni **correzioni di problema di schema** vengono visualizzate quando il servizio di database SQL rileva un'anomalia nel numero di errori SQL correlati allo schema nel database SQL. In genere questa raccomandazione viene visualizzata quando il database rileva più errori correlati allo schema (nome di colonna o di oggetto non valido e così via) nell'arco di un'ora.

"Problemi di schema" sono una classe di errori di sintassi in SQL Server. Tali errori si verificano quando la definizione della query SQL e quella dello schema del database non sono allineate. Una delle colonne previste dalla query, ad esempio, potrebbe essere assente nella tabella di destinazione o viceversa.

La raccomandazione "Correzione del problema dello schema" viene visualizzata quando il servizio Database SQL di Azure rileva un'anomalia nel numero di errori SQL correlati allo schema che si verificano nel database SQL. La tabella seguente illustra gli errori correlati ai problemi di schema:

| Codice di errore SQL | Message |
| --- | --- |
| 201 |La procedura o funzione '*' richiede il parametro '*', che non è stato specificato. |
| 207 |Il nome di colonna '*' non è valido. |
| 208 |Il nome di oggetto '*' non è valido. |
| 213 |Il nome della colonna o il numero dei valori specificati non corrisponde alla definizione della tabella. |
| 2812 |Non è stato possibile trovare la stored procedure '*'. |
| 8144 |Numero eccessivo di argomenti specificati per la procedura o la funzione *. |

## <a name="custom-applications"></a>Applicazioni personalizzate

Gli sviluppatori potrebbero prendere in considerazione lo sviluppo di applicazioni personalizzate usando consigli sulle prestazioni per il database SQL di Azure.Developers might consider developing custom applications using performance recommendations for Azure SQL Database. Tutti i consigli elencati nel portale per un database sono accessibili tramite l'API [Get-AzSqlDatabaseRecommendedAction.All](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) recommendations listed in the portal for a database can be accessed through Get-AzSqlDatabaseRecommendedAction API.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'ottimizzazione automatica di indici del database e di piani di esecuzione di query, vedere [Ottimizzazione automatica nel database SQL di Azure](sql-database-automatic-tuning.md).
- Per altre informazioni sul monitoraggio automatico delle prestazioni del database con diagnostica automatizzata e analisi della causa radice dei problemi di prestazioni, vedere [Intelligent Insights in Azure SQL](sql-database-intelligent-insights.md).
- Per imparare a esaminare l'impatto sulle prestazioni delle query principali, vedere [Query Performance Insight del database SQL di Azure](sql-database-query-performance.md).
