---
title: Estensioni-database di Azure per PostgreSQL-server flessibile
description: Informazioni sulle estensioni Postgres disponibili nel database di Azure per PostgreSQL-server flessibile
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f38006e83be47142a6d7a6db25eefb3daccd0d92
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307586"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Estensioni di PostgreSQL nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

PostgreSQL offre la capacità di estendere le funzionalità del database usando le estensioni. Le estensioni raggruppano più oggetti SQL correlati in un singolo pacchetto che può essere caricato o rimosso dal database con un comando. Dopo essere stato caricate nel database, le estensioni si comportano come le funzionalità predefinite.

## <a name="how-to-use-postgresql-extensions"></a>Come usare le estensioni di PostgreSQL
Per poter usare le estensioni di PostgreSQL è prima necessario installarle nel database. Per installare un'estensione specifica, eseguire il comando [Crea estensione](https://www.postgresql.org/docs/current/sql-createextension.html) . Questo comando carica gli oggetti inclusi nel pacchetto nel database.

Database di Azure per PostgreSQL supporta un subset di estensioni chiave come indicato di seguito. Queste informazioni sono disponibili anche eseguendo `SHOW azure.extensions;`. Le estensioni non elencate in questo documento non sono supportate nel database di Azure per PostgreSQL-server flessibile. Non è possibile creare o caricare un'estensione personalizzata nel database di Azure per PostgreSQL.


## <a name="postgres-12-extensions"></a>Estensioni Postgres 12

