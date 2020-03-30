---
title: Query Store - Azure Database for MariaDB
description: Informazioni sulla funzionalità Archivio query nel database di Azure per MariaDB per tenere traccia delle prestazioni nel tempo.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a502638744009fc34a7f0a27f8034b89d2c8fa26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527810"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Monitorare le prestazioni del database di Azure per MariaDB con l'archivio queryMonitor Azure Database for MariaDB performance with Query Store

**Si applica a:** Database di Azure per MariaDB 10.2Azure Database for MariaDB 10.2

La funzionalità Archivio query nel database di Azure per Mariadb consente di tenere traccia delle prestazioni delle query nel tempo. Query Store semplifica la risoluzione dei problemi di prestazioni consentendo di trovare rapidamente le query con il tempo di esecuzione più lungo e il più elevato utilizzo di risorse. Query Store acquisisce automaticamente una cronologia delle query e le statistiche di runtime e le conserva a scopo di verifica. I dati vengono separati per intervalli di tempo per consentire l'individuazione dei modelli di utilizzo dei database. I dati per tutti gli utenti, i database e le query vengono archiviati nel database dello schema mysql nel database di Azure per l'istanza MariaDB.Data for all users, databases, and queries is stored in the **mysql** schema database in the Azure Database for MariaDB instance.

## <a name="common-scenarios-for-using-query-store"></a>Scenari comuni per l'utilizzo dell'archivio queryCommon scenarios for using Query Store

L'archivio query può essere utilizzato in diversi scenari, tra cui:

- Rilevamento di query regredite
- Determinazione del numero di volte in cui una query è stata eseguita in un determinato intervallo di tempo
- Confronto del tempo di esecuzione di una query nei diversi intervalli di tempo per identificare differenze significative

## <a name="enabling-query-store"></a>Abilitazione di Query Store

Query Store è una funzionalità con consenso esplicito e non è quindi attivo per impostazione predefinita in un server. L'archivio query è abilitato o disabilitato a livello globale per tutti i database in un determinato server e non può essere attivato o disattivato per ogni database.

### <a name="enable-query-store-using-the-azure-portal"></a>Abilitare Query Store con il portale di Azure

1. Accedere al portale di Azure e selezionare il database di Azure per il server MariaDB.Sign in to the Azure portal and select your Azure Database for MariaDB server.
1. Selezionare **Parametri del server** nella sezione **Impostazioni** del menu.
1. Cercare il parametro query_store_capture_mode.
1. Impostare il valore su TUTTI e **Salva**.

Per abilitare le statistiche di attesa nell'archivio query:

1. Cercare il parametro query_store_wait_sampling_capture_mode.
1. Impostare il valore su TUTTI e **Salva**.

Consentire fino a 20 minuti per la persistenza del primo batch di dati nel database mysql.

## <a name="information-in-query-store"></a>Informazioni in Query Store

Query Store include due archivi:

- Le statistiche di runtime archiviano per rendere persistenti le informazioni statistiche di esecuzione delle query.
- Le statistiche di attesa archiviano per rendere persistenti le informazioni sulle statistiche di attesa.

Per ridurre al minimo l'utilizzo dello spazio, le statistiche di esecuzione di runtime nell'archivio delle statistiche di runtime vengono aggregate in un intervallo di tempo fisso e configurabile. Le informazioni negli archivi sono visibili eseguendo query sulle viste di Query Store.

La query seguente restituisce informazioni sulle query in Query Store:

```sql
SELECT * FROM mysql.query_store;
```

Oppure questa query per le statistiche di attesa:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Ricerca di query in relazione all'attesa

> [!NOTE]
> Le statistiche di attesa non devono essere abilitate durante le ore di picco del carico di lavoro o essere attivate a tempo indeterminato per i carichi di lavoro sensibili. <br>Per i carichi di lavoro in esecuzione con un utilizzo elevato della CPU o su server configurati con vCore inferiori, prestare attenzione quando si abilitano le statistiche di attesa. Non deve essere attivato a tempo indeterminato. 

I tipi di eventi di attesa combinano diversi eventi di attesa in bucket in base alla somiglianza. Query Store indica il tipo di evento di attesa, il nome dello specifico evento di attesa e la query in questione. La possibilità di correlare queste informazioni sulle attese alle statistiche di runtime delle query consente di comprendere in modo più approfondito ciò che contribuisce alle caratteristiche di prestazioni delle query.

Di seguito sono riportati alcuni esempi di come è possibile ottenere informazioni dettagliate sul carico di lavoro usando le statistiche di attesa in Query Store:

| **Osservazione** | **Azione** |
|---|---|
|Attese di blocco elevate | Controllare il testo delle query interessate e identificare le entità di destinazione. Cercare in Query Store altre query che modificano la stessa entità e che vengono eseguite spesso e/o hanno durata elevata. Dopo aver identificato tali query, valutare la possibilità di modificare la logica dell'applicazione per migliorare la concorrenza o usare un livello di isolamento meno restrittivo. |
|Attese di I/O del buffer elevate | Trovare le query con un numero elevato di letture fisiche in Query Store. Se corrispondono alle query con attese di I/O elevate, è consigliabile introdurre un indice nell'entità sottostante, in modo da eseguire ricerche anziché analisi. Questo ridurrebbe al minimo il sovraccarico di I/O delle query. Controllare le **raccomandazioni per le prestazioni** relative al server nel portale per verificare se sono presenti raccomandazioni sugli indici per il server che ottimizzerebbero le query. |
|Attese di memoria elevate | Trovare le query con il maggiore utilizzo di memoria in Query Store. Queste query probabilmente ritardano l'avanzamento delle query interessate. Controllare le **raccomandazioni per le prestazioni** relative al server nel portale per verificare se sono presenti raccomandazioni sugli indici che ottimizzerebbero queste query.|

## <a name="configuration-options"></a>Opzioni di configurazione

Quando è abilitato, Query Store salva i dati in intervalli di aggregazione di 15 minuti, con un massimo di 500 query distinte per intervallo.

Per la configurazione dei parametri di Query Store sono disponibili le opzioni seguenti.

| **Parametro** | **Descrizione** | **Predefinito** | **Gamma** |
|---|---|---|---|
| query_store_capture_mode | Attivare/disattivare la funzionalità di archiviazione delle query IN base al valore. Nota: se performance_schema è disattivata, l'attivazione di query_store_capture_mode attiva performance_schema e un sottoinsieme di strumenti dello schema di prestazioni necessari per questa funzionalità. | ALL | NONE, TUTTI |
| query_store_capture_interval | Intervallo di acquisizione dell'archivio query in minuti. Consente di specificare l'intervallo di aggregazione delle metriche di query | 15 | 5 - 60 |
| query_store_capture_utility_queries | Attivazione o disattivazione di ON o OFF per acquisire tutte le query di utilità in esecuzione nel sistema. | NO | SI, NO |
| query_store_retention_period_in_days | Intervallo di tempo in giorni per mantenere i dati nell'archivio query. | 7 | 1-30 |

Le opzioni seguenti si applicano specificamente alle statistiche di attesa.

| **Parametro** | **Descrizione** | **Predefinito** | **Gamma** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Consente di attivare/disattivare le statistiche di attesa. | Nessuno | NONE, TUTTI |
| query_store_wait_sampling_frequency | Modifica la frequenza di campionamento di attesa in secondi. da 5 a 300 secondi. | 30 | 5-300 |

> [!NOTE]
> Attualmente **query_store_capture_mode** sostituisce questa configurazione, ovvero sia **query_store_capture_mode** che **query_store_wait_sampling_capture_mode** devono essere abilitate a TUTTI perché le statistiche di attesa funzionino. Se **query_store_capture_mode** è disattivata, anche le statistiche di attesa vengono disattivate poiché le statistiche di attesa utilizzano il performance_schema abilitato e il query_text acquisito dall'archivio query.

Usare il portale di [Azure](howto-server-parameters.md) per ottenere o impostare un valore diverso per un parametro.

## <a name="views-and-functions"></a>Viste e funzioni

