---
title: Query Store nel Database di Azure per MariaDB
description: Questo articolo descrive la funzionalità di Query Store nel Database di Azure per MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 883f780059e38c53dedda309dd059cc714539f80
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462084"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Esegue il monitoraggio delle prestazioni di MariaDB con Query Store nel Database di Azure

**Si applica a:**  per Database di Azure per MariaDB 10.2

> [!NOTE]
> Query Store è disponibile in anteprima.

La funzionalità di Query Store nel Database di Azure per Mariadb fornisce un modo per tenere traccia delle prestazioni delle query nel tempo. Query Store semplifica la risoluzione dei problemi di prestazioni consentendo di trovare rapidamente le query con il tempo di esecuzione più lungo e il più elevato utilizzo di risorse. Query Store acquisisce automaticamente una cronologia delle query e le statistiche di runtime e le conserva a scopo di verifica. I dati vengono separati per intervalli di tempo per consentire l'individuazione dei modelli di utilizzo dei database. Dati per tutti gli utenti, database e le query vengono archiviati nel **mysql** database con schema nel Database di Azure per l'istanza di MariaDB.

## <a name="common-scenarios-for-using-query-store"></a>Scenari comuni per l'uso di Query Store

Archivio query può essere usato in diversi scenari, inclusi i seguenti:

- Rilevare le query regredite
- Determinazione del numero di volte in cui una query è stata eseguita in un determinato intervallo di tempo
- Confronto del tempo di esecuzione di una query nei diversi intervalli di tempo per identificare differenze significative

## <a name="enabling-query-store"></a>Abilitazione di Query Store

Query Store è una funzionalità con consenso esplicito e non è quindi attivo per impostazione predefinita in un server. L'archivio query è abilitato o disabilitato a livello globale per tutti i database in un determinato server e non può essere attivata o disattivata per ogni database.

### <a name="enable-query-store-using-the-azure-portal"></a>Abilitare Query Store con il portale di Azure

1. Accedere al portale di Azure e selezionare il Database di Azure per MariaDB server.
1. Selezionare **i parametri del Server** nel **impostazioni** sezione del menu.
1. Ricerca per il parametro query_store_capture_mode.
1. Impostare il valore a tutte e **salvare**.

Per abilitare le statistiche di attesa in Store la Query:

1. Ricerca per il parametro query_store_wait_sampling_capture_mode.
1. Impostare il valore a tutte e **salvare**.

Attendere fino a 20 minuti per il primo batch di dati in modo permanente nel database mysql.

## <a name="information-in-query-store"></a>Informazioni in Query Store

Query Store include due archivi:

- Delle statistiche di runtime archiviare per rendere persistenti le informazioni sulle statistiche di esecuzione query.
- Un archivio delle statistiche di attesa per rendere persistenti le informazioni sulle statistiche di attesa.

Per ridurre l'utilizzo dello spazio, le statistiche di esecuzione runtime nell'archivio delle statistiche di runtime vengono aggregate in un intervallo di tempo fisso, può essere configurato. Le informazioni negli archivi sono visibili eseguendo query sulle viste di Query Store.

La query seguente restituisce informazioni sulle query in Query Store:

```sql
SELECT * FROM mysql.query_store;
```

Questa query o per le statistiche di attesa:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Ricerca di query in relazione all'attesa

I tipi di eventi di attesa combinano diversi eventi di attesa in bucket in base alla somiglianza. Query Store indica il tipo di evento di attesa, il nome dello specifico evento di attesa e la query in questione. La possibilità di correlare queste informazioni sulle attese alle statistiche di runtime delle query consente di comprendere in modo più approfondito ciò che contribuisce alle caratteristiche di prestazioni delle query.

Di seguito sono riportati alcuni esempi di come è possibile ottenere informazioni dettagliate sul carico di lavoro usando le statistiche di attesa in Query Store:

