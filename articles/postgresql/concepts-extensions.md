---
title: Estensioni - Database di Azure per PostgreSQL - Server singoloExtensions - Azure Database for PostgreSQL - Single Server
description: Informazioni sulle estensioni Postgres disponibili nel database di Azure per PostgreSQL - Server singoloLearn about the available Postgres extensions in Azure Database for PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201272"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL extensions in Azure Database for PostgreSQL - Single Server
PostgreSQL offre la capacità di estendere le funzionalità del database usando le estensioni. Le estensioni raggruppano più oggetti SQL correlati in un unico pacchetto che può essere caricato o rimosso dal database con un singolo comando. Dopo essere state caricate nel database, le estensioni funzionano come le funzionalità incorporate.

## <a name="how-to-use-postgresql-extensions"></a>Come usare le estensioni di PostgreSQL
Per poter usare le estensioni di PostgreSQL è prima necessario installarle nel database. Per installare una particolare estensione, eseguire il comando [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) dallo strumento psql per caricare gli oggetti inclusi nel pacchetto nel database.

Il database di Azure per PostgreSQL supporta un sottoinsieme di estensioni chiave elencate di seguito. Queste informazioni sono disponibili anche eseguendo `SELECT * FROM pg_available_extensions;`. Le estensioni oltre a quelle elencate non sono supportate. Non è possibile creare un'estensione personalizzata nel database di Azure per PostgreSQL.You cannot create your own extension in Azure Database for PostgreSQL.

## <a name="postgres-11-extensions"></a>Postgres 11 estensioni

Le estensioni seguenti sono disponibili nel database di Azure per i server PostgreSQL con Postgres versione 11.The following extensions are available in Azure Database for PostgreSQL servers that have Postgres version 11. 

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Esempio di set di dati Address Standardizer US|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | supporto per l'indicizzazione dei tipi di dati comuni nel GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | supporto per l'indicizzazione di tipi di dati comuni in GiST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[Cubo](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | tipo di dati per cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | modello di dizionario di ricerca di testo per numeri interi|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | calcolare le distanze di grande cerchio sulla superficie della Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | determinare le somiglianze e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Indici ipotetici per PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funzioni, operatori e supporto dell'indice per matrici 1D di numeri interi|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | tipi di dati per gli standard internazionali di numerazione dei prodotti|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | tipo di dati per strutture ad albero gerarchiche|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funzioni e operatori che emulano un sottoinsieme di funzioni e pacchetti da RDBMS commerciali|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funzioni crittografiche|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | Estensione pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | visualizzare le informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | visualizzare le statistiche a livello di tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | esaminare la cache del buffer condiviso|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Estensione per gestire le tabelle partizionate in base al tempo o all'ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dati di relazione preriscaldata|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | misurazione della somiglianza del testo e ricerca dell'indice in base ai trigrammi|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Linguaggio procedurale PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | Linguaggio procedurale affidabile PL/JavaScript (v8)|
> |[Postgis](https://www.postgis.net/)                      | 2.5.1           | Funzioni e tipi spaziali di geometria, geografia e raster PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geocodificatore tigre PostGIS e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Tipi spaziali e funzioni della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funzioni che manipolano intere tabelle, tra cui la tabella incrociata|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | Consente inserimenti scalabili e query complesse per i dati di serie temporali|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | generare identificatori universalmente univoci (UUID)|

## <a name="postgres-10-extensions"></a>Postgres 10 estensioni 

Le estensioni seguenti sono disponibili nel database di Azure per i server PostgreSQL con Postgres versione 10.The following extensions are available in Azure Database for PostgreSQL servers that have Postgres version 10.

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Esempio di set di dati Address Standardizer US|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | supporto per l'indicizzazione dei tipi di dati comuni nel GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | supporto per l'indicizzazione di tipi di dati comuni in GiST|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | tipo di dati per le password crittografate automaticamente|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[Cubo](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | tipo di dati per cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | modello di dizionario di ricerca di testo per numeri interi|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | calcolare le distanze di grande cerchio sulla superficie della Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | determinare le somiglianze e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indici ipotetici per PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | funzioni, operatori e supporto dell'indice per matrici 1D di numeri interi|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | tipi di dati per gli standard internazionali di numerazione dei prodotti|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | tipo di dati per strutture ad albero gerarchiche|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funzioni e operatori che emulano un sottoinsieme di funzioni e pacchetti da RDBMS commerciali|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | funzioni crittografiche|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | Estensione pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | visualizzare le informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | visualizzare le statistiche a livello di tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | esaminare la cache del buffer condiviso|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Estensione per gestire le tabelle partizionate in base al tempo o all'ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | dati di relazione preriscaldata|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | misurazione della somiglianza del testo e ricerca dell'indice in base ai trigrammi|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Linguaggio procedurale PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Linguaggio procedurale affidabile PL/JavaScript (v8)|
> |[Postgis](https://www.postgis.net/)                      | 2.4.3           | Funzioni e tipi spaziali di geometria, geografia e raster PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | Geocodificatore tigre PostGIS e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Tipi spaziali e funzioni della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funzioni che manipolano intere tabelle, tra cui la tabella incrociata|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Consente inserimenti scalabili e query complesse per i dati di serie temporali|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | generare identificatori universalmente univoci (UUID)|

## <a name="postgres-96-extensions"></a>Estensioni Postgres 9.6 

Le estensioni seguenti sono disponibili nel database di Azure per i server PostgreSQL con Postgres versione 9.6.The following extensions are available in Azure Database for PostgreSQL servers that have Postgres version 9.6.

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Esempio di set di dati Address Standardizer US|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | supporto per l'indicizzazione dei tipi di dati comuni nel GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | supporto per l'indicizzazione di tipi di dati comuni in GiST|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | tipo di dati per le password crittografate automaticamente|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[Cubo](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | tipo di dati per cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | modello di dizionario di ricerca di testo per numeri interi|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | calcolare le distanze di grande cerchio sulla superficie della Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | determinare le somiglianze e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indici ipotetici per PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | funzioni, operatori e supporto dell'indice per matrici 1D di numeri interi|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | tipi di dati per gli standard internazionali di numerazione dei prodotti|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | tipo di dati per strutture ad albero gerarchiche|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funzioni e operatori che emulano un sottoinsieme di funzioni e pacchetti da RDBMS commerciali|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | funzioni crittografiche|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | Estensione pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | visualizzare le informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | visualizzare le statistiche a livello di tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | esaminare la cache del buffer condiviso|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Estensione per gestire le tabelle partizionate in base al tempo o all'ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | dati di relazione preriscaldata|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | misurazione della somiglianza del testo e ricerca dell'indice in base ai trigrammi|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Linguaggio procedurale PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Linguaggio procedurale affidabile PL/JavaScript (v8)|
> |[Postgis](https://www.postgis.net/)                      | 2.3.2           | Funzioni e tipi spaziali di geometria, geografia e raster PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | Geocodificatore tigre PostGIS e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Tipi spaziali e funzioni della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funzioni che manipolano intere tabelle, tra cui la tabella incrociata|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Consente inserimenti scalabili e query complesse per i dati di serie temporali|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | generare identificatori universalmente univoci (UUID)|

## <a name="postgres-95-extensions"></a>Estensioni Postgres 9.5 

Le estensioni seguenti sono disponibili nel database di Azure per i server PostgreSQL con Postgres versione 9.5.The following extensions are available in Azure Database for PostgreSQL servers that have Postgres version 9.5.

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Esempio di set di dati Address Standardizer US|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | supporto per l'indicizzazione dei tipi di dati comuni nel GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | supporto per l'indicizzazione di tipi di dati comuni in GiST|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | tipo di dati per le password crittografate automaticamente|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[Cubo](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | tipo di dati per cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | modello di dizionario di ricerca di testo per numeri interi|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | calcolare le distanze di grande cerchio sulla superficie della Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | determinare le somiglianze e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indici ipotetici per PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | funzioni, operatori e supporto dell'indice per matrici 1D di numeri interi|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | tipi di dati per gli standard internazionali di numerazione dei prodotti|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | tipo di dati per strutture ad albero gerarchiche|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Funzioni e operatori che emulano un sottoinsieme di funzioni e pacchetti da RDBMS commerciali|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | funzioni crittografiche|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | Estensione pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | visualizzare le informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | visualizzare le statistiche a livello di tupla|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | esaminare la cache del buffer condiviso|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Estensione per gestire le tabelle partizionate in base al tempo o all'ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | dati di relazione preriscaldata|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | misurazione della somiglianza del testo e ricerca dell'indice in base ai trigrammi|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Linguaggio procedurale PL/pgSQL|
> |[Postgis](https://www.postgis.net/)                      | 2.3.0           | Funzioni e tipi spaziali di geometria, geografia e raster PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | Geocodificatore tigre PostGIS e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Tipi spaziali e funzioni della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funzioni che manipolano intere tabelle, tra cui la tabella incrociata|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | generare identificatori universalmente univoci (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
[L'estensione pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) viene precaricata in ogni database di Azure per il server PostgreSQL per fornire un mezzo per tenere traccia delle statistiche di esecuzione delle istruzioni SQL.
L'impostazione `pg_stat_statements.track`, che controlla le istruzioni conteggiate mediante l'estensione, è impostata su `top`. Ciò consente di rilevare tutte le istruzioni eseguite direttamente dai client. Gli altri due livelli di rilevamento sono `none` e `all`. Questa impostazione è configurabile come parametro del server tramite il [portale di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

È necessario trovare un compromesso tra le informazioni sull'esecuzione di query fornite da pg_stat_statements e l'impatto della registrazione di ogni istruzione SQL sulle prestazioni del server. Se non si usa attivamente l'estensione pg_stat_statements, è consigliabile impostare `pg_stat_statements.track` su `none`. Si noti che alcuni servizi di monitoraggio di terze parti possono basarsi su pg_stat_statements per fornire informazioni dettagliate sulle prestazioni delle query. Verificare se questo è il proprio caso.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) e [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) consentono di connettersi da un server PostgreSQL a un altro o a un altro database nello stesso server. Il server di destinazione deve consentire le connessioni dal server di origine attraverso il firewall. Quando si usano queste estensioni per la connessione tra server Database di Azure per PostgreSQL, questa operazione può essere eseguita attivando l'impostazione "Consenti l'accesso a Servizi di Azure". Questo è necessario anche quando si vogliono usare le estensioni per il loopback allo stesso server. L'impostazione "Consenti l'accesso a Servizi di Azure" è disponibile nella pagina del portale di Azure per il server Postgres, nell'area Sicurezza connessione. Se si fa "Consenti accesso ai servizi di Azure" attivate, tutti gli indirizzi IP di Azure vengono inseriti nell'elenco Consenti.Turning "Allow access to Azure services" on puts all Azure IPs on the Allow list.

Attualmente, le connessioni in uscita dal database di Azure per PostgreSQL non sono supportate, ad eccezione delle connessioni ad altri database di Azure per i server PostgreSQL.

## <a name="uuid"></a>uuid
Se si prevede `uuid_generate_v4()` di utilizzare dall'estensione [uuid-ossp](https://www.postgresql.org/docs/current/uuid-ossp.html), è consigliabile eseguire il confronto con `gen_random_uuid()` dall'estensione [pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) per ottenere vantaggi in termini di prestazioni.

## <a name="pgaudit"></a>pgAudit
[L'estensione pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) fornisce la registrazione di controllo di sessioni e oggetti. Per informazioni su come usare questa estensione nel database di Azure per PostgreSQL, vedere l'articolo sui concetti di [controllo.](concepts-audit.md) 

## <a name="pg_prewarm"></a>pg_prewarm
L'estensione pg_prewarm carica i dati relazionali nella cache. Prewarming your caches significa che le query hanno tempi di risposta migliori alla prima esecuzione dopo un riavvio. In Postgres 10 e sotto, prewarming viene fatto manualmente utilizzando la [funzione prewarm](https://www.postgresql.org/docs/10/pgprewarm.html).

In Postgres 11 e versioni successive, è possibile configurare la preriscaldamento in modo che avvenga [automaticamente.](https://www.postgresql.org/docs/current/pgprewarm.html) È necessario includere pg_prewarm `shared_preload_libraries` nell'elenco del parametro e riavviare il server per applicare la modifica. I parametri possono essere impostati dal modello Portale di [Azure,](howto-configure-server-parameters-using-portal.md) [CLI,](howto-configure-server-parameters-using-cli.md)API REST o ARM. 

## <a name="timescaledb"></a>Periodo cronologicoDB
TimescaleDB è un database di serie temporali che viene incluso come estensione per PostgreSQL. TimescaleDB fornisce funzioni analitiche, ottimizzazioni e scale Postgres orientate al tempo per carichi di lavoro di serie temporali.

[Ulteriori informazioni su TimescaleDB](https://docs.timescale.com/latest), un marchio registrato di [Timescale, Inc.](https://www.timescale.com/) Azure Database for PostgreSQL provides the open-source version of Timescale. Per informazioni sulle funzionalità di Timescale disponibili in questa versione, vedere il confronto tra i prodotti [della scala cronologica](https://www.timescale.com/products/).

### <a name="installing-timescaledb"></a>Installazione di TimescaleDB
Per installare TimescaleDB, è necessario includerlo nelle librerie di precaricamento condivise del server. Una modifica al parametro `shared_preload_libraries` di Postgres richiede un **riavvio** del server per rendere effettive. È possibile modificare i parametri usando il [portale di Azure](howto-configure-server-parameters-using-portal.md) o l'interfaccia della riga di comando di [Azure.](howto-configure-server-parameters-using-cli.md)

Uso del portale di [Azure:](https://portal.azure.com/)

1. Selezionare il server di Database di Azure per PostgreSQL.

2. Nella barra laterale, seleziona **Parametri server**.

3. Cercare il `shared_preload_libraries` parametro.

4. Selezionare **TimescaleDB**.

5. Selezionare **Salva** per mantenere le modifiche. Una volta salvata la modifica, viene visualizzata una notifica. 

6. Dopo la notifica, **riavviare** il server per applicare queste modifiche. Per informazioni su come riavviare un server, vedere [Riavviare un server di Database di Azure per PostgreSQL](howto-restart-server-portal.md).


È ora possibile abilitare TimescaleDB nel database Postgres. Connettersi al database ed eseguire il comando seguente:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Se viene visualizzato un errore, verificare di aver [riavviato il server](howto-restart-server-portal.md) dopo il salvataggio di shared_preload_libraries. 

È ora possibile creare un'ipertabella TimescaleDB [da zero](https://docs.timescale.com/getting-started/creating-hypertables) o eseguire la migrazione dei [dati esistenti di serie temporali in PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Ripristino di un database della scala cronologica
Per ripristinare un database della scalabilità temporale utilizzando pg_dump e pg_restore, `timescaledb_pre_restore()` `timescaledb_post restore()`è necessario eseguire due procedure di supporto nel database di destinazione: e .

Preparare innanzitutto il database di destinazione:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

A questo punto è possibile eseguire pg_dump nel database originale e quindi eseguire pg_restore. Dopo il ripristino, assicurarsi di eseguire il comando seguente nel database ripristinato:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Passaggi successivi
Segnalare a Microsoft la mancanza di un'estensione che si desidera usare. Vota le richieste esistenti o crea nuove richieste di feedback nel nostro forum di [feedback](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