Visualizzare e gestire Query Store usando le viste e le funzioni seguenti. Chiunque nel [ruolo pubblico dei privilegi](howto-create-users.md#create-additional-admin-users) di selezione può usare queste visualizzazioni per visualizzare i dati in Query Store. Queste viste sono disponibili solo nel database **mysql.**

Le query vengono normalizzate esaminandone la struttura dopo la rimozione di valori letterali e costanti. Due query identiche tranne per i valori letterali avranno lo stesso hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Questa vista restituisce tutti i dati in Query Store. Contiene una riga per ogni specifico ID database, ID utente e ID query.

| **Nome** | **Tipo di dati** | **IS_NULLABLE** | **Descrizione** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Nome dello schema |
| `query_id`| bigint(20) | NO| ID univoco generato per la query specifica, se la stessa query viene eseguita in uno schema diverso, verrà generato un nuovo ID |
| `timestamp_id` |  timestamp| NO| Timestamp in cui viene eseguita la query. Questo si basa sulla configurazione query_store_interval|
| `query_digest_text`| longtext| NO| Il testo della query normalizzata dopo la rimozione di tutti i valori letterali|
| `query_sample_text` | longtext| NO| Primo aspetto della query effettiva con valori letterali|
| `query_digest_truncated` | bit| YES| Se il testo della query è stato troncato. Il valore sarà Sì se la query è più lunga di 1 KB|
| `execution_count` | bigint(20)| NO| Il numero di volte in cui la query è stata eseguita per questo ID timestamp / durante il periodo di intervallo configurato|
| `warning_count` | bigint(20)| NO| Numero di avvisi generati da questa query durante|
| `error_count` | bigint(20)| NO| Numero di errori generati da questa query durante l'intervallo|
| `sum_timer_wait` | double| YES| Tempo di esecuzione totale della query durante l'intervallo|
| `avg_timer_wait` | double| YES| Tempo medio di esecuzione per questa query durante l'intervallo|
| `min_timer_wait` | double| YES| Tempo di esecuzione minimo per questa query|
| `max_timer_wait` | double| YES| Tempo massimo di esecuzione|
| `sum_lock_time` | bigint(20)| NO| Quantità totale di tempo impiegato per tutti i blocchi per l'esecuzione della query durante questo intervallo di tempo|
| `sum_rows_affected` | bigint(20)| NO| numero di righe interessate|
| `sum_rows_sent` | bigint(20)| NO| Numero di righe inviate al client|
| `sum_rows_examined` | bigint(20)| NO| Numero di righe esaminate|
| `sum_select_full_join` | bigint(20)| NO| Numero di join completi|
| `sum_select_scan` | bigint(20)| NO| Numero di scansioni selezionate |
| `sum_sort_rows` | bigint(20)| NO| Numero di righe ordinate|
| `sum_no_index_used` | bigint(20)| NO| Numero di volte in cui la query non ha utilizzato indici|
| `sum_no_good_index_used` | bigint(20)| NO| Numero di volte in cui il motore di esecuzione delle query non ha utilizzato indici effettivi|
| `sum_created_tmp_tables` | bigint(20)| NO| Numero totale di tabelle temporanee create|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Numero totale di tabelle temporanee create nel disco (genera I/O)|
| `first_seen` |  timestamp| NO| La prima occorrenza (UTC) della query durante la finestra di aggregazione|
| `last_seen` |  timestamp| NO| L'ultima occorrenza (UTC) della query durante questa finestra di aggregazione|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Questa vista restituisce i dati degli eventi di attesa in Query Store. Contiene una riga per ogni specifico ID database, ID utente, ID query ed evento.

| **Nome**| **Tipo di dati** | **IS_NULLABLE** | **Descrizione** |
|---|---|---|---|
| `interval_start` |  timestamp | NO| Inizio dell'intervallo (incremento di 15 minuti)|
| `interval_end` |  timestamp | NO| Fine dell'intervallo (incremento di 15 minuti)|
| `query_id` | bigint(20) | NO| ID univoco generato nella query normalizzata (dall'archivio query)|
| `query_digest_id` |  varchar(32) | NO| Il testo della query normalizzato dopo la rimozione di tutti i valori letterali (dall'archivio query) |
| `query_digest_text` | longtext | NO| Primo aspetto della query effettiva con valori letterali (dall'archivio query) |
| `event_type` |  varchar(32) | NO| Categoria dell'evento di attesa |
| `event_name` | varchar(128) | NO| Nome dell'evento wait |
| `count_star` | bigint(20) | NO| Numero di eventi di attesa campionati durante l'intervallo per la query |
| `sum_timer_wait_ms` | double | NO| Tempo di attesa totale (in millisecondi) di questa query durante l'intervallo |

### <a name="functions"></a>Funzioni

| **Nome**| **Descrizione** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Elimina tutti i dati dell'archivio query prima del timestamp specificato |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Elimina tutti i dati dell'evento di attesa prima del timestamp specificato |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Elimina i consigli la cui scadenza è prima del timestamp specificato |

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

- Se il parametro `default_transaction_read_only` è attivato da un server MariaDB, Query Store non è in grado di acquisire dati.
- La funzionalità dell'archivio query può essere\>interrotta se vengono rilevate query Unicode lunghe (6000 byte).
- Il periodo di conservazione per le statistiche di attesa è di 24 ore.
- Le statistiche di attesa usano l'acquisizione di una frazione di eventi di esempio. La frequenza può essere `query_store_wait_sampling_frequency`modificata utilizzando il parametro .

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su Informazioni dettagliate sulle prestazioni [delle queryLearn](concepts-query-performance-insight.md) more about Query Performance Insights
