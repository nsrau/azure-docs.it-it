---
title: Estensioni – Hyperscale (Citus) - Database di Azure per PostgreSQLExtensions – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Descrive la possibilità di estendere la funzionalità del database usando le estensioni nel database di Azure per PostgreSQL - Hyperscale (Citus)
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485404"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Estensioni PostgreSQL in Database di Azure per PostgreSQL – Hyperscale (Citus)

PostgreSQL offre la possibilità di estendere la funzionalità del database utilizzando le estensioni. Le estensioni consentono di creare bundle di più oggetti SQL correlati in un singolo pacchetto che può essere caricato o rimosso dal database con un singolo comando. Dopo essere state caricate nel database, le estensioni possono funzionare come le funzionalità incorporate. Per ulteriori informazioni sulle estensioni PostgreSQL, consultate Creare pacchetti [di oggetti correlati in un'estensione.](https://www.postgresql.org/docs/current/static/extend-extensions.html)

## <a name="use-postgresql-extensions"></a>Usare le estensioni PostgreSQLUse PostgreSQL extensions

Per poter usare le estensioni di PostgreSQL è prima necessario installarle nel database. Per installare una particolare estensione, eseguire il comando [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) dallo strumento psql per caricare gli oggetti inclusi nel pacchetto nel database.

Database di Azure per PostgreSQL - Hyperscale (Citus) supporta attualmente un sottoinsieme di estensioni chiave elencate di seguito. Le estensioni diverse da quelle elencate non sono supportate. Non è possibile creare un'estensione personalizzata con Database di Azure per PostgreSQL.You can't create your own extension with Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Estensioni supportate dal database di Azure per PostgreSQL

Le tabelle seguenti includono un elenco delle estensioni standard di PostgreSQL attualmente supportate dal database di Azure per PostgreSQL. Queste informazioni sono disponibili anche eseguendo `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Estensioni di tipi di dati

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Fornisce un tipo stringa di caratteri che non distingue fra maiuscole e minuscole. |
> | [Cubo](https://www.postgresql.org/docs/current/static/cube.html) | Fornisce un tipo di dati per i cubi multidimensionali. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Fornisce un tipo di dati per l'archiviazione di set di coppie chiave-valore. |
> | [hll](https://github.com/citusdata/postgresql-hll) | Fornisce una struttura di dati HyperLogLog. |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | Fornisce tipi di dati per gli standard di numerazione dei prodotti internazionali. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenzione di oggetti di grandi dimensioni. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Fornisce un tipo di dati per le strutture ad albero gerarchico. |
> | [Seg](https://www.postgresql.org/docs/current/seg.html) | Tipo di dati per la rappresentazione di segmenti di linea o intervalli a virgola mobile. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Tipo per JSONB di primo n. |

### <a name="full-text-search-extensions"></a>Estensioni di ricerca full-text

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Fornisce un modello di dizionario di ricerca di testo per i numeri interi. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modello di dizionario di ricerca di testo per l'elaborazione di sinonimi estesi. |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Un dizionario di ricerca di testo che rimuove gli accenti (segni diacritici) dai lessemi. |

### <a name="functions-extensions"></a>Estensioni di funzioni

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [Autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funzioni per l'incremento automatico dei campi. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Fornisce un modo per calcolare le distanze ortodromiche sulla superficie terrestre. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Offre diverse funzioni per determinare analogie e distanza tra le stringhe. |
> | [inserire\_il nome utente](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funzioni per tenere traccia di chi ha modificato una tabella. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Aggregatore di numeri interi ed enumeratore (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Fornisce funzioni e operatori per la manipolazione delle matrici di interi senza null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funzioni per tenere traccia dell'ora dell'ultima modifica. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Fornisce funzioni di crittografia. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gestisce le tabelle partizionate per ora o ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Fornisce funzioni e operatori per determinare la somiglianza del testo alfanumerico in base alla corrispondenza trigramma. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funzioni per l'implementazione dell'integrità referenziale (obsoleta). |
> | analisi\_delle sessioni | Funzioni per l'esecuzione di query su matrici hstore. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Fornisce funzioni che consentono di modificare intere tabelle, compresi i campi incrociati. |
> | [Tcn](https://www.postgresql.org/docs/current/tcn.html) | Notifiche di modifica attivate. |
> | [viaggio nel tempo](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funzioni per l'implementazione di viaggi nel tempo. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Genera identificatori universalmente univoci (UUID). |

### <a name="hyperscale-extensions"></a>Estensioni Hyperscale

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Database distribuito Citus. |
> | ribilanciamento del servizio di bilanciamento del carico delle partizioni\_ | Ribilanciare in modo sicuro i dati in un gruppo di server in caso di aggiunta o rimozione di nodi. |

### <a name="index-types-extensions"></a>Estensioni dei tipi di indiceIndex types extensions

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [Fioritura](https://www.postgresql.org/docs/current/bloom.html) | Metodo di accesso Bloom - indice basato su file di firma. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Fornisce classi di operatori GIN di esempio che implementano un comportamento simile ad albero B per determinati tipi di dati. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Fornisce classi operatore indice GiST che implementano la struttura b-tree. |

### <a name="language-extensions"></a>Estensioni del linguaggio

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Linguaggio procedurale caricabile PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Estensioni varie

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funzioni amministrative per PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funzioni per verificare l'integrità della relazione. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper di dati esterni per l'accesso ai file flat. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Esaminare il contenuto delle pagine del database a un livello basso. |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Fornisce un modo per esaminare ciò che avviene nella cache del buffer condiviso in tempo reale. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Job scheduler per PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Esaminare la mappa dello spazio libero (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Fornisce un modo per caricare i dati di relazione nella cache del buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Fornisce un modo per tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite da un server. Vedere la sezione "pg_stat_statements" per informazioni su questa estensione. |
> | [visibilità pg\_](https://www.postgresql.org/docs/current/pgvisibility.html) | Esaminare la mappa di visibilità (VM) e le informazioni sulla visibilità a livello di pagina. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Fornisce un modo per visualizzare le informazioni di blocco a livello di riga. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Fornisce un modo per visualizzare le statistiche a livello di tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Wrapper di dati esterni usato per accedere ai dati archiviati in server PostgreSQL esterni. Vedere la sezione "dblink and postgres_fdw" per informazioni su questa estensione.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informazioni sui certificati SSL. |
> | [righe di\_\_sistema tsm](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TableSAMPLE, che accetta il numero di righe come limite. |
> | [tsm\_\_tempo di sistema](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE, che accetta il tempo in millisecondi come limite. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Consente di creare indici ipotetici che non comportano un utilizzo eccessivo di CPU o disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Modulo che supporta le connessioni ad altri database PostgreSQL da una sessione di database. Vedere la sezione "dblink and postgres_fdw" per informazioni su questa estensione. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Query XPath e XSLT. |


### <a name="postgis-extensions"></a>Estensioni di PostGIS

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Oggetti spaziali e geografici per PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Consente di analizzare un indirizzo nei suoi elementi costitutivi. Consente di supportare il passaggio di normalizzazione dell'indirizzo nella geocodifica. |
> | postgis\_sfcgal | funzioni SFCGAL PostGIS. |
> | geocodificatore\_tigre postgis\_ | Geocodificatore tigre PostGIS e geocodificatore inverso. |
> | topologia postgis\_ | Tipi spaziali e funzioni della topologia PostGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
L'estensione delle [istruzioni\_pg stat\_](https://www.postgresql.org/docs/current/pgstatstatements.html) viene precaricata in ogni database di Azure per il server PostgreSQL per fornire un mezzo per tenere traccia delle statistiche di esecuzione delle istruzioni SQL.

L'impostazione `pg_stat_statements.track` controlla quali istruzioni vengono conteggiate dall'estensione. Il valore `top`predefinito è , il che significa che viene tenuta traccia di tutte le istruzioni emesse direttamente dai client. Gli altri due livelli di rilevamento sono `none` e `all`. Questa impostazione è configurabile come parametro del server tramite il [portale di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Esiste un compromesso tra le informazioni di esecuzione della query fornite da pg_stat_statements e l'effetto sulle prestazioni del server durante la gestione della loging di ogni istruzione SQL. Se non si utilizza attivamente l'estensione pg_stat_statements, `pg_stat_statements.track` `none`è consigliabile impostare su . Alcuni servizi di monitoraggio di terze parti potrebbero basarsi su pg_stat_statements per fornire informazioni dettagliate sulle prestazioni delle query, quindi verificare se questo è il caso per l'utente o meno.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
È possibile utilizzare dblink e postgres_fdw per connettersi da un server PostgreSQL a un altro o a un altro database nello stesso server. Il server di destinazione deve consentire le connessioni dal server di origine attraverso il firewall. Per usare queste estensioni per connettersi tra il database di Azure per i server PostgreSQL, impostare **Consenti accesso ai servizi** di Azure su ON. È inoltre necessario attivare questa impostazione se si desidera utilizzare le estensioni per tornare allo stesso server. L'impostazione **Consenti accesso ai servizi** di Azure è disponibile nella pagina del portale di Azure per il server Postgres in Sicurezza della **connessione**. Attivazione di **Consenti accesso ai servizi** di Azure in bianco tutti gli indirizzi IP di Azure.Turning Allow access to Azure services ON whitelists all Azure IPs.

Attualmente, le connessioni in uscita dal database di Azure per PostgreSQL non sono supportate, ad eccezione delle connessioni ad altri database di Azure per i server PostgreSQL.Currently, outbound connections from Azure Database for PostgreSQL are not supported, except for connections to other Azure Database for PostgreSQL servers.