| **Osservazione** | **Azione** |
|---|---|
|Attese di blocco elevate | Controllare il testo delle query interessate e identificare le entità di destinazione. Cercare in Query Store altre query che modificano la stessa entità e che vengono eseguite spesso e/o hanno durata elevata. Dopo aver identificato tali query, valutare la possibilità di modificare la logica dell'applicazione per migliorare la concorrenza o usare un livello di isolamento meno restrittivo. |
|Attese di I/O del buffer elevate | Trovare le query con un numero elevato di letture fisiche in Query Store. Se corrispondono le query con attese dei / o elevate, prendere in considerazione a introdurre un indice nell'entità sottostante, eseguire ricerche anziché analisi. Questo ridurrebbe al minimo il sovraccarico di I/O delle query. Verificare i **raccomandazioni per le prestazioni** per il server nel portale per vedere se sono presenti indicazioni relative agli indici per il server che verrebbe ottimizzano le query. |
|Attese di memoria elevate | Trovare le query con il maggiore utilizzo di memoria in Query Store. Queste query probabilmente ritardano l'avanzamento delle query interessate. Verificare i **raccomandazioni per le prestazioni** per il server nel portale per vedere se sono presenti indicazioni relative agli indici cui verrebbe ottimizzare le query.|

## <a name="configuration-options"></a>Opzioni di configurazione

Quando è abilitato, Query Store salva i dati in intervalli di aggregazione di 15 minuti, con un massimo di 500 query distinte per intervallo.

Per la configurazione dei parametri di Query Store sono disponibili le opzioni seguenti.

| **Parametro** | **Descrizione** | **Default** | **Range** |
|---|---|---|---|
| query_store_capture_mode | Attivare la funzionalità di archivio query ON/OFF in base al valore. Note: Se performance_schema è impostata su OFF, attivando query_store_capture_mode attiverà performance_schema e un subset di strumenti di schema prestazioni necessari per questa funzionalità. | ALL | NONE, TUTTO |
| query_store_capture_interval | L'archivio query acquisire intervallo in minuti. Consente di specificare l'intervallo in cui vengono aggregate le metriche di query | 15 | 5 - 60 |
| query_store_capture_utility_queries | L'attivazione ON o OFF per acquisire tutte le query di utilità che è in esecuzione nel sistema. | NO | SÌ, NO |
| query_store_retention_period_in_days | Intervallo di tempo in giorni per cui conservare i dati nell'archivio query. | 7 | 1-30 |

Le opzioni seguenti si applicano specificamente alle statistiche di attesa.

| **Parametro** | **Descrizione** | **Default** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Consente di abilitare ON / OFF le statistiche di attesa. | Nessuno | NONE, TUTTO |
| query_store_wait_sampling_frequency | Frequenza di modifica di wait-campionamento in secondi. da 5 a 300 secondi. | 30 | 5-300 |

> [!NOTE]
> Attualmente **query_store_capture_mode** sostituisce questa configurazione, vale a dire entrambi **query_store_capture_mode** e **query_store_wait_sampling_capture_mode** è necessario attivare a tutti per le statistiche di attesa lavorare. Se **query_store_capture_mode** è disattivata, le statistiche di attesa sono disattivato anche poiché le statistiche di attesa utilizza performance_schema abilitata e il query_text acquisita da query store.

Usare la [portale di Azure](howto-server-parameters.md) da ottenere o impostare un valore diverso per un parametro.

## <a name="views-and-functions"></a>Viste e funzioni

