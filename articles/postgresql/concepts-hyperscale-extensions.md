---
title: Estensioni di PostgreSQL in Database di Azure per PostgreSQL - con Iperscalabilità (Citus) (anteprima)
description: Descrive la capacità di estendere le funzionalità del database usando le estensioni nel database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 32870f37781b4161de692af91c79fe47efb3737e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077321"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>Estensioni di PostgreSQL in Database di Azure per PostgreSQL - con Iperscalabilità (Citus) (anteprima)

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
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornisce un tipo stringa di caratteri che non distingue fra maiuscole e minuscole. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornisce un tipo di dati per i cubi multidimensionali. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornisce un tipo di dati per l'archiviazione dei set di coppie chiave/valore. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornisce tipi di dati per gli standard di numerazione dei prodotti internazionali. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenzione di oggetti grande. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornisce un tipo di dati per le strutture ad albero gerarchico. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo di dati per la rappresentazione a virgola mobile a intervalli o segmenti di linea. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Tipo per primi n JSONB. |

### <a name="functions-extensions"></a>Estensioni di funzioni

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funzioni per i campi a incremento automatico. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornisce un modo per calcolare le distanze ortodromiche sulla superficie terrestre. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Offre diverse funzioni per determinare analogie e distanza tra le stringhe. |
> | [Inserisci\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funzioni per l'utente che ha modificato una tabella di rilevamento. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Sil aggregator integer e l'enumeratore (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornisce funzioni e operatori per la manipolazione delle matrici di interi senza null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funzioni per l'ora dell'ultima modifica di rilevamento. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornisce funzioni di crittografia. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gestisce le tabelle partizionate per ora o ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornisce funzioni e operatori per determinare la somiglianza del testo alfanumerico in base alla corrispondenza trigramma. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funzioni per l'implementazione di integrità referenziale (obsoleto). |
> | session\_analytics | Funzioni per l'esecuzione di query hstore matrici. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornisce funzioni che consentono di modificare intere tabelle, compresi i campi incrociati. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Attivato le notifiche di modifica. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funzioni per l'implementazione del tempo di viaggio. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genera identificatori universalmente univoci (UUID). |

### <a name="full-text-search-extensions"></a>Estensioni di ricerca full-text

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornisce un modello di dizionario di ricerca di testo per i numeri interi. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modello di dizionario ricerca testo per l'elaborazione estesa sinonimo. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Un dizionario di ricerca di testo che rimuove gli accenti (segni diacritici) dai lessemi. |

### <a name="index-types-extensions"></a>Estensioni di tipi di indice

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Metodo di accesso Bloom - file della firma basato su indice. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornisce classi operatore GIN di esempio che implementano un comportamento simile alla struttura b-tree per determinati tipi di dati. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornisce classi operatore indice GiST che implementano la struttura b-tree. |

### <a name="language-extensions"></a>Estensioni di linguaggio

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Linguaggio procedurale caricabile PL/pgSQL. |

### <a name="hyperscale-extensions"></a>Estensioni con Iperscalabilità

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Database distribuito Citus. |
> | shard\_rebalancer | Ribilanciamento in modo sicuro i dati in un gruppo di server in caso di aggiunta nodo o la rimozione. |

### <a name="miscellaneous-extensions"></a>Estensioni varie

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funzioni amministrative per PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funzioni per la verifica dell'integrità di relazione. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper di dati esterni per l'accesso ai file flat. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Esaminare il contenuto delle pagine di database a un livello basso. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornisce un modo per esaminare ciò che avviene nella cache del buffer condiviso in tempo reale. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Utilità di pianificazione di processi per PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Esaminare la mappa di spazio disponibile (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornisce un modo per caricare i dati di relazione nella cache del buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornisce un modo per tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite da un server. Per una nota relativa a questa estensione, vedere di seguito. |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | Esaminare il mapping di visibilità (VM) e le informazioni di visibilità a livello di pagina. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornisce un modo per visualizzare le informazioni di blocco a livello di riga. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornisce un modo per visualizzare le statistiche a livello di tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper di dati esterni usato per accedere ai dati archiviati in server PostgreSQL esterni. Per una nota relativa a questa estensione, vedere di seguito.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informazioni sui certificati SSL. |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Metodo TABLESAMPLE che accetta numero di righe di un limite. |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | Metodo TABLESAMPLE che accetta tempo in millisecondi come limite. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Consente di creare indici ipotetici che non comportano un utilizzo eccessivo di CPU o disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Modulo che supporta le connessioni ad altri database PostgreSQL da una sessione di database. Per una nota relativa a questa estensione, vedere di seguito. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | L'esecuzione di query XPath e XSLT. |


### <a name="postgis-extensions"></a>Estensioni di PostGIS

> [!div class="mx-tableFixed"]
> | **Estensione** | **Descrizione** |
> |---|---|
> | [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Oggetti spaziali e geografici per PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Consente di analizzare un indirizzo nei suoi elementi costitutivi. Consente di supportare il passaggio di normalizzazione dell'indirizzo nella geocodifica. |
> | postgis\_sfcgal | Funzioni di PostGIS SFCGAL. |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder e geocoder inverso. |
> | postgis\_topologia | Funzioni e tipi spaziali di PostGIS topologia. |


## <a name="pgstatstatements"></a>pg_stat_statements
L'estensione [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) è precaricata in ogni server di Database di Azure per PostgreSQL per consentire di tenere traccia delle statistiche di esecuzione delle istruzioni SQL.
L'impostazione `pg_stat_statements.track`, che controlla le istruzioni conteggiate mediante l'estensione, è impostata su `top`. Ciò consente di rilevare tutte le istruzioni eseguite direttamente dai client. Gli altri due livelli di rilevamento sono `none` e `all`. Questa impostazione è configurabile come parametro del server tramite il [portale di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

È necessario trovare un compromesso tra le informazioni sull'esecuzione di query fornite da pg_stat_statements e l'impatto della registrazione di ogni istruzione SQL sulle prestazioni del server. Se non si usa attivamente l'estensione pg_stat_statements, è consigliabile impostare `pg_stat_statements.track` su `none`. Si noti che alcuni servizi di monitoraggio di terze parti possono basarsi su pg_stat_statements per fornire informazioni dettagliate sulle prestazioni delle query. Verificare se questo è il proprio caso.

## <a name="dblink-and-postgresfdw"></a>dblink e postgres_fdw
dblink e postgres_fdw consentono di connettersi da un server PostgreSQL a un altro oppure a un altro database nello stesso server. Il server di destinazione deve consentire le connessioni dal server di origine attraverso il firewall. Quando si usano queste estensioni per la connessione tra server Database di Azure per PostgreSQL, questa operazione può essere eseguita attivando l'impostazione "Consenti l'accesso a Servizi di Azure". Questo è necessario anche quando si vogliono usare le estensioni per il loopback allo stesso server. L'impostazione "Consenti l'accesso a Servizi di Azure" è disponibile nella pagina del portale di Azure per il server Postgres, nell'area Sicurezza connessione. Quando si attiva "Consenti l'accesso a Servizi di Azure", tutti gli indirizzi IP di Azure vengono inclusi nell'elenco elementi consentiti.

Attualmente, le connessioni in uscita dal Database di Azure per PostgreSQL non sono supportate, ad eccezione delle connessioni da altri Database di Azure per i server PostgreSQL.