Le estensioni seguenti sono disponibili nel database di Azure per PostgreSQL-server flessibili con Postgres versione 12. 

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Esempio di set di dati degli indirizzi standardizzati|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | funzioni per la verifica dell'integrità della relazione|
> |[Bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | Metodo di accesso Bloom-indice basato su file di firma|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | supporto per l'indicizzazione di tipi di DataType comuni in GIN|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | supporto per l'indicizzazione di tipi di DataType comuni in GiST|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[cubo](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | tipo di dati per i cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | modello del dizionario di ricerca del testo per numeri interi|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | modello del dizionario di ricerca testo per l'elaborazione di sinonimi estesi|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | calcola le distanze di grandi cerchio sulla superficie della terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | determinare le analogie e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[intagging](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | aggregatore integer ed enumeratore. (Obsoleto)|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | funzioni, operatori e supporto per gli indici per matrici unidimensionali di numeri interi|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | tipi di dati per gli standard di numerazione dei prodotti internazionali|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | tipo di dati per le strutture ad albero gerarchico|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | esaminare il contenuto delle pagine del database a un livello basso|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | esaminare la cache del buffer condivisa|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | esaminare la mappa dello spazio disponibile (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | dati della relazione preriscaldati|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | misurazione della somiglianza del testo e ricerca nell'indice basata su trigrammi|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | esaminare la mappa di visibilità (VM) e le informazioni di visibilità a livello di pagina|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | funzioni di crittografia|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | Mostra informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Mostra statistiche a livello di tupla|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | Lingua procedurale PL/pgSQL|
> |[PostGIS](https://www.postgis.net/)                      | 3.0.0           | Geometria PostGIS, geografia |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | Funzioni e tipi raster PostGIS| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | Geocodificatore PostGIS Tiger e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | Funzioni e tipi spaziali della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | informazioni sui certificati SSL|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  Metodo TABLESAMPLE che accetta il numero di righe come limite|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  Metodo TABLESAMPLE che accetta un limite di tempo in millisecondi|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | genera identificatori universalmente univoci (UUID)|

## <a name="postgres-11-extensions"></a>Estensioni Postgres 11

Le estensioni seguenti sono disponibili nel database di Azure per PostgreSQL-server flessibili con Postgres versione 11. 

> [!div class="mx-tableFixed"]
> | **Estensione**| **Versione dell'estensione** | **Descrizione** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Consente di analizzare un indirizzo nei suoi elementi costitutivi. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Esempio di set di dati degli indirizzi standardizzati|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | funzioni per la verifica dell'integrità della relazione|
> |[Bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | Metodo di accesso Bloom-indice basato su file di firma|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | supporto per l'indicizzazione di tipi di DataType comuni in GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | supporto per l'indicizzazione di tipi di DataType comuni in GiST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | tipo di dati per stringhe di caratteri senza distinzione tra maiuscole e minuscole|
> |[cubo](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | tipo di dati per i cubi multidimensionali|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | connettersi ad altri database PostgreSQL dall'interno di un database|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | modello del dizionario di ricerca del testo per numeri interi|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | modello del dizionario di ricerca testo per l'elaborazione di sinonimi estesi|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | calcola le distanze di grandi cerchio sulla superficie della terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | determinare le analogie e la distanza tra le stringhe|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | tipo di dati per l'archiviazione di set di coppie (chiave, valore)|
> |[intagging](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | aggregatore integer ed enumeratore. (Obsoleto)|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funzioni, operatori e supporto per gli indici per matrici unidimensionali di numeri interi|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | tipi di dati per gli standard di numerazione dei prodotti internazionali|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | tipo di dati per le strutture ad albero gerarchico|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | esaminare il contenuto delle pagine del database a un livello basso|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | esaminare la cache del buffer condivisa|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | esaminare la mappa dello spazio disponibile (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dati della relazione preriscaldati|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | misurazione della somiglianza del testo e ricerca nell'indice basata su trigrammi|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | esaminare la mappa di visibilità (VM) e le informazioni di visibilità a livello di pagina|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fornisce funzionalità di controllo|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funzioni di crittografia|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Mostra informazioni di blocco a livello di riga|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Mostra statistiche a livello di tupla|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Lingua procedurale PL/pgSQL|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometria, geografia e tipi spaziali e funzioni raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funzioni SFCGAL PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geocodificatore PostGIS Tiger e geocodificatore inverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Funzioni e tipi spaziali della topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | wrapper di dati esterni per server PostgreSQL remoti|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | informazioni sui certificati SSL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funzioni che modificano intere tabelle, inclusi i campi incrociati|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  Metodo TABLESAMPLE che accetta il numero di righe come limite|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  Metodo TABLESAMPLE che accetta un limite di tempo in millisecondi|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | dizionario di ricerca di testo che rimuove gli accenti|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | genera identificatori universalmente univoci (UUID)|


## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) e [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) consentono di connettersi da un server PostgreSQL a un altro o a un altro database nello stesso server. Il server di invio deve consentire le connessioni in uscita al server di destinazione. Il server di destinazione deve consentire le connessioni dal server di invio.

Se si prevede di usare queste due estensioni, è consigliabile distribuire i server con l' [integrazione VNet](concepts-networking.md) . Per impostazione predefinita, l'integrazione VNet consente le connessioni tra i server di VNET. È anche possibile scegliere di usare i [gruppi di sicurezza di rete VNet](../../virtual-network/manage-network-security-group.md) per personalizzare l'accesso.


## <a name="pg_prewarm"></a>pg_prewarm

L'estensione pg_prewarm carica i dati relazionali nella cache. Se si riscalda le cache, le query hanno tempi di risposta migliori alla prima esecuzione dopo un riavvio. La funzionalità di preriscaldamento automatico non è attualmente disponibile nel database di Azure per PostgreSQL-server flessibile.

## <a name="pg_stat_statements"></a>pg_stat_statements
L' [estensione pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) viene precaricata in tutti i server flessibili di database di Azure per PostgreSQL per fornire un modo per tenere traccia delle statistiche di esecuzione delle istruzioni SQL.
L'impostazione `pg_stat_statements.track`, che controlla le istruzioni conteggiate mediante l'estensione, è impostata su `top`. Ciò consente di rilevare tutte le istruzioni eseguite direttamente dai client. Gli altri due livelli di rilevamento sono `none` e `all`. Questa impostazione può essere configurata come parametro Server.

È necessario trovare un compromesso tra le informazioni sull'esecuzione di query fornite da pg_stat_statements e l'impatto della registrazione di ogni istruzione SQL sulle prestazioni del server. Se non si usa attivamente l'estensione pg_stat_statements, è consigliabile impostare `pg_stat_statements.track` su `none`. Si noti che alcuni servizi di monitoraggio di terze parti possono basarsi su pg_stat_statements per fornire informazioni dettagliate sulle prestazioni delle query. Verificare se questo è il proprio caso.


## <a name="next-steps"></a>Passaggi successivi

Segnalare a Microsoft la mancanza di un'estensione che si desidera usare. Votare per le richieste esistenti o creare nuove richieste di feedback nel [Forum dei commenti](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).