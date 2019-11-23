---
title: Usare le estensioni di PostgreSQL nel database di Azure per PostgreSQL-server singolo
description: Descrive la possibilità di estendere la funzionalità del database usando le estensioni nel database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 4f81b23378427faa522071f4a20e07485f5c3387
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296424"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Estensioni di PostgreSQL nel database di Azure per PostgreSQL-server singolo
PostgreSQL offre la capacità di estendere le funzionalità del database usando le estensioni. Le estensioni raggruppano più oggetti SQL correlati in un singolo pacchetto che può essere caricato o rimosso dal database con un unico comando. Dopo essere stato caricato nel database, le estensioni funzionano come le funzionalità predefinite.

## <a name="how-to-use-postgresql-extensions"></a>Come usare le estensioni di PostgreSQL
Per poter usare le estensioni di PostgreSQL è prima necessario installarle nel database. Per installare una determinata estensione, eseguire il comando  [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html)  dallo strumento psql per caricare gli oggetti del pacchetto nel database.

Database di Azure per PostgreSQL supporta un subset di estensioni chiave come indicato di seguito. Queste informazioni sono disponibili anche eseguendo `SELECT * FROM pg_available_extensions;`. Le estensioni oltre a quelle elencate non sono supportate. Non è possibile creare un'estensione personalizzata nel database di Azure per PostgreSQL.

## <a name="postgres-11-extensions"></a>Estensioni Postgres 11

Le estensioni seguenti sono disponibili nei server di database di Azure per PostgreSQL con Postgres versione 11. 

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Esempio di set di dati degli indirizzi standardizzati|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | supporto per l'indicizzazione di tipi di DataType comuni in GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1,5             | supporto per l'indicizzazione di tipi di DataType comuni in GiST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1,5             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | tipo di dati per i cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | modello del dizionario di ricerca del testo per numeri interi|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | calcola le distanze di grandi cerchio sulla superficie della terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | determinare le analogie e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1,5             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Indici ipotetici per PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funzioni, operatori e supporto per gli indici per matrici unidimensionali di numeri interi|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | tipi di dati per gli standard di numerazione dei prodotti internazionali|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | tipo di dati per le strutture ad albero gerarchico|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funzioni e operatori che emulano un subset di funzioni e pacchetti da un RDBMS commerciale|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funzioni di crittografia|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | Estensione pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Mostra informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1,5             | Mostra statistiche a livello di tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | esaminare la cache del buffer condivisa|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Estensione per gestire le tabelle partizionate in base all'ora o all'ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dati della relazione preriscaldati|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | misurazione della somiglianza del testo e ricerca nell'indice basata su trigrammi|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Lingua procedurale PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | Linguaggio procedurale attendibile PL/JavaScript (V8)|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometria, geografia e tipi spaziali e funzioni raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geocodificatore PostGIS Tiger e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Funzioni e tipi spaziali della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funzioni che modificano intere tabelle, inclusi i campi incrociati|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | Abilita inserimenti scalabili e query complesse per i dati di serie temporali|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | genera identificatori universalmente univoci (UUID)|

## <a name="postgres-10-extensions"></a>Estensioni Postgres 10 

