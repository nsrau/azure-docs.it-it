---
title: Usare le estensioni di PostgreSQL nel database di Azure per PostgreSQL-server singolo
description: Descrive la possibilità di estendere la funzionalità del database usando le estensioni nel database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: 93cc02fafcfa153c452f37c2bc69bb47e2629f1d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998071"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Estensioni di PostgreSQL nel database di Azure per PostgreSQL-server singolo
PostgreSQL offre la capacità di estendere le funzionalità del database usando le estensioni. Le estensioni raggruppano più oggetti SQL correlati in un singolo pacchetto che può essere caricato o rimosso dal database con un unico comando. Dopo essere stato caricato nel database, le estensioni funzionano come le funzionalità predefinite.

## <a name="how-to-use-postgresql-extensions"></a>Come usare le estensioni di PostgreSQL
Per poter usare le estensioni di PostgreSQL è prima necessario installarle nel database. Per installare una determinata estensione, eseguire il comando  [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html)  dallo strumento psql per caricare gli oggetti del pacchetto nel database.

Database di Azure per PostgreSQL supporta un subset di estensioni chiave come indicato di seguito. Le estensioni oltre a quelle elencate non sono supportate. Non è possibile creare un'estensione personalizzata nel database di Azure per PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Estensioni supportate dal database di Azure per PostgreSQL
Le tabelle seguenti includono un elenco delle estensioni standard di PostgreSQL attualmente supportate dal database di Azure per PostgreSQL. Queste informazioni sono disponibili anche eseguendo `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Estensioni di tipi di dati

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Fornisce un tipo di dati per le password con crittografia automatica. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornisce un tipo stringa di caratteri che non distingue fra maiuscole e minuscole. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornisce un tipo di dati per i cubi multidimensionali. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornisce un tipo di dati per l'archiviazione dei set di coppie chiave/valore. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornisce tipi di dati per gli standard di numerazione dei prodotti internazionali. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornisce un tipo di dati per le strutture ad albero gerarchico. |

### <a name="functions-extensions"></a>Estensioni di funzioni

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornisce un modo per calcolare le distanze ortodromiche sulla superficie terrestre. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Offre diverse funzioni per determinare analogie e distanza tra le stringhe. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornisce funzioni e operatori per la manipolazione delle matrici di interi senza null. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornisce funzioni di crittografia. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gestisce le tabelle partizionate per ora o ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornisce funzioni e operatori per determinare la somiglianza del testo alfanumerico in base alla corrispondenza trigramma. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornisce funzioni che consentono di modificare intere tabelle, compresi i campi incrociati. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genera identificatori universalmente univoci (UUID). Per una nota relativa a questa estensione, vedere di seguito. |
> | [orafce](https://github.com/orafce/orafce) | Fornisce un subset di funzioni e pacchetti emulati da database commerciali. |

### <a name="full-text-search-extensions"></a>Estensioni di ricerca full-text

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornisce un modello di dizionario di ricerca di testo per i numeri interi. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Un dizionario di ricerca di testo che rimuove gli accenti (segni diacritici) dai lessemi. |

### <a name="index-types-extensions"></a>Estensioni di tipi di indice

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornisce classi operatore GIN di esempio che implementano un comportamento simile alla struttura b-tree per determinati tipi di dati. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornisce classi operatore indice GiST che implementano la struttura b-tree. |

### <a name="language-extensions"></a>Estensioni di linguaggio

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Linguaggio procedurale caricabile PL/pgSQL. |
> | [plv8](https://plv8.github.io/) | Estensione del linguaggio Javascript per PostgreSQL che può essere usata per stored procedure, trigger e così via. |

### <a name="miscellaneous-extensions"></a>Estensioni varie

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornisce un modo per esaminare ciò che avviene nella cache del buffer condiviso in tempo reale. |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornisce un modo per caricare i dati di relazione nella cache del buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornisce un modo per tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite da un server. Per una nota relativa a questa estensione, vedere di seguito. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornisce un modo per visualizzare le informazioni di blocco a livello di riga. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornisce un modo per visualizzare le statistiche a livello di tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper di dati esterni usato per accedere ai dati archiviati in server PostgreSQL esterni. Per una nota relativa a questa estensione, vedere di seguito.|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Consente di creare indici ipotetici che non comportano un utilizzo eccessivo di CPU o disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Modulo che supporta le connessioni ad altri database PostgreSQL da una sessione di database. Per una nota relativa a questa estensione, vedere di seguito. |


### <a name="postgis-extensions"></a>Estensioni di PostGIS

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Oggetti spaziali e geografici per PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Consente di analizzare un indirizzo nei suoi elementi costitutivi. Consente di supportare il passaggio di normalizzazione dell'indirizzo nella geocodifica. |
> | [pgrouting](https://pgrouting.org/) | Estende il database geospaziale PostGIS/PostgreSQL per fornire funzionalità di routing geospaziale. |


### <a name="time-series-extensions"></a>Estensioni serie temporali

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | Un database SQL di serie temporali che supporta il partizionamento automatico per l'inserimento e le query più veloci. Fornisce funzioni analitiche basate sul tempo, ottimizzazioni e scale PostgreSQL per carichi di lavoro di serie temporali. TimescaleDB viene sviluppato da e un marchio registrato di [scala cronologica, Inc.](https://www.timescale.com/) Per una nota relativa a questa estensione, vedere di seguito. |

## <a name="postgres-11-extensions"></a>Estensioni Postgres 11

Le estensioni seguenti sono disponibili nei server di database di Azure per PostgreSQL con Postgres versione 11.

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Esempio di set di dati degli indirizzi standardizzati|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | supporto per l'indicizzazione di tipi di DataType comuni in GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1,5             | supporto per l'indicizzazione di tipi di DataType comuni in GiST|
> |[citext](https://www.postgresql.org/docs/11/static/citext.html)                       | 1,5             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[cube](https://www.postgresql.org/docs/11/static/cube.html)                         | 1.4             | tipo di dati per i cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/11/static/dict-int.html)                     | 1.0             | modello del dizionario di ricerca del testo per numeri interi|
> |[earthdistance](https://www.postgresql.org/docs/11/static/earthdistance.html)                | 1.1             | calcola le distanze di grandi cerchio sulla superficie della terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/static/fuzzystrmatch.html)                | 1.1             | determinare le analogie e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/11/static/hstore.html)                       | 1,5             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Indici ipotetici per PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/static/intarray.html)                     | 1.2             | funzioni, operatori e supporto per gli indici per matrici unidimensionali di numeri interi|
> |[isn](https://www.postgresql.org/docs/11/static/isn.html)                          | 1.2             | tipi di dati per gli standard di numerazione dei prodotti internazionali|
> |[ltree](https://www.postgresql.org/docs/11/static/ltree.html)                        | 1.1             | tipo di dati per le strutture ad albero gerarchico|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funzioni e operatori che emulano un subset di funzioni e pacchetti da un RDBMS commerciale|
> |[pgcrypto](https://www.postgresql.org/docs/11/static/pgcrypto.html)                     | 1.3             | funzioni di crittografia|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | Estensione pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/static/pgrowlocks.html)                   | 1.2             | Mostra informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/11/static/pgstattuple.html)                  | 1,5             | Mostra statistiche a livello di tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/11/static/pgbuffercache.html)               | 1.3             | esaminare la cache del buffer condivisa|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Estensione per gestire le tabelle partizionate in base all'ora o all'ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dati della relazione preriscaldati|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/static/pgstatstatements.html)           | 1.6             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/11/static/pgtrgm.html)                      | 1.4             | misurazione della somiglianza del testo e ricerca nell'indice basata su trigrammi|
> |[plpgsql](https://www.postgresql.org/docs/11/static/plpgsql.html)                      | 1.0             | Lingua procedurale PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | Linguaggio procedurale attendibile PL/JavaScript (V8)|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometria, geografia e tipi spaziali e funzioni raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geocodificatore PostGIS Tiger e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Funzioni e tipi spaziali della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/static/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[tablefunc](https://www.postgresql.org/docs/11/static/tablefunc.html)                    | 1.0             | funzioni che modificano intere tabelle, inclusi i campi incrociati|
> |[unaccent](https://www.postgresql.org/docs/11/static/unaccent.html)                     | 1.1             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/11/static/uuid-ossp.html)                    | 1.1             | genera identificatori universalmente univoci (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
L'estensione pg_stat_statements viene precaricata in ogni database di Azure per il server PostgreSQL per fornire un modo per tenere traccia delle statistiche di esecuzione delle istruzioni SQL.
L'impostazione `pg_stat_statements.track`, che controlla le istruzioni conteggiate mediante l'estensione, è impostata su `top`. Ciò consente di rilevare tutte le istruzioni eseguite direttamente dai client. Gli altri due livelli di rilevamento sono `none` e `all`. Questa impostazione è configurabile come parametro del server tramite il [portale di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

È necessario trovare un compromesso tra le informazioni sull'esecuzione di query fornite da pg_stat_statements e l'impatto della registrazione di ogni istruzione SQL sulle prestazioni del server. Se non si usa attivamente l'estensione pg_stat_statements, è consigliabile impostare `pg_stat_statements.track` su `none`. Si noti che alcuni servizi di monitoraggio di terze parti possono basarsi su pg_stat_statements per fornire informazioni dettagliate sulle prestazioni delle query. Verificare se questo è il proprio caso.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
dblink e postgres_fdw consentono di connettersi da un server PostgreSQL a un altro oppure a un altro database nello stesso server. Il server di destinazione deve consentire le connessioni dal server di origine attraverso il firewall. Quando si usano queste estensioni per la connessione tra server Database di Azure per PostgreSQL, questa operazione può essere eseguita attivando l'impostazione "Consenti l'accesso a Servizi di Azure". Questo è necessario anche quando si vogliono usare le estensioni per il loopback allo stesso server. L'impostazione "Consenti l'accesso a Servizi di Azure" è disponibile nella pagina del portale di Azure per il server Postgres, nell'area Sicurezza connessione. Quando si attiva "Consenti l'accesso ai servizi di Azure" in vengono inseriti tutti gli indirizzi IP di Azure nell'elenco Consenti.

Attualmente, le connessioni in uscita da database di Azure per PostgreSQL non sono supportate, tranne che per le connessioni ad altri server di database di Azure per PostgreSQL.

## <a name="uuid"></a>uuid
Se si prevede di usare `uuid_generate_v4()` dall'estensione uuid-ossp, provare a confrontare con `gen_random_uuid()` dall'estensione pgcrypto per ottenere i vantaggi delle prestazioni.


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB è un database di serie temporali assemblato come estensione per PostgreSQL. TimescaleDB fornisce funzioni analitiche basate sul tempo, ottimizzazioni e scale Postgres per i carichi di lavoro di serie temporali.

[Scopri di più su TimescaleDB](https://docs.timescale.com/latest), un marchio registrato di [scala cronologica, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Installazione di TimescaleDB
Per installare TimescaleDB, è necessario includerlo nelle librerie di precaricamento condivise del server. Per rendere effettiva una modifica al `shared_preload_libraries` parametro Postgres è necessario **riavviare il server** . È possibile modificare i parametri usando il [portale di Azure](howto-configure-server-parameters-using-portal.md) o l'interfaccia della riga di comando di [Azure](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> TimescaleDB può essere abilitato nel database di Azure per PostgreSQL versioni 9,6 e 10

Utilizzando la [portale di Azure](https://portal.azure.com/):

1. Selezionare il server di Database di Azure per PostgreSQL.

2. Nella barra laterale selezionare **parametri server**.

3. Cercare il `shared_preload_libraries` parametro.

4. Selezionare **TimescaleDB**.

5. Selezionare **Save (Salva** ) per mantenere le modifiche. Una volta salvata la modifica, si riceve una notifica. 

6. Dopo la notifica, **riavviare** il server per applicare le modifiche. Per informazioni su come riavviare un server, vedere [Riavviare un server di Database di Azure per PostgreSQL](howto-restart-server-portal.md).


È ora possibile abilitare TimescaleDB nel database postgres. Connettersi al database ed eseguire il comando seguente:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Se viene visualizzato un errore, confermare di aver [riavviato il server](howto-restart-server-portal.md) dopo avere salvato shared_preload_libraries. 

È ora possibile creare un'ipertabella TimescaleDB [da zero](https://docs.timescale.com/getting-started/creating-hypertables) o migrare [i dati di serie temporali esistenti in PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Passaggi successivi
Segnalare a Microsoft la mancanza di un'estensione che si desidera usare. Votare per le richieste esistenti o creare nuove richieste di feedback nel [Forum dei commenti](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
