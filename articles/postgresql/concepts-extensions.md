---
title: Usare le estensioni di PostgreSQL in Database di Azure per PostgreSQL - Server singolo
description: Descrive la possibilità di estendere le funzionalità del database usando le estensioni nel Database di Azure per PostgreSQL - singolo Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 412ce3c5245f3f22bfb03740a0451670dc6a90a7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448114"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Estensioni di PostgreSQL in Database di Azure per PostgreSQL - Server singolo
PostgreSQL offre la capacità di estendere le funzionalità del database usando le estensioni. Le estensioni consentono di creare bundle di più oggetti SQL correlati in un singolo pacchetto che può essere caricato o rimosso dal database con un singolo comando. Dopo averle caricate nel database, le estensioni possono operare come funzionalità predefinite. Per altre informazioni sulle estensioni di PostgreSQL, vedere  [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html) (Creare un pacchetto di oggetti correlati formando un'estensione).

## <a name="how-to-use-postgresql-extensions"></a>Come usare le estensioni di PostgreSQL
Per poter usare le estensioni di PostgreSQL è prima necessario installarle nel database. Per installare una determinata estensione, eseguire il comando  [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html)  dallo strumento psql per caricare gli oggetti del pacchetto nel database.

Il database di Azure per PostgreSQL attualmente supporta un subset delle estensioni chiave come indicato di seguito. Le estensioni diverse da quelle elencate non sono supportate. Non è possibile creare estensioni personalizzate con il servizio Database di Azure per PostgreSQL.

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
> | [orafce](https://github.com/orafce/orafce) | Fornisce un subset di funzioni e i pacchetti emulati dai database commerciali. |

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


### <a name="time-series-extensions"></a>Estensioni di serie temporali

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | Un database SQL di serie temporali che supporta automatizzata più veloce di partizionamento per inserire ed esegue una query. Fornisce basati sul tempo funzioni analitiche, le ottimizzazioni e offre la scalabilità PostgreSQL per carichi di lavoro di serie temporali. Viene sviluppato TimescaleDB e un marchio registrato di [scala cronologica, Inc.](https://www.timescale.com/) Per una nota relativa a questa estensione, vedere di seguito. |


## <a name="pgstatstatements"></a>pg_stat_statements
L'estensione [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) è precaricata in ogni server di Database di Azure per PostgreSQL per consentire di tenere traccia delle statistiche di esecuzione delle istruzioni SQL.
L'impostazione `pg_stat_statements.track`, che controlla le istruzioni conteggiate mediante l'estensione, è impostata su `top`. Ciò consente di rilevare tutte le istruzioni eseguite direttamente dai client. Gli altri due livelli di rilevamento sono `none` e `all`. Questa impostazione è configurabile come parametro del server tramite il [portale di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

È necessario trovare un compromesso tra le informazioni sull'esecuzione di query fornite da pg_stat_statements e l'impatto della registrazione di ogni istruzione SQL sulle prestazioni del server. Se non si usa attivamente l'estensione pg_stat_statements, è consigliabile impostare `pg_stat_statements.track` su `none`. Si noti che alcuni servizi di monitoraggio di terze parti possono basarsi su pg_stat_statements per fornire informazioni dettagliate sulle prestazioni delle query. Verificare se questo è il proprio caso.

## <a name="dblink-and-postgresfdw"></a>dblink e postgres_fdw
dblink e postgres_fdw consentono di connettersi da un server PostgreSQL a un altro oppure a un altro database nello stesso server. Il server di destinazione deve consentire le connessioni dal server di origine attraverso il firewall. Quando si usano queste estensioni per la connessione tra server Database di Azure per PostgreSQL, questa operazione può essere eseguita attivando l'impostazione "Consenti l'accesso a Servizi di Azure". Questo è necessario anche quando si vogliono usare le estensioni per il loopback allo stesso server. L'impostazione "Consenti l'accesso a Servizi di Azure" è disponibile nella pagina del portale di Azure per il server Postgres, nell'area Sicurezza connessione. Quando si attiva "Consenti l'accesso a Servizi di Azure", tutti gli indirizzi IP di Azure vengono inclusi nell'elenco elementi consentiti.

Attualmente, le connessioni in uscita dal Database di Azure per PostgreSQL non sono supportate, ad eccezione delle connessioni da altri Database di Azure per i server PostgreSQL.

## <a name="uuid"></a>uuid
Se si prevede di usare `uuid_generate_v4()` dall'estensione per l'uuid ossp, prendere in considerazione il confronto con `gen_random_uuid()` dall'estensione pgcrypto per i vantaggi delle prestazioni.


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB è un database di serie temporali che viene fornito come un'estensione per PostgreSQL. TimescaleDB fornisce basati sul tempo funzioni analitiche, le ottimizzazioni e offre la scalabilità Postgres per carichi di lavoro di serie temporali.

[Altre informazioni su TimescaleDB](https://docs.timescale.com/latest), un marchio registrato di [scala cronologica, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Installazione TimescaleDB
Per installare TimescaleDB, è necessario includerlo nelle librerie di precaricamento condivisa del server. Una modifica di Postgres `shared_preload_libraries` parametro è necessario specificare un **riavvio del server** effettive. È possibile modificare i parametri usando il [portale di Azure](howto-configure-server-parameters-using-portal.md) o il [CLI Azure](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> TimescaleDB può essere abilitata nel Database di Azure per PostgreSQL versione 9.6 e 10

Usando il [portale di Azure](https://portal.azure.com/):

1. Selezionare il server di Database di Azure per PostgreSQL.

2. Sulla barra laterale, selezionare **i parametri del Server**.

3. Cercare il `shared_preload_libraries` parametro.

4. Selezionare **TimescaleDB**.

5. Selezionare **salvare** per mantenere le modifiche. Dopo avere salvata la modifica, si riceverà una notifica. 

6. Dopo la notifica **riavviare** server per rendere effettive le modifiche. Per informazioni su come riavviare un server, vedere [Riavviare un server di Database di Azure per PostgreSQL](howto-restart-server-portal.md).


È ora possibile abilitare TimescaleDB nel database Postgres. Connettersi al database ed eseguire il comando seguente:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Se viene visualizzato un errore, verificare che si [riavviato il server](howto-restart-server-portal.md) dopo il salvataggio shared_preload_libraries. 

È ora possibile creare un hypertable TimescaleDB [da zero](https://docs.timescale.com/getting-started/creating-hypertables) oppure eseguire la migrazione [i dati di serie temporali esistenti presenti in PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Passaggi successivi
Segnalare a Microsoft la mancanza di un'estensione che si desidera usare. Votare per le richieste esistenti o creare nuove richieste di feedback nel nostro [forum dei commenti](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