Le estensioni seguenti sono disponibili nei server di database di Azure per PostgreSQL con Postgres versione 10.

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Esempio di set di dati degli indirizzi standardizzati|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | supporto per l'indicizzazione di tipi di DataType comuni in GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1,5             | supporto per l'indicizzazione di tipi di DataType comuni in GiST|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | tipo di dati per le password crittografate automaticamente|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | tipo di dati per i cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | modello del dizionario di ricerca del testo per numeri interi|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | calcola le distanze di grandi cerchio sulla superficie della terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | determinare le analogie e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indici ipotetici per PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | funzioni, operatori e supporto per gli indici per matrici unidimensionali di numeri interi|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | tipi di dati per gli standard di numerazione dei prodotti internazionali|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | tipo di dati per le strutture ad albero gerarchico|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funzioni e operatori che emulano un subset di funzioni e pacchetti da un RDBMS commerciale|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | funzioni di crittografia|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | Estensione pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | Mostra informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1,5             | Mostra statistiche a livello di tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | esaminare la cache del buffer condivisa|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Estensione per gestire le tabelle partizionate in base all'ora o all'ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | dati della relazione preriscaldati|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | misurazione della somiglianza del testo e ricerca nell'indice basata su trigrammi|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Lingua procedurale PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Linguaggio procedurale attendibile PL/JavaScript (V8)|
> |[PostGIS](https://www.postgis.net/)                      | 2.4.3           | PostGIS geometria, geografia e tipi spaziali e funzioni raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | Geocodificatore PostGIS Tiger e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Funzioni e tipi spaziali della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funzioni che modificano intere tabelle, inclusi i campi incrociati|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Abilita inserimenti scalabili e query complesse per i dati di serie temporali|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | genera identificatori universalmente univoci (UUID)|

## <a name="postgres-96-extensions"></a>Estensioni Postgres 9,6 

Le estensioni seguenti sono disponibili nei server di database di Azure per PostgreSQL con Postgres versione 9,6.

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Esempio di set di dati degli indirizzi standardizzati|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | supporto per l'indicizzazione di tipi di DataType comuni in GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | supporto per l'indicizzazione di tipi di DataType comuni in GiST|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | tipo di dati per le password crittografate automaticamente|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | tipo di dati per i cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | modello del dizionario di ricerca del testo per numeri interi|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | calcola le distanze di grandi cerchio sulla superficie della terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | determinare le analogie e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indici ipotetici per PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | funzioni, operatori e supporto per gli indici per matrici unidimensionali di numeri interi|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | tipi di dati per gli standard di numerazione dei prodotti internazionali|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | tipo di dati per le strutture ad albero gerarchico|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funzioni e operatori che emulano un subset di funzioni e pacchetti da un RDBMS commerciale|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | funzioni di crittografia|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | Estensione pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | Mostra informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | Mostra statistiche a livello di tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | esaminare la cache del buffer condivisa|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Estensione per gestire le tabelle partizionate in base all'ora o all'ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | dati della relazione preriscaldati|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | misurazione della somiglianza del testo e ricerca nell'indice basata su trigrammi|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Lingua procedurale PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Linguaggio procedurale attendibile PL/JavaScript (V8)|
> |[PostGIS](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometria, geografia e tipi spaziali e funzioni raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | Geocodificatore PostGIS Tiger e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Funzioni e tipi spaziali della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funzioni che modificano intere tabelle, inclusi i campi incrociati|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Abilita inserimenti scalabili e query complesse per i dati di serie temporali|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | genera identificatori universalmente univoci (UUID)|

## <a name="postgres-95-extensions"></a>Estensioni Postgres 9,5 

Le estensioni seguenti sono disponibili nei server di database di Azure per PostgreSQL con Postgres versione 9,5.

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Esempio di set di dati degli indirizzi standardizzati|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | supporto per l'indicizzazione di tipi di DataType comuni in GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | supporto per l'indicizzazione di tipi di DataType comuni in GiST|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | tipo di dati per le password crittografate automaticamente|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | tipo di dati per i cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | modello del dizionario di ricerca del testo per numeri interi|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | calcola le distanze di grandi cerchio sulla superficie della terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | determinare le analogie e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indici ipotetici per PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | funzioni, operatori e supporto per gli indici per matrici unidimensionali di numeri interi|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | tipi di dati per gli standard di numerazione dei prodotti internazionali|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | tipo di dati per le strutture ad albero gerarchico|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funzioni e operatori che emulano un subset di funzioni e pacchetti da un RDBMS commerciale|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | funzioni di crittografia|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | Estensione pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | Mostra informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | Mostra statistiche a livello di tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | esaminare la cache del buffer condivisa|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Estensione per gestire le tabelle partizionate in base all'ora o all'ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | dati della relazione preriscaldati|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | misurazione della somiglianza del testo e ricerca nell'indice basata su trigrammi|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Lingua procedurale PL/pgSQL|
> |[PostGIS](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometria, geografia e tipi spaziali e funzioni raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | Geocodificatore PostGIS Tiger e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Funzioni e tipi spaziali della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funzioni che modificano intere tabelle, inclusi i campi incrociati|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | genera identificatori universalmente univoci (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
L'estensione pg_stat_statements viene precaricata in ogni database di Azure per il server PostgreSQL per fornire un modo per tenere traccia delle statistiche di esecuzione delle istruzioni SQL.
L'impostazione `pg_stat_statements.track`, che controlla le istruzioni conteggiate mediante l'estensione, è impostata su `top`. Ciò consente di rilevare tutte le istruzioni eseguite direttamente dai client. Gli altri due livelli di rilevamento sono `none` e `all`. Questa impostazione è configurabile come parametro del server tramite il [portale di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

È necessario trovare un compromesso tra le informazioni sull'esecuzione di query fornite da pg_stat_statements e l'impatto della registrazione di ogni istruzione SQL sulle prestazioni del server. Se non si usa attivamente l'estensione pg_stat_statements, è consigliabile impostare `pg_stat_statements.track` su `none`. Si noti che alcuni servizi di monitoraggio di terze parti possono basarsi su pg_stat_statements per fornire informazioni dettagliate sulle prestazioni delle query. Verificare se questo è il proprio caso.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
dblink e postgres_fdw consentono di connettersi da un server PostgreSQL a un altro oppure a un altro database nello stesso server. Il server di destinazione deve consentire le connessioni dal server di origine attraverso il firewall. Quando si usano queste estensioni per la connessione tra server Database di Azure per PostgreSQL, questa operazione può essere eseguita attivando l'impostazione "Consenti l'accesso a Servizi di Azure". Questo è necessario anche quando si vogliono usare le estensioni per il loopback allo stesso server. L'impostazione "Consenti l'accesso a Servizi di Azure" è disponibile nella pagina del portale di Azure per il server Postgres, nell'area Sicurezza connessione. Quando si attiva "Consenti l'accesso ai servizi di Azure" in vengono inseriti tutti gli indirizzi IP di Azure nell'elenco Consenti.

Attualmente, le connessioni in uscita da database di Azure per PostgreSQL non sono supportate, tranne che per le connessioni ad altri server di database di Azure per PostgreSQL.

## <a name="uuid"></a>uuid
Se si prevede di usare `uuid_generate_v4()` dall'estensione uuid-ossp, provare a confrontare con `gen_random_uuid()` dall'estensione pgcrypto per ottenere i vantaggi delle prestazioni.


## <a name="pgaudit"></a>pgAudit
L'estensione pgAudit fornisce la registrazione di controllo di sessione e oggetti. Per informazioni su come usare questa estensione in database di Azure per PostgreSQL, vedere l' [articolo sui concetti di controllo](concepts-audit.md). 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB è un database di serie temporali assemblato come estensione per PostgreSQL. TimescaleDB fornisce funzioni analitiche basate sul tempo, ottimizzazioni e scale Postgres per i carichi di lavoro di serie temporali.

[Scopri di più su TimescaleDB](https://docs.timescale.com/latest), un marchio registrato di [scala cronologica, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Installazione di TimescaleDB
Per installare TimescaleDB, è necessario includerlo nelle librerie di precaricamento condivise del server. Per rendere effettive le modifiche apportate al parametro `shared_preload_libraries` di Postgres, è necessario **riavviare il server** . È possibile modificare i parametri usando il [portale di Azure](howto-configure-server-parameters-using-portal.md) o l'interfaccia della riga di comando di [Azure](howto-configure-server-parameters-using-cli.md).

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
