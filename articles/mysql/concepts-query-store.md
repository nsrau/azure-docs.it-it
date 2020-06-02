---
title: Query Store - Database di Azure per MySQL
description: Informazioni sulla funzionalità Query Store di Database di Azure per MySQL che consente di tenere traccia delle prestazioni nel tempo.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: b47ab44c5a5f8faad85b60032a6781475235a170
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402252"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitorare le prestazioni di Database di Azure per MySQL con Query Store

**Si applica a:** Database di Azure per MySQL 5.7, 8.0

La funzionalità Query Store di Database di Azure per MySQL offre la possibilità di tenere traccia delle prestazioni delle query nel tempo. Query Store semplifica la risoluzione dei problemi di prestazioni consentendo di trovare rapidamente le query con il tempo di esecuzione più lungo e il più elevato utilizzo di risorse. Query Store acquisisce automaticamente una cronologia delle query e le statistiche di runtime e le conserva a scopo di verifica. I dati vengono separati per intervalli di tempo per consentire l'individuazione dei modelli di utilizzo dei database. I dati relativi a utenti, database e query vengono archiviati nel database con schema **mysql** nell'istanza di Database di Azure per MySQLSQL.

## <a name="common-scenarios-for-using-query-store"></a>Scenari comuni per l'uso di Query Store

È possibile usare Query Store in numerosi scenari, inclusi i seguenti:

- Rilevamento di query regredite
- Determinazione del numero di volte in cui una query è stata eseguita in un determinato intervallo di tempo
- Confronto del tempo di esecuzione di una query nei diversi intervalli di tempo per identificare differenze significative

## <a name="enabling-query-store"></a>Abilitazione di Query Store

Query Store è una funzionalità con consenso esplicito e non è quindi attivo per impostazione predefinita in un server. Viene abilitata o disabilitata a livello globale per tutti i database in un determinato server e non può essere attivata o disattivata in base al database.

### <a name="enable-query-store-using-the-azure-portal"></a>Abilitare Query Store con il portale di Azure

1. Accedere al portale di Azure e selezionare il server di Database di Azure per MySQL.
1. Selezionare **Parametri del server** nella sezione **Impostazioni** del menu.
1. Cercare il parametro query_store_capture_mode.
1. Impostare il valore su TUTTO e selezionare **Salva**.

Per abilitare le statistiche di attesa in Query Store:

1. Cercare il parametro query_store_wait_sampling_capture_mode.
1. Impostare il valore su TUTTO e selezionare **Salva**.

Per il salvataggio permanente del primo batch di dati nel database mysql possono essere necessari fino a 20 minuti.

## <a name="information-in-query-store"></a>Informazioni in Query Store

Query Store include due archivi:

- Un archivio delle statistiche di runtime per il salvataggio permanente delle informazioni sulle statistiche di esecuzione delle query.
- Un archivio delle statistiche di attesa per il salvataggio permanente delle informazioni sulle statistiche di attesa.

Per ridurre al minimo l'utilizzo di spazio, le statistiche di esecuzione di runtime nell'archivio delle statistiche di runtime vengono aggregate per un intervallo di tempo fisso configurabile. Le informazioni negli archivi sono visibili eseguendo query sulle viste di Query Store.

La query seguente restituisce informazioni sulle query in Query Store:

```sql
SELECT * FROM mysql.query_store;
```

In alternativa, usare questa query per le statistiche di attesa:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Ricerca di query in relazione all'attesa

> [!NOTE]
> Le statistiche di attesa non devono essere abilitate durante le ore di picco del carico di lavoro o essere attivate per un periodo illimitato per carichi di lavoro sensibili. <br>Per i carichi di lavoro in esecuzione con utilizzo elevato della CPU o su server configurati con vCore inferiori, prestare attenzione quando si abilitano le statistiche di attesa. Non devono essere attivate per un periodo illimitato. 

I tipi di eventi di attesa combinano diversi eventi di attesa in bucket in base alla somiglianza. Query Store indica il tipo di evento di attesa, il nome dello specifico evento di attesa e la query in questione. La possibilità di correlare queste informazioni sulle attese alle statistiche di runtime delle query consente di comprendere in modo più approfondito ciò che contribuisce alle caratteristiche di prestazioni delle query.

Di seguito sono riportati alcuni esempi di come è possibile ottenere informazioni dettagliate sul carico di lavoro usando le statistiche di attesa in Query Store:

| **Osservazione** | **Azione** |
|---|---|
|Attese di blocco elevate | Controllare il testo delle query interessate e identificare le entità di destinazione. Cercare in Query Store altre query che modificano la stessa entità e che vengono eseguite spesso e/o hanno durata elevata. Dopo aver identificato tali query, valutare la possibilità di modificare la logica dell'applicazione per migliorare la concorrenza o usare un livello di isolamento meno restrittivo. |
|Attese di I/O del buffer elevate | Trovare le query con un numero elevato di letture fisiche in Query Store. Se corrispondono alle query con attese di I/O elevate, valutare la possibilità di introdurre un indice sull'entità sottostante per eseguire ricerche anziché analisi. Questo ridurrebbe al minimo il sovraccarico di I/O delle query. Controllare le **raccomandazioni per le prestazioni** relative al server nel portale per verificare se sono presenti raccomandazioni sugli indici per il server che ottimizzerebbero le query. |
|Attese di memoria elevate | Trovare le query con il maggiore utilizzo di memoria in Query Store. Queste query probabilmente ritardano l'avanzamento delle query interessate. Controllare le **raccomandazioni per le prestazioni** relative al server nel portale per verificare se sono presenti raccomandazioni sugli indici che ottimizzerebbero queste query.|

## <a name="configuration-options"></a>Opzioni di configurazione

Quando è abilitato, Query Store salva i dati in intervalli di aggregazione di 15 minuti, con un massimo di 500 query distinte per intervallo.

Per la configurazione dei parametri di Query Store sono disponibili le opzioni seguenti.

| **Parametro** | **Descrizione** | **Default** | **Range** |
|---|---|---|---|
| query_store_capture_mode | Attivare o disattivare la funzionalità Query Store in base al valore. Nota: Se performance_schema è disattivato, l'attivazione di query_store_capture_mode attiverà performance_schema e un subset di strumenti dello schema di prestazioni necessari per questa funzionalità. | ALL | NESSUNO, TUTTO |
| query_store_capture_interval | Intervallo di acquisizione di Query Store espresso in minuti. Consente di specificare l'intervallo in cui vengono aggregate le metriche della query. | 15 | 5 - 60 |
| query_store_capture_utility_queries | Attivazione o disattivazione per acquisire tutte le query di utilità in esecuzione nel sistema. | NO | SÌ, NO |
| query_store_retention_period_in_days | Intervallo di tempo espresso in giorni per la conservazione dei dati in Query Store. | 7 | 1-30 |

Le opzioni seguenti si applicano specificamente alle statistiche di attesa.

| **Parametro** | **Descrizione** | **Default** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Consente di attivare/disattivare le statistiche di attesa. | NONE | NESSUNO, TUTTO |
| query_store_wait_sampling_frequency | Modifica la frequenza del campionamento di attesa espressa in secondi. Da 5 a 300 secondi | 30 | 5 - 300 |

> [!NOTE]
> Attualmente **query_store_capture_mode** sostituisce questa configurazione, vale a dire che **query_store_capture_mode** e **query_store_wait_sampling_capture_mode** devono essere impostati su TUTTO affinché le statistiche di attesa funzionino. Se **query_store_capture_mode** è disattivato, lo saranno anche le statistiche di attesa, perché usano il parametro performance_schema abilitato e il parametro query_text acquisito da Query Store.

Per ottenere o impostare un valore diverso per un parametro, usare il  [portale di Azure](howto-server-parameters.md)  o l' [interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md) .

## <a name="views-and-functions"></a>Viste e funzioni

Visualizzare e gestire Query Store usando le viste e le funzioni seguenti. Queste viste possono essere usate da qualsiasi membro del [ruolo pubblico con privilegi selezionati](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) per visualizzare i dati in Query Store e sono disponibili solo nel database **mysql**.

Le query vengono normalizzate esaminandone la struttura dopo la rimozione di valori letterali e costanti. Due query identiche tranne per i valori letterali avranno lo stesso hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Questa vista restituisce tutti i dati in Query Store. Contiene una riga per ogni specifico ID database, ID utente e ID query.

