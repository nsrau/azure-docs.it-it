---
title: Usare sys_schema-database di Azure per MySQL
description: Informazioni su come usare sys_schema per individuare i problemi di prestazioni e gestire il database in database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 12/02/2019
ms.openlocfilehash: 50552b87fad9d8f58ff8c48dc03463d4c901bf99
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775946"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Come usare sys_schema per l'ottimizzazione delle prestazioni e la manutenzione del database in Database di Azure per MySQL

Il performance_schema MySQL, disponibile per la prima volta in MySQL 5,5, fornisce la strumentazione per molte risorse server essenziali, ad esempio l'allocazione di memoria, i programmi archiviati, il blocco dei metadati e così via. Tuttavia, il performance_schema contiene più di 80 tabelle e il recupero delle informazioni necessarie spesso richiede l'Unione di tabelle all'interno del performance_schema, nonché le tabelle dal information_schema. Basato su performance_schema e information_schema, sys_schema offre un'efficace raccolta di [viste intuitive](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) in un database di sola lettura completamente abilitato in Database di Azure per MySQL versione 5.7.

![viste di sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Il database sys_schema include 52 viste, ognuna con uno dei prefissi seguenti:

- Host_summary o IO: latenze correlate alle operazioni di I/O.
- InnoDB: stato e blocchi del buffer InnoDB.
- Memory: utilizzo della memoria da parte di host e utenti.
- Schema: informazioni correlate allo schema, come incremento automatico, indici e così via.
- Statement: informazioni sulle istruzioni SQL; può trattarsi di istruzioni che causano una scansione di tabella completa o una durata prolungata delle query.
- User: risorse utilizzate e raggruppate in base agli utenti. Ad esempio I/O su file, connessioni e memoria.
- Wait: eventi di attesa raggruppati in base a host o utente.

Di seguito verranno esaminati alcuni modelli di utilizzo comuni di sys_schema. Per iniziare, i modelli di utilizzo verranno raggruppati in due categorie: **ottimizzazione delle prestazioni** e **manutenzione del database**.

## <a name="performance-tuning"></a>Ottimizzazione delle prestazioni

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

Le operazioni di I/O sono le più dispendiose nel database. È possibile scoprire la latenza media di I/O tramite una query sulla vista *sys.user_summary_by_file_io*. Con il valore predefinito di 125 GB di spazio di archiviazione sottoposto a provisioning, la latenza di I/O è di circa 15 secondi.

![Latenza di I/O: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Dato che Database di Azure per MySQL ridimensiona le risorse di I/O in riferimento allo spazio di archiviazione, con l'aumento dello spazio di archiviazione sottoposto a provisioning a 1 TB, la latenza di I/O si riduce a 571 ms.

![Latenza di I/O: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Anche con un'attenta pianificazione, molte query possono comunque comportare scansioni di tabella complete. Per altre informazioni sui tipi di indici e su come ottimizzarli, è possibile fare riferimento a questo articolo: [Come usare EXPLAIN per profilare le prestazioni delle query in Database di Azure per MySQL](./howto-troubleshoot-query-performance.md). Le scansioni di tabella complete comportano un elevato utilizzo di risorse e influiscono negativamente sulle prestazioni del database. Il modo più rapido per individuare le tabelle con scansione di tabella completa consiste nell'eseguire una query sulla vista *sys.schema_tables_with_full_table_scans*.

![scansioni di tabella complete](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Per risolvere i problemi di prestazioni del database, potrebbe essere utile identificare gli eventi che si verificano all'interno del database e la vista *sys.user_summary_by_statement_type* può essere perfetta a questo scopo.

![riepilogo in base alle istruzioni](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

In questo esempio, Database di Azure per MySQL ha dedicato 53 minuti allo scaricamento del log di query slog 44579 volte. Si tratta di molto tempo e di molte operazioni di I/O. È possibile ridurre questa attività disabilitando il log query lente o riducendo la frequenza di registrazione nel log query lente nel portale di Azure.

## <a name="database-maintenance"></a>Manutenzione del database

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

Il pool di buffer InnoDB risiede in memoria e rappresenta il principale meccanismo di cache tra il sistema di gestione di database e l'archiviazione. Le dimensioni del pool di buffer InnoDB sono associate al livello di prestazioni e non possono essere modificate, se non scegliendo uno SKU di prodotto diverso. Come nel caso della memoria nel sistema operativo, viene effettuato lo swapping delle pagine meno recenti per fare spazio a dati più nuovi. Per scoprire quali tabelle utilizzano la maggior parte della memoria del pool di buffer InnoDB, è possibile eseguire una query sulla vista *sys.innodb_buffer_stats_by_table*.

![stato del buffer InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Nella figura precedente è evidente che, escludendo le tabelle e le viste di sistema, ogni tabella nel database mysqldatabase033, che ospita uno dei siti WordPress, occupa 16 KB, ovvero 1 pagina, di dati in memoria.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Gli indici sono strumenti validi per migliorare le prestazioni di lettura, ma comportano costi aggiuntivi per inserimenti e archiviazione. *Sys.schema_unused_indexes* e *sys.schema_redundant_indexes* forniscono informazioni dettagliate sugli indici inutilizzati o duplicati.

![indici inutilizzati](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![indici ridondanti](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Conclusione

In sintesi, il database sys_schema è un valido strumento sia per l'ottimizzazione delle prestazioni che per la manutenzione del database. Assicurarsi di sfruttare i vantaggi di questa funzionalità in Database di Azure per MySQL. 

## <a name="next-steps"></a>Passaggi successivi
- Per trovare risposte dai colleghi alle domande più pressanti o per pubblicare una nuova domanda o risposta, visitare il [forum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
