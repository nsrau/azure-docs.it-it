---
title: Limiti di capacità-Azure sinapsi Analytics (in precedenza SQL DW)
description: Valori massimi consentiti per vari componenti di analisi SQL in sinapsi di Azure.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: e3661797ea408f219a67a1862901fee7c27a1d58
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123911"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Limiti di capacità di Azure sinapsi Analytics (precedentemente SQL DW)

Valori massimi consentiti per vari componenti di sinapsi di Azure.

## <a name="workload-management"></a>Gestione del carico di lavoro

| Categoria | DESCRIZIONE | Massima |
|:--- |:--- |:--- |
| [Unità Data Warehouse (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Numero massimo di DWU per una singola unità del pool SQL (data warehouse) | Prima generazione: DW6000<br></br>Seconda generazione: DW30000c |
| [Unità Data Warehouse (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |DTU predefinita per server |54.000<br></br>Per impostazione predefinita, ogni server SQL (ad esempio, myserver.database.windows.net) ha una quota DTU di 54.000, che consente fino a DW5000c. Questa quota è semplicemente un limite di sicurezza. È possibile aumentare la quota [creando un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md) e selezionando *Quota* come tipo di richiesta.  Per calcolare le esigenze di DTU, moltiplicare il 7,5 per il totale DWU necessario oppure moltiplicare 9,5 per il totale DWU necessario. Ad esempio:<br></br>DW6000 x 7,5 = 45.000 DTU<br></br>DW5000c x 9,5 = 47.500 DTU.<br></br>È possibile visualizzare l'utilizzo di DTU attuale nell'opzione SQL Server del portale. I database in pausa e non in pausa vengono conteggiati nella quota di DTU. |
| Connessione del database |Numero massimo di sessioni aperte simultanee |1024<br/><br/>Il numero di sessioni aperte simultanee può variare in base al DWU selezionato. DWU600c e versioni successive supportano un massimo di 1024 sessioni aperte. DWU500c e versioni precedenti supportano un limite massimo di sessioni aperte simultanee pari a 512. Si noti che vi sono limiti nel numero di query che è possibile eseguire contemporaneamente. Quando si supera il limite di concorrenza, la richiesta viene inviata a una coda interna in cui resta in attesa di elaborazione. |
| Connessione del database |Memoria massima per le istruzioni preparate |20 MB |
| [gestione del carico di lavoro](resource-classes-for-workload-management.md) |Numero massimo di query simultanee |128<br/><br/>  Verrà eseguito un massimo di 128 query simultanee e le query rimanenti verranno accodate.<br/><br/>Il numero di query simultanee può diminuire quando gli utenti vengono assegnati a classi di risorse superiori o quando l'impostazione dell' [unità data warehouse](memory-concurrency-limits.md) è ridotta. Per alcune query, come le query DMV, l'esecuzione è sempre consentita e non influisce sul limite di query simultanee. Per altre informazioni sull'esecuzione di query simultanee, vedere l'articolo sui [valori massimi di concorrenza](memory-concurrency-limits.md). |
| [tempdb](sql-data-warehouse-tables-temporary.md) |GB massimi |399 GB per DW100. Pertanto a DWU1000, tempdb ha le dimensioni di 3,99 TB. |

## <a name="database-objects"></a>Oggetti di database
| Categoria | DESCRIZIONE | Massima |
|:--- |:--- |:--- |
| Database |Dimensioni massime | Prima generazione: 240 TB compressi su disco. Questo spazio è indipendente dallo spazio di tempdb o del log ed è dedicato alle tabelle permanenti.  La compressione stimata per columnstore cluster è 5X.  Questa compressione consente al database di crescere fino a circa 1 PB quando tutte le tabelle sono columnstore cluster (tipo di tabella predefinito). <br/><br/> Seconda generazione: 240 TB per rowstore e archiviazione illimitata per le tabelle columnstore |
| tabella |Dimensioni massime |60 TB compressi su disco |
| tabella |Tabelle per database | 100.000 |
| tabella |Colonne per tabella |1024 colonne |
| tabella |Byte per colonna |Dipende dalla colonna [tipo di dati](sql-data-warehouse-tables-data-types.md). Il limite è 8000 per i tipi di dati char, 4000 per nvarchar o 2 GB per i tipi di dati MAX. |
| tabella |Byte per riga, dimensioni definite |8060 byte<br/><br/>Il numero di byte per riga viene calcolato come per SQL Server, con la compressione pagina. Come SQL Server, è supportata l'archiviazione di overflow della riga, che consente di eseguire il push delle **colonne a lunghezza variabile** all'esterno di righe. Quando le righe di lunghezza variabile vengono inviate all'esterno delle righe, viene archiviata nel record principale solo una radice 24 byte. Per altre informazioni, vedere [Dati di overflow della riga che superano 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| tabella |Partizioni per tabella |15.000<br/><br/>Per prestazioni elevate, è consigliabile ridurre al minimo il numero di partizioni necessarie pur supportando i requisiti aziendali. Con l'aumentare del numero di partizioni, l'overhead per le operazioni DDL (Data Definition Language) e DML (Data Manipulation Language ) aumenta e le prestazioni rallentano. |
| tabella |Caratteri per valore limite della partizione. |4000 |
| Indice |Indici non in cluster per tabella. |50<br/><br/>Si applica solo alle tabelle rowstore. |
| Indice |Indici in cluster per tabella. |1<br><br/>Si applica sia alle tabelle rowstore che columnstore. |
| Indice |Dimensioni della chiave indice. |900 byte.<br/><br/>Si applica solo agli indici rowstore.<br/><br/>È possibile creare indici in colonne varchar con una dimensione massima di oltre 900 byte se al momento della creazione dell'indice i dati esistenti in tali colonne non superano i 900 byte. Tuttavia, le azioni INSERT o UPDATE successive eseguite nelle colonne che causano un aumento delle dimensioni totali oltre i 900 byte avranno esito negativo. |
| Indice |Colonne chiave per indice. |16<br/><br/>Si applica solo agli indici rowstore. Gli indici columnstore in cluster includono tutte le colonne. |
| Statistiche |Dimensione dei valori combinati delle colonne. |900 byte. |
| Statistiche |Colonne per oggetto statistiche. |32 |
| Statistiche |Statistiche create per le colonne per tabella. |30.000 |
| Stored procedure |Livello massimo di annidamento. |8 |
| visualizzazione |Colonne per vista |1024 |

## <a name="loads"></a>Operazioni di caricamento
| Categoria | DESCRIZIONE | Massima |
|:--- |:--- |:--- |
| Operazioni di caricamento di PolyBase |MB per riga |1<br/><br/>La polibase carica righe di dimensioni inferiori a 1 MB. Il caricamento di tipi di dati LOB in tabelle con un indice columnstore cluster (CCI) non è supportato.<br/><br/> |

## <a name="queries"></a>Query
| Categoria | DESCRIZIONE | Massima |
|:--- |:--- |:--- |
| Query |Query in coda nelle tabelle utente |1000 |
| Query |Query simultanee nelle viste di sistema |100 |
| Query |Query in coda nelle viste di sistema |1000 |
| Query |Parametri massimi |2098 |
| Batch |Dimensione massima |65.536*4096 |
| Risultati SELECT |Colonne per riga |4096<br/><br/>Nel risultato SELECT non è possibile avere più di 4096 colonne per riga. Non è garantito che si possa avere sempre 4096. Se il piano di query richiede una tabella temporanea, potrebbe venire applicato il valore massimo di 1024 colonne per tabella. |
| SELECT |Sottoquery nidificate |32<br/><br/>In un'istruzione SELECT non è possibile avere più di 32 sottoquery nidificate. Non è garantito che si possa averne sempre 32. Ad esempio, un JOIN può introdurre una sottoquery nel piano di query. Il numero di sottoquery può essere limitato anche dalla memoria disponibile. |
| SELECT |Colonne per JOIN |1024 colonne<br/><br/>Nel JOIN non è mai possibile avere più di 1024 colonne. Non è garantito che si possa averne sempre 1024. Se il piano JOIN richiede una tabella temporanea con più colonne del risultato JOIN, il limite di 1024 viene applicato alla tabella temporanea. |
| SELECT |Byte per le colonne GROUP BY. |8060<br/><br/>Le colonne presenti nella clausola GROUP BY possono avere un massimo di 8060 byte. |
| SELECT |Byte per le colonne ORDER BY |8060 byte<br/><br/>Le colonne presenti nella clausola ORDER BY possono avere un massimo di 8060 byte. |
| Identificatori per istruzione |Numero di identificatori di riferimento |65.535<br/><br/> Il numero di identificatori che possono essere contenuti in una singola espressione di una query è limitato. Il superamento di questo numero genera un errore 8632 di SQL Server. Per altre informazioni, vedere la pagina relativa al messaggio [Errore interno: è stato raggiunto un limite per i servizi di gestione delle espressioni](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Valori letterali stringa | Numero di valori letterali stringa in un'istruzione | 20.000 <br/><br/>Il numero di costanti stringa in una singola espressione di una query è limitato. Il superamento di questo numero genera un errore 8632 di SQL Server.|

## <a name="metadata"></a>Metadati
| Vista di sistema | Righe massime |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10.000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Numero totale di ruoli di lavoro DMS per le 1000 richieste SQL più recenti. |
| sys.dm_pdw_errors |10.000 |
| sys.dm_pdw_exec_requests |10.000 |
| sys.dm_pdw_exec_sessions |10.000 |
| sys.dm_pdw_request_steps |Numero totale di passaggi per le 1000 richieste SQL più recenti archiviate in sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10.000 |
| sys.dm_pdw_sql_requests |Le 1000 richieste SQL più recenti archiviate in sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Passaggi successivi
Per consigli sull'uso di sinapsi di Azure, vedere il [foglio](cheat-sheet.md)informativo.
