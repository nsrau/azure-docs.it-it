---
title: Usare estensioni di PostgreSQL nel database di Azure per PostgreSQL | Microsoft Docs
description: "Descrive la capacità di estendere le funzionalità del database usando le estensioni nel database di Azure per PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: f02588495e7107b34dac7e076cf3612de12b51d4
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Estensioni di PostgreSQL in Database di Azure per PostgreSQL
PostgreSQL offre la capacità di estendere le funzionalità del database usando le estensioni. Le estensioni consentono di creare bundle di più oggetti SQL correlati in un singolo pacchetto che può essere caricato o rimosso dal database con un singolo comando. Dopo averle caricate nel database, le estensioni possono operare come funzionalità predefinite. Per altre informazioni sulle estensioni di PostgreSQL, vedere [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html) (Creare un pacchetto di oggetti correlati formando un'estensione).

## <a name="how-to-use-postgresql-extensions"></a>Come usare le estensioni di PostgreSQL
Per poter usare le estensioni di PostgreSQL è prima necessario installarle nel database. Per installare una determinata estensione, eseguire il comando [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) dallo strumento psql per caricare gli oggetti del pacchetto nel database.

Il database di Azure per PostgreSQL attualmente supporta un subset delle estensioni chiave come indicato di seguito. Le estensioni diverse da quelle elencate non sono supportate. Non è possibile creare estensioni personalizzate con il servizio Database di Azure per PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Estensioni supportate dal database di Azure per PostgreSQL
Le tabelle seguenti includono un elenco delle estensioni standard di PostgreSQL attualmente supportate dal database di Azure per PostgreSQL. Queste informazioni sono disponibili anche eseguendo una query `pg\_available\_extensions`.

### <a name="data-types-extensions"></a>Estensioni di tipi di dati

> [!div class="mx-tableFixed"]
| **Estensione** | **Descrizione** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Fornisce un tipo di dati per le password con crittografia automatica. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornisce un tipo stringa di caratteri che non distingue fra maiuscole e minuscole. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornisce un tipo di dati per i cubi multidimensionali. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornisce un tipo di dati per l'archiviazione dei set di coppie chiave/valore. |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornisce tipi di dati per gli standard di numerazione dei prodotti internazionali. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornisce un tipo di dati per le strutture ad albero gerarchico. |

### <a name="functions-extensions"></a>Estensioni di funzioni

> [!div class="mx-tableFixed"]
| **Estensione** | **Descrizione** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornisce un modo per calcolare le distanze ortodromiche sulla superficie terrestre. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Offre diverse funzioni per determinare analogie e distanza tra le stringhe. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornisce funzioni e operatori per la manipolazione delle matrici di interi senza null. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornisce funzioni di crittografia. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gestisce le tabelle partizionate per ora o ID. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornisce funzioni e operatori per determinare la somiglianza del testo alfanumerico in base alla corrispondenza trigramma. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornisce funzioni che consentono di modificare intere tabelle, compresi i campi incrociati. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genera identificatori universalmente univoci (UUID). |

### <a name="full-text-search-extensions"></a>Estensioni di ricerca full-text

> [!div class="mx-tableFixed"]
| **Estensione** | **Descrizione** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornisce un modello di dizionario di ricerca di testo per i numeri interi. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Un dizionario di ricerca di testo che rimuove gli accenti (segni diacritici) dai lessemi. |

### <a name="index-types-extensions"></a>Estensioni di tipi di indice

> [!div class="mx-tableFixed"]
| **Estensione** | **Descrizione** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornisce classi operatore GIN di esempio che implementano un comportamento simile alla struttura b-tree per determinati tipi di dati. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornisce classi operatore indice GiST che implementano la struttura b-tree. |

### <a name="language-extensions"></a>Estensioni di linguaggio

> [!div class="mx-tableFixed"]
| **Estensione** | **Descrizione** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Linguaggio procedurale caricabile PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Estensioni varie

> [!div class="mx-tableFixed"]
| **Estensione** | **Descrizione** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornisce un modo per esaminare ciò che avviene nella cache del buffer condiviso in tempo reale. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornisce un modo per caricare i dati di relazione nella cache del buffer. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornisce un modo per tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite da un server. |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornisce un modo per visualizzare le informazioni di blocco a livello di riga. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornisce un modo per visualizzare le statistiche a livello di tupla. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper di dati esterni usato per accedere ai dati archiviati in server PostgreSQL esterni. |

### <a name="postgis-extensions"></a>Estensioni di PostGIS

> [!div class="mx-tableFixed"]
| **Estensione** | **Descrizione** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Oggetti spaziali e geografici per PostgreSQL. |
| address\_standardizer, address\_standardizer\_data\_us | Consente di analizzare un indirizzo nei suoi elementi costitutivi. Consente di supportare il passaggio di normalizzazione dell'indirizzo nella geocodifica. |
| [pgrouting](http://pgrouting.org/) | Estende il database geospaziale PostGIS/PostgreSQL per fornire funzionalità di routing geospaziale. |

## <a name="next-steps"></a>Passaggi successivi
Segnalare a Microsoft la mancanza di un'estensione che si desidera usare. Esprimere un voto per le richieste esistenti o creare nuovi commenti e richieste nel [forum dei commenti dei clienti](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
