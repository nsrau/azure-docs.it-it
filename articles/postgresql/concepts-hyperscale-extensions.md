---
title: 'Estensioni PostgreSQL nel database di Azure per PostgreSQL: iperscalabilità (CITUS)'
description: Descrive la possibilità di estendere la funzionalità del database usando le estensioni nel database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 41edcc40bae7915e321a278592dd2f73621aae05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482413"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Estensioni PostgreSQL nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

PostgreSQL offre la possibilità di estendere le funzionalità del database usando le estensioni. Le estensioni consentono di creare bundle di più oggetti SQL correlati in un singolo pacchetto che può essere caricato o rimosso dal database con un singolo comando. Una volta caricate nel database, le estensioni possono fungere da funzionalità predefinite. Per altre informazioni sulle estensioni di PostgreSQL, vedere creare [un pacchetto di oggetti correlati in un'estensione](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Usare le estensioni di PostgreSQL

Per poter usare le estensioni di PostgreSQL è prima necessario installarle nel database. Per installare un'estensione specifica, eseguire il comando [create extension](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) dallo strumento PSQL per caricare gli oggetti inclusi nel pacchetto nel database.

Database di Azure per PostgreSQL-iperscalabilità (CITUS) supporta attualmente un subset di estensioni chiave come elencato qui. Le estensioni diverse da quelle elencate non sono supportate. Non è possibile creare un'estensione personalizzata con database di Azure per PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Estensioni supportate dal database di Azure per PostgreSQL

Le tabelle seguenti includono un elenco delle estensioni standard di PostgreSQL attualmente supportate dal database di Azure per PostgreSQL. Queste informazioni sono disponibili anche eseguendo `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Estensioni di tipi di dati

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornisce un tipo stringa di caratteri che non distingue fra maiuscole e minuscole. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornisce un tipo di dati per i cubi multidimensionali. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornisce un tipo di dati per l'archiviazione di set di coppie chiave-valore. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornisce tipi di dati per gli standard di numerazione dei prodotti internazionali. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Large Object manutenzione. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornisce un tipo di dati per le strutture ad albero gerarchico. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo di dati per rappresentare segmenti di linea o intervalli a virgola mobile. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Digitare per Top-n JSONB. |

### <a name="full-text-search-extensions"></a>Estensioni di ricerca full-text

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornisce un modello di dizionario di ricerca di testo per i numeri interi. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modello del dizionario di ricerca del testo per l'elaborazione di sinonimi estesi. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Un dizionario di ricerca di testo che rimuove gli accenti (segni diacritici) dai lessemi. |

### <a name="functions-extensions"></a>Estensioni di funzioni

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funzioni per i campi di incremento automatico. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornisce un modo per calcolare le distanze ortodromiche sulla superficie terrestre. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Offre diverse funzioni per determinare analogie e distanza tra le stringhe. |
> | [Inserisci nome utente\_](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funzioni per il rilevamento delle modifiche apportate a una tabella. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Aggregator integer ed enumeratore (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornisce funzioni e operatori per la manipolazione delle matrici di interi senza null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funzioni per il rilevamento dell'ora dell'Ultima modifica. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornisce funzioni di crittografia. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gestisce le tabelle partizionate per ora o ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornisce funzioni e operatori per determinare la somiglianza del testo alfanumerico in base alla corrispondenza trigramma. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funzioni per l'implementazione dell'integrità referenziale (obsolete). |
> | analisi\_sessione | Funzioni per l'esecuzione di query sulle matrici HStore. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornisce funzioni che consentono di modificare intere tabelle, compresi i campi incrociati. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Notifiche delle modifiche attivate. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funzioni per l'implementazione del viaggio temporale. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genera identificatori universalmente univoci (UUID). |

### <a name="hyperscale-extensions"></a>Estensioni di iperscala

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | CITUS database distribuito. |
> | ribilanciamento\_partizione | Ribilanciare in modo sicuro i dati in un gruppo di server in caso di aggiunta o rimozione di nodi. |

### <a name="index-types-extensions"></a>Estensioni di tipi di indice

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Metodo di accesso Bloom-indice basato su file di firma. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornisce classi di operatori GIN di esempio che implementano il comportamento di tipo albero B per determinati tipi di dati. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornisce classi operatore indice GiST che implementano la struttura b-tree. |

### <a name="language-extensions"></a>Estensioni di linguaggio

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Linguaggio procedurale caricabile PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Estensioni varie

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funzioni amministrative per PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funzioni per la verifica dell'integrità della relazione. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper di dati esterni per l'accesso ai file flat. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Esaminare il contenuto delle pagine del database a un livello basso. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornisce un modo per esaminare ciò che avviene nella cache del buffer condiviso in tempo reale. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Utilità di pianificazione dei processi per PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Esaminare la mappa dello spazio disponibile (MSF). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornisce un modo per caricare i dati di relazione nella cache del buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornisce un modo per tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite da un server. Per informazioni su questa estensione, vedere la sezione "pg_stat_statements". |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | Esaminare la mappa di visibilità (VM) e le informazioni di visibilità a livello di pagina. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornisce un modo per visualizzare le informazioni di blocco a livello di riga. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornisce un modo per visualizzare le statistiche a livello di tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper di dati esterni usato per accedere ai dati archiviati in server PostgreSQL esterni. Per informazioni su questa estensione, vedere la sezione "dblink e postgres_fdw".|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informazioni sui certificati SSL. |
> | [TSM\_System\_righe](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Metodo TABLESAMPLE, che accetta il numero di righe come limite. |
> | [tempo\_di\_di TSM](https://www.postgresql.org/docs/current/tsm-system-time.html) | Metodo TABLESAMPLE, che accetta un limite di tempo in millisecondi. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Consente di creare indici ipotetici che non comportano un utilizzo eccessivo di CPU o disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Modulo che supporta le connessioni ad altri database PostgreSQL da una sessione di database. Per informazioni su questa estensione, vedere la sezione "dblink e postgres_fdw". |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Query XPath e XSLT. |


### <a name="postgis-extensions"></a>Estensioni di PostGIS

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Oggetti spaziali e geografici per PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Consente di analizzare un indirizzo nei suoi elementi costitutivi. Consente di supportare il passaggio di normalizzazione dell'indirizzo nella geocodifica. |
> | PostGIS\_sfcgal | Funzioni SFCGAL PostGIS. |
> | postgis\_tiger\_geocoder | Geocodificatore PostGIS Tiger e geocodificatore inverso. |
> | topologia\_PostGIS | Funzioni e tipi spaziali della topologia PostGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
L' [estensione delle istruzioni pg\_stat\_](https://www.postgresql.org/docs/current/pgstatstatements.html) viene precaricata in ogni database di Azure per il server PostgreSQL per fornire un modo per tenere traccia delle statistiche di esecuzione delle istruzioni SQL.

L'impostazione `pg_stat_statements.track` controlla le istruzioni che vengono conteggiate dall'estensione. Il valore predefinito è `top`, il che significa che vengono rilevate tutte le istruzioni rilasciate direttamente dai client. Gli altri due livelli di rilevamento sono `none` e `all`. Questa impostazione è configurabile come parametro del server tramite il [portale di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Esiste un compromesso tra le informazioni sull'esecuzione di query pg_stat_statements fornisce e l'effetto sulle prestazioni del server durante la registrazione di ogni istruzione SQL. Se non si usa attivamente l'estensione pg_stat_statements, è consigliabile impostare `pg_stat_statements.track` su `none`. Alcuni servizi di monitoraggio di terze parti possono basarsi su pg_stat_statements per fornire informazioni dettagliate sulle prestazioni delle query, quindi verificare se questo è il caso o meno.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
È possibile usare dblink e postgres_fdw per connettersi da un server PostgreSQL a un altro o a un altro database nello stesso server. Il server di destinazione deve consentire le connessioni dal server di origine attraverso il firewall. Per usare queste estensioni per la connessione tra i server di database di Azure per PostgreSQL, impostare **Consenti l'accesso ai servizi di Azure** su on. È inoltre necessario attivare questa impostazione se si desidera utilizzare le estensioni per eseguire il loopback allo stesso server. L'impostazione **Consenti l'accesso a servizi di Azure** è disponibile nella pagina portale di Azure per il server Postgres in **sicurezza connessione**. La **possibilità di abilitare l'accesso ai servizi di Azure** in whitelist tutti gli indirizzi IP di Azure.

Attualmente, le connessioni in uscita da database di Azure per PostgreSQL non sono supportate, tranne che per le connessioni ad altri server di database di Azure per PostgreSQL.
