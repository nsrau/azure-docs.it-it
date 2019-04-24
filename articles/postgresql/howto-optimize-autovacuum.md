---
title: Ottimizzare i processi autovacuum in un server di Database di Azure per PostgreSQL
description: Questo articolo descrive come è possibile ottimizzare i processi autovacuum in un server di Database di Azure per PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: e1b4bf1f9fa956da7a7b0ca1521439002d1ce76b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420271"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql-server"></a>Ottimizzare i processi autovacuum in un server di Database di Azure per PostgreSQL 
Questo articolo descrive come ottimizzare efficacemente i processi autovacuum in un server di Database di Azure per PostgreSQL.

## <a name="overview-of-autovacuum"></a>Panoramica dei processi autovacuum
PostgreSQL usa il controllo della concorrenza multiversione (MVCC) per consentire una maggiore concorrenza del database. Ogni aggiornamento comporta un inserimento e un'eliminazione e ogni eliminazione comporta che una o più righe vengano contrassegnate in modo temporaneo per essere eliminate. L'operazione di contrassegno temporaneo identifica le tuple inattive da eliminare in un secondo momento. Per eseguire queste attività, PostgreSQL esegue un processo vacuum.

Un processo vacuum può essere attivato manualmente o automaticamente. Un numero maggiore di tuple inattive è presente quando nel database vengono eseguite operazioni di aggiornamento o di eliminazione onerose. Un numero minore è presente quando il database è inattivo. La necessità di eseguire processi vacuum più frequentemente è maggiore quando il carico del database è oneroso, rendendo non conveniente l'esecuzione *manuale* di processi di questo tipo.

I processi autovacuum possono essere configurati e possono sfruttare vantaggi derivanti dall'ottimizzazione. I valori predefiniti di PostgreSQL provano a garantire che il prodotto funzioni su tutti i tipi di dispositivi. I dispositivi includono Raspberry Pi. I valori di configurazione ideale dipendono da:
- Totale risorse disponibili, come SKU e dimensioni di archiviazione.
- Uso delle risorse.
- Caratteristiche del singolo oggetto.

## <a name="autovacuum-benefits"></a>Vantaggi dei processi autovacuum
Se i processi non vengono eseguiti con una certa frequenza, le tuple inattive che si accumulano possono provocare i problemi seguenti:
- Aumento della quantità di dati, ad esempio database e tabelle di dimensioni maggiori.
- Indici di dimensioni maggiori non ottimali.
- Aumento delle operazioni di I/O.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitorare l'aumento di dati con query sui processi autovacuum
La query di esempio seguente è progettata per identificare il numero di tuple attive e inattive in una tabella denominata XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Configurazioni di processi autovacuum
I parametri di configurazione che controllano i processi autovacuum si basano sulle risposte a due domande fondamentali:
- Quando è necessario iniziare?
- Quanti elementi è necessario eliminare dopo l'avvio?

Ecco alcuni parametri di configurazione di processi autovacuum che è possibile aggiornare in base alle domande precedenti, con alcune indicazioni.

Parametro|DESCRIZIONE|Valore predefinito
---|---|---
autovacuum_vacuum_threshold|Specifica il numero minimo di tuple aggiornate o eliminate necessarie per attivare un'operazione vacuum in una tabella. Il valore predefinito è 50 tuple. Impostare questo parametro solo nel file postgresql.conf o nella riga di comando del server. Per eseguire l'override dell'impostazione per le singole tabelle, modificare i parametri di archiviazione della tabella stessa.|50
autovacuum_vacuum_scale_factor|Specifica una frazione delle dimensioni della tabella da aggiungere a autovacuum_vacuum_threshold quando si decide se attivare un'operazione vacuum. Il valore predefinito è 0,2, ovvero il 20% delle dimensioni della tabella. Impostare questo parametro solo nel file postgresql.conf o nella riga di comando del server. Per eseguire l'override dell'impostazione per le singole tabelle, modificare i parametri di archiviazione della tabella stessa.|5 percento
autovacuum_vacuum_cost_limit|Specifica il valore di limite di costo usato nelle operazioni vacuum automatiche. Se viene specificato -1, l'impostazione predefinita, viene usato il valore vacuum_cost_limit normale. Se è presente più di un ruolo di lavoro, il valore viene distribuito in modo proporzionale tra ruoli di lavoro autovacuum in esecuzione. La somma dei limiti per ogni ruolo di lavoro non supera il valore di questa variabile. Impostare questo parametro solo nel file postgresql.conf o nella riga di comando del server. Per eseguire l'override dell'impostazione per le singole tabelle, modificare i parametri di archiviazione della tabella stessa.|-1
autovacuum_vacuum_cost_delay|Specifica il valore di intervallo di costo usato nelle operazioni vacuum automatiche. Se viene specificato -1, viene usato il valore vacuum_cost_delay normale. Il valore predefinito è 20 millisecondi. Impostare questo parametro solo nel file postgresql.conf o nella riga di comando del server. Per eseguire l'override dell'impostazione per le singole tabelle, modificare i parametri di archiviazione della tabella stessa.|20 ms
autovacuum_nap_time|Specifica l'intervallo minimo tra le esecuzioni di processi autovacuum su un database specifico. In ogni ciclo il daemon esamina il database e genera i comandi VACUUM e ANALYZE in base alle necessità per le tabelle nel database. L'intervallo viene misurato in secondi e il valore predefinito è 1 minuto (1 min). Impostare questo parametro solo nel file postgresql.conf o nella riga di comando del server.|15 s
autovacuum_max_workers|Specifica il numero massimo di processi autovacuum, diversi dall'utilità di avvio autovacuum, che possono essere in esecuzione in qualsiasi momento. Il valore predefinito è tre. Impostare questo parametro solo all'avvio del server.|3

