---
title: "Limiti di capacità di SQL Data Warehouse | Documentazione Microsoft"
description: I valori massimi per le connessioni, i database, le tabelle e le query per SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 12/14/2017
ms.author: kevin;barbkess
ms.openlocfilehash: 3a8edb3806f981ebb6f8c1ca6c994ae198df2ec2
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="sql-data-warehouse-capacity-limits"></a>Limiti di capacità di SQL Data Warehouse
Le tabelle seguenti contengono i valori massimi consentiti per vari componenti di SQL Data Warehouse di Azure.

## <a name="workload-management"></a>Gestione del carico di lavoro
| Categoria | DESCRIZIONE | Massima |
|:--- |:--- |:--- |
| [Unità Data Warehouse (DWU)][Data Warehouse Units (DWU)] |Max DWU per un singolo SQL Data Warehouse | [Livello di prestazioni](performance-tiers.md) ottimizzato per l'elasticità: DW6000<br></br>[Livello di prestazioni](performance-tiers.md) ottimizzato per il calcolo: DW30000c |
| [Unità Data Warehouse (DWU)][Data Warehouse Units (DWU)] |DTU predefinita per server |54.000<br></br>Per impostazione predefinita, ogni server SQL, ad esempio myserver.database.windows.net, ha una quota DTU di 54.000 che consente al massimo DW6000c. Questa quota è semplicemente un limite di sicurezza. È possibile aumentare la quota [creando un ticket di supporto][creating a support ticket] e selezionando *Quota* come tipo di richiesta.  Per calcolare le esigenze in termini di DTU, moltiplicare 7,5 per il valore DWU totale necessario oppure moltiplicare per 9 il totale di DWU a elevato utilizzo di calcolo necessari. Ad esempio: <br></br>DW6000 x 7,5 = 45.000 DTU<br></br>DW600c x 9 = 54.000 DTU.<br></br>È possibile visualizzare l'utilizzo di DTU attuale nell'opzione SQL Server del portale. I database in pausa e non in pausa vengono conteggiati nella quota di DTU. |
| Connessione del database |Sessioni simultanee aperte |1024<br/><br/>Ognuna delle 1024 sessioni attive può inviare richieste a un database SQL Data Warehouse nello stesso momento. Si noti che vi sono limiti nel numero di query che è possibile eseguire contemporaneamente. Quando si supera il limite di concorrenza, la richiesta viene inviata a una coda interna in cui resta in attesa di elaborazione. |
| Connessione del database |Memoria massima per le istruzioni preparate |20 MB |
| [Gestione del carico di lavoro][Workload management] |Numero massimo di query simultanee |32<br/><br/> Per impostazione predefinita, SQL Data Warehouse può eseguire un massimo di 32 query simultanee e mette in coda le query rimanenti.<br/><br/>Il numero di query simultanee può diminuire quando gli utenti sono assegnati a classi di risorse superiori o quando SQL Data Warehouse ha un [livello di servizio](performance-tiers.md#service-levels) inferiore. Alcune query, come ad esempio le query DMV, possono essere sempre eseguite. |
| [tempdb][Tempdb] |GB massimi |399 GB per DW100. Pertanto a DWU1000, tempdb ha le dimensioni di 3,99 TB |

## <a name="database-objects"></a>Oggetti di database
| Categoria | DESCRIZIONE | Massima |
|:--- |:--- |:--- |
| Database |Dimensioni massime |240 TB compressi su disco<br/><br/>Questo spazio è indipendente dallo spazio di tempdb o del log ed è dedicato alle tabelle permanenti.  La compressione stimata per columnstore cluster è 5X.  Questa compressione consente al database di crescere fino a circa 1 PB quando tutte le tabelle sono columnstore cluster (tipo di tabella predefinito). |
| Tabella |Dimensioni massime |60 TB compressi su disco |
| Tabella |Tabelle per database |2 miliardi |
| Tabella |Colonne per tabella |1024 colonne |
| Tabella |Byte per colonna |Dipende dalla colonna [tipo di dati][data type].  Il limite è 8000 per i tipi di dati char, 4000 per nvarchar o 2 GB per i tipi di dati MAX. |
| Tabella |Byte per riga, dimensioni definite |8060 byte<br/><br/>Il numero di byte per riga viene calcolato come per SQL Server, con la compressione pagina. Come SQL Server, SQL Data Warehouse supporta l'archiviazione di dati di overflow della riga che consente di spostare le **colonne a lunghezza variabile** all'esterno delle righe. Quando le righe di lunghezza variabile vengono inviate all'esterno delle righe, viene archiviata nel record principale solo una radice 24 byte. Per altre informazioni, vedere [Dati di overflow della riga che superano 8 KB][Row-Overflow Data Exceeding 8 KB]. |
| Tabella |Partizioni per tabella |15.000<br/><br/>Per prestazioni elevate, è consigliabile ridurre al minimo il numero di partizioni necessarie garantendo al tempo stesso il supporto dei requisiti aziendali. Con l'aumentare del numero di partizioni, l'overhead per le operazioni DDL (Data Definition Language) e DML (Data Manipulation Language ) aumenta e le prestazioni rallentano. |
| Tabella |Caratteri per valore limite della partizione. |4000 |
| Indice |Indici non in cluster per tabella. |999<br/><br/>Si applica solo alle tabelle rowstore. |
| Indice |Indici in cluster per tabella. |1<br><br/>Si applica sia alle tabelle rowstore che columnstore. |
| Indice |Dimensioni della chiave indice. |900 byte.<br/><br/>Si applica solo agli indici rowstore.<br/><br/>È possibile creare indici in colonne varchar con una dimensione massima di oltre 900 byte se al momento della creazione dell'indice i dati esistenti in tali colonne non superano i 900 byte. Tuttavia, le azioni INSERT o UPDATE successive eseguite nelle colonne che causano un aumento delle dimensioni totali oltre i 900 byte avranno esito negativo. |
| Indice |Colonne chiave per indice. |16<br/><br/>Si applica solo agli indici rowstore. Gli indici columnstore in cluster includono tutte le colonne. |
| Statistiche |Dimensione dei valori combinati delle colonne. |900 byte. |
| Statistiche |Colonne per oggetto statistiche. |32 |
| Statistiche |Statistiche create per le colonne per tabella. |30.000 |
| Stored procedure |Livello massimo di annidamento. |8 |
| Visualizza |Colonne per vista |1.024 |

## <a name="loads"></a>Operazioni di caricamento
| Categoria | DESCRIZIONE | Massima |
|:--- |:--- |:--- |
| Operazioni di caricamento di PolyBase |MB per riga |1<br/><br/>Le operazioni di caricamento di PolyBase caricano solo righe inferiori a 1 MB che non possono essere caricate in VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX).<br/><br/> |

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
| SELECT |Byte per le colonne ORDER BY |8060 byte.<br/><br/>Le colonne presenti nella clausola ORDER BY possono avere un massimo di 8060 byte. |
| Identificatori e costanti per istruzione |Numero di identificatori e costanti di riferimento. |65.535<br/><br/>SQL Data Warehouse limita il numero di identificatori e costanti che possono essere contenuti in una singola espressione di query. Il limite è 65.535. Il superamento di questo numero genera un errore 8632 di SQL Server. Per altre informazioni, vedere [Errore interno: è stato raggiunto un limite di servizi di espressione][Internal error: An expression services limit has been reached]. |

## <a name="metadata"></a>Metadata
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
Per altre informazioni di riferimento, vedere la [panoramica degli argomenti di riferimento di SQL Data Warehouse][SQL Data Warehouse reference overview].

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050