| **Nome** | **Tipo di dati** | **IS_NULLABLE** | **Descrizione** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Nome dello schema |
| `query_id`| bigint(20) | NO| ID univoco generato per la query specifica. Se la stessa query viene eseguita in uno schema diverso, verrà generato un nuovo ID |
| `timestamp_id` | timestamp| NO| Timestamp in cui viene eseguita la query. Si basa sulla configurazione di query_store_interval|
| `query_digest_text`| longtext| NO| Testo della query normalizzata dopo la rimozione di tutti i valori letterali|
| `query_sample_text` | longtext| NO| Primo aspetto della query effettiva con valori letterali|
| `query_digest_truncated` | bit| YES| Indica se il testo della query è stato troncato. Il valore sarà Yes se la query è più lunga di 1 KB|
| `execution_count` | bigint(20)| NO| Numero di volte in cui la query è stata eseguita per questo ID timestamp/durante il periodo di intervallo configurato|
| `warning_count` | bigint(20)| NO| Numero di avvisi generati dalla query durante l'esecuzione interna|
| `error_count` | bigint(20)| NO| Numero di errori generati dalla query durante l'intervallo|
| `sum_timer_wait` | double| YES| Tempo di esecuzione totale della query durante l'intervallo|
| `avg_timer_wait` | double| YES| Tempo medio di esecuzione per la query durante l'intervallo|
| `min_timer_wait` | double| YES| Tempo di esecuzione minimo per la query|
| `max_timer_wait` | double| YES| Tempo di esecuzione massimo|
| `sum_lock_time` | bigint(20)| NO| Quantità totale di tempo impiegato per tutti i blocchi per l'esecuzione di questa query durante questo intervallo di tempo|
| `sum_rows_affected` | bigint(20)| NO| numero di righe interessate|
| `sum_rows_sent` | bigint(20)| NO| Numero di righe inviate al client|
| `sum_rows_examined` | bigint(20)| NO| Numero di righe esaminate|
| `sum_select_full_join` | bigint(20)| NO| Numero di join completi|
| `sum_select_scan` | bigint(20)| NO| Numero di analisi di selezione |
| `sum_sort_rows` | bigint(20)| NO| Numero di righe ordinate|
| `sum_no_index_used` | bigint(20)| NO| Numero di volte in cui la query non ha usato indici|
| `sum_no_good_index_used` | bigint(20)| NO| Numero di volte in cui il motore di esecuzione delle query non ha usato indici validi|
| `sum_created_tmp_tables` | bigint(20)| NO| Numero totale di tabelle temporanee create|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Numero totale di tabelle temporanee create nel disco (genera I/O)|
| `first_seen` | timestamp| NO| Prima occorrenza (UTC) della query durante la finestra di aggregazione|
| `last_seen` | timestamp| NO| Ultima occorrenza (UTC) della query durante la finestra di aggregazione|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Questa vista restituisce i dati degli eventi di attesa in Query Store. Contiene una riga per ogni specifico ID database, ID utente, ID query ed evento.

| **Nome**| **Tipo di dati** | **IS_NULLABLE** | **Descrizione** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Inizio intervallo (incremento di 15 minuti)|
| `interval_end` | timestamp | NO| Fine intervallo (incremento di 15 minuti)|
| `query_id` | bigint(20) | NO| ID univoco generato nella query normalizzata (da Query Store)|
| `query_digest_id` | varchar(32) | NO| Testo della query normalizzata dopo la rimozione di tutti i valori letterali (da Query Store) |
| `query_digest_text` | longtext | NO| Primo aspetto della query effettiva con valori letterali (da Query Store) |
| `event_type` | varchar(32) | NO| Categoria dell'evento di attesa |
| `event_name` | varchar(128) | NO| Nome dell'evento di attesa |
| `count_star` | bigint(20) | NO| Numero di eventi di attesa campionati durante l'intervallo per la query |
| `sum_timer_wait_ms` | double | NO| Tempo totale di attesa (espresso in millisecondi) di questa query durante l'intervallo |

### <a name="functions"></a>Funzioni

| **Nome**| **Descrizione** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Elimina tutti i dati di Query Store prima del timestamp specificato |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Elimina tutti i dati dell'evento di attesa prima del timestamp specificato |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Elimina le raccomandazioni la cui scadenza è precedente al timestamp specificato |

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

- Se per un server MySQL è attivo il parametro `default_transaction_read_only`, Query Store non può acquisire i dati.
- La funzionalità Query Store può essere interrotta se si verificano query Unicode lunghe (\> = 6000 byte).
- Il periodo di conservazione per le statistiche di attesa è di 24 ore.
- Le statistiche di attesa usano l'esempio per acquisire una frazione di eventi. La frequenza può essere modificata usando il parametro `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md)