Per eseguire l'override delle impostazioni per le singole tabelle, modificare i parametri di archiviazione della tabella stessa. 

## <a name="autovacuum-cost"></a>Costo dei processi autovacuum
Di seguito vengono indicati i costi di esecuzione di un'operazione vacuum:

- Sulle pagine di dati su cui si esegue il processo vacuum viene inserito un blocco.
- Quando è in esecuzione un processo vacuum, vengono usate risorse di calcolo e memoria.

Di conseguenza, non eseguire processi vacuum troppo spesso o troppo raramente. Un processo vacuum deve adattarsi al carico di lavoro. Testare tutte le modifiche dei parametri dei processi autovacuum a causa dei compromessi che possono verificarsi per ognuna.

## <a name="autovacuum-start-trigger"></a>Trigger di avvio dei processi autovacuum
Il processo autovacuum viene attivato quando il numero di tuple inattive supera il valore autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. In questo caso, reltuples è una costante.

La pulizia eseguita dal processo autovacuum deve essere adeguata al carico del database. In caso contrario, è possibile che si verifichino una mancanza di spazio di archiviazione e un rallentamento generale delle query. Ammortizzata nel tempo, la frequenza con cui un'operazione vacuum elimina le tuple inattive deve corrispondere a quella con cui tali tuple vengono create.

I database con molte operazione di aggiornamento ed eliminazione contengono più tuple inattive e necessitano di più spazio. In generale tali database beneficiano di valori bassi di autovacuum_vacuum_scale_factor e di autovacuum_vacuum_threshold. Il valore basso evita l'accumulo prolungato di tuple inattive. È possibile usare valori più alti per entrambi i parametri con database di dimensioni ridotte perché la necessità di processi vacuum è meno urgente. L'esecuzione frequente di processi vacuum comporta un costo in termini di calcolo e memoria.

Il fattore di scala predefinito pari al 20% funziona bene in tabelle con una bassa percentuale di tuple inattive. Non funziona bene nelle tabelle con una percentuale elevata di tuple inattive. Ad esempio, in una tabella di 20 GB, questo fattore di scala si traduce in 4 GB di tuple inattive. In una tabella di 1 TB, è pari a 200 GB di tuple inattiva.

Con PostgreSQL, è possibile impostare questi parametri a livello di tabella oppure a livello di istanza. Attualmente è possibile impostare questi parametri a livello di tabella solo nel Database di Azure per PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Stimare i costi dei processi autovacuum
L'esecuzione di processi autovacuum comporta un costo e sono disponibili parametri per il controllo del runtime delle operazioni autovacuum. I parametri seguenti consentono di stimare il costo di esecuzione dei un processo autovacuum:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Il processo vacuum legge pagine fisiche e verifica la presenza di tuple inattive. Per ogni pagina in shared_buffers viene considerato un costo pari a 1 (vacuum_cost_page_hit). Per tutte le altre pagine viene considerato un costo pari a 20 (vacuum_cost_page_dirty) se sono presenti tuple inattive o pari a 10 (vacuum_cost_page_miss) se non è presente alcuna tupla inattiva. L'operazione vacuum si interrompe quando il processo supera il valore di autovacuum_vacuum_cost_limit. 

Dopo che il limite è stato raggiunto, il processo viene sospeso per la durata specificata dal parametro autovacuum_vacuum_cost_delay prima di essere avviato nuovamente. Se non viene raggiunto il limite, il processo autovacuum viene avviato dopo un intervallo specificato dal parametro autovacuum_nap_time.

In breve, i parametri autovacuum_vacuum_cost_delay e autovacuum_vacuum_cost_limit controllano la quantità di operazioni di pulizia dei dati consentita per unità di tempo. Si noti che i valori predefiniti sono insufficienti per la maggior parte dei piani tariffari. I valori ottimali per questi parametri sono dipendenti dal piano tariffario e devono essere configurati di conseguenza.

Il parametro autovacuum_max_workers determina il numero massimo di processi autovacuum che possono essere eseguiti simultaneamente.

Con PostgreSQL, è possibile impostare questi parametri a livello di tabella oppure a livello di istanza. Attualmente è possibile impostare questi parametri a livello di tabella solo nel Database di Azure per PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Ottimizzare i processi autovacuum per ogni tabella
È possibile configurare tutti i parametri di configurazione precedente per ogni tabella. Ad esempio:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Il processo autovacuum è un processo sincrono per tabella. Maggiore è la percentuale di tuple inattive nella tabella, maggiore è il costo di esecuzione di un processo autovacuum. È possibile suddividere le tabelle che includono un tasso elevato di aggiornamenti ed eliminazioni in più tabelle. La suddivisione delle tabelle consente di parallelizzare i processi autovacuum e di ridurne il costo di completamento in una tabella. È anche possibile aumentare il numero di ruoli di lavoro autovacuum paralleli per assicurarsi che i ruoli di lavoro siano pianificati in modo libero.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso e sull'ottimizzazione dei processi autovacuum, vedere la documentazione di PostgreSQL seguente:

 - [Chapter 18, Server Configuration](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html) (Capitolo 18 - Configurazione server)
 - [Chapter 24, Routine Database Maintenance Tasks](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html) (Capitolo 24, Attività di gestione del database di routine)