Visualizzare e gestire Query Store usando le viste e le funzioni seguenti. Tutti gli utenti di [ruolo public privilegio selezionare](howto-create-users.md#create-additional-admin-users) può usare queste viste per visualizzare i dati in Query Store. Queste viste sono disponibili solo nel **mysql** database.

Le query vengono normalizzate esaminandone la struttura dopo la rimozione di valori letterali e costanti. Due query identiche tranne per i valori letterali avranno lo stesso hash.

### <a name="mysqlquerystore"></a>mysql.query_store

Questa vista restituisce tutti i dati in Query Store. Contiene una riga per ogni specifico ID database, ID utente e ID query.

| **Nome** | **Tipo di dati** | **IS_NULLABLE** | **Descrizione** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Nome dello schema |
| `query_id`| bigint(20) | NO| ID univoco generato per la query specifica, se la stessa query viene eseguita in schemi diversi, un nuovo ID verrà generato |
| `timestamp_id` | timestamp| NO| Timestamp in cui viene eseguita la query. Questo è basato sulla configurazione query_store_interval|
| `query_digest_text`| longtext| NO| Il testo della query normalizzate dopo la rimozione di tutti i valori letterali|
| `query_sample_text` | longtext| NO| Prima occorrenza della query corrente con i valori letterali|
| `query_digest_truncated` | bit| SÌ| Indica se il testo della query è stato troncato. Valore sarà Yes se la query è maggiore di 1 KB|
| `execution_count` | bigint(20)| NO| Il numero di volte in cui che è stata eseguita la query per questo ID timestamp / durante il periodo di intervallo configurato|
| `warning_count` | bigint(20)| NO| Numero di avvisi generati da questa query durante l'interno|
| `error_count` | bigint(20)| NO| Numero di errori generata da questa query nell'intervallo|
| `sum_timer_wait` | Double| SÌ| Tempo totale di esecuzione di questa query nell'intervallo|
| `avg_timer_wait` | Double| SÌ| Tempo medio di esecuzione per questa query nell'intervallo|
| `min_timer_wait` | Double| SÌ| Tempo di esecuzione minimo per questa query|
| `max_timer_wait` | Double| SÌ| Tempo di esecuzione massimo|
| `sum_lock_time` | bigint(20)| NO| Quantità totale di tempo impiegato per tutti i blocchi per l'esecuzione di query durante questo intervallo di tempo|
| `sum_rows_affected` | bigint(20)| NO| Numero di righe interessate|
| `sum_rows_sent` | bigint(20)| NO| Numero di righe inviate al client|
| `sum_rows_examined` | bigint(20)| NO| Numero di righe esaminate|
| `sum_select_full_join` | bigint(20)| NO| Numero di join completo|
| `sum_select_scan` | bigint(20)| NO| Numero di analisi selezionare |
| `sum_sort_rows` | bigint(20)| NO| Numero di righe ordinate|
| `sum_no_index_used` | bigint(20)| NO| Numero di volte quando la query non utilizza gli indici|
| `sum_no_good_index_used` | bigint(20)| NO| Numero di volte quando il motore di esecuzione di query non usavano account eventuali indici con buone caratteristiche|
| `sum_created_tmp_tables` | bigint(20)| NO| Numero totale di tabelle temporanee create|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Numero totale di tabelle temporanee create nel disco (genera i/o)|
| `first_seen` | timestamp| NO| La prima occorrenza (UTC) della query durante la finestra di aggregazione|
| `last_seen` | timestamp| NO| L'ultima occorrenza (UTC) della query durante questa finestra di aggregazione|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

Questa vista restituisce i dati degli eventi di attesa in Query Store. Contiene una riga per ogni specifico ID database, ID utente, ID query ed evento.

| **Nome**| **Tipo di dati** | **IS_NULLABLE** | **Descrizione** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Inizio dell'intervallo (incremento di 15 minuti)|
| `interval_end` | timestamp | NO| Fine dell'intervallo (incremento di 15 minuti)|
| `query_id` | bigint(20) | NO| ID univoco generato sulla query normalizzata (da query store)|
| `query_digest_id` | varchar(32) | NO| Il testo della query normalizzate dopo la rimozione di tutti i valori letterali (da query store) |
| `query_digest_text` | longtext | NO| Prima occorrenza della query corrente con i valori letterali (da query store) |
| `event_type` | varchar(32) | NO| Categoria dell'evento di attesa |
| `event_name` | varchar(128) | NO| Nome dell'evento di attesa |
| `count_star` | bigint(20) | NO| Numero di eventi di attesa campionati nell'intervallo per la query |
| `sum_timer_wait_ms` | Double | NO| Tempo di attesa totale (in millisecondi) di questa query nell'intervallo |

### <a name="functions"></a>Funzioni

| **Nome**| **Descrizione** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Elimina tutti i dati di archivio query prima di timestamp specificato |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Ripulisce tutti i dati dell'evento prima all'ora specificata di attesa |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Elimina i consigli la cui scadenza è precedente all'ora specificata |

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

- Se il parametro dispone di un server di MariaDB `default_transaction_read_only` on, Query Store non è possibile acquisire i dati.
- La funzionalità query Store può essere interrotta se si verificano query Unicode lunghe (\>= 6000 byte).
- Il periodo di conservazione per le statistiche di attesa è 24 ore.
- Le statistiche di attesa utilizzano acquisizione ti esempio una frazione degli eventi. La frequenza può essere modificata usando il parametro `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [informazioni dettagliate prestazioni Query](concepts-query-performance-insight.md)
