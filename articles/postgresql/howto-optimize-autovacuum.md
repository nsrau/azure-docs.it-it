---
title: Ottimizzare i processi autovacuum nel Database di Azure per il server PostgreSQL
description: Questo articolo descrive come è possibile ottimizzare i processi autovacuum nel Database di Azure per il server PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 0f8db7dd3a90e06587a7e0e05f33cb6fba5c72e1
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539790"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>Ottimizzazione di processi autovacuum nel Database di Azure per il server PostgreSQL 
Questo articolo descrive come ottimizzare efficacemente i processi autovacuum nel Database di Azure per PostgreSQL.

## <a name="overview-of-autovacuum"></a>Panoramica dei processi autovacuum
PostgreSQL usa MVCC per consentire una maggiore concorrenza del database. Ogni aggiornamento comporta un inserimento e un'eliminazione e ogni eliminazione comporta che una o più righe vengano contrassegnate in modo temporaneo per essere eliminate. L'operazione di contrassegno temporaneo comporta l'identificazione di tuple inattive da eliminare in un secondo momento. PostgreSQL consente di ottenere questi risultati eseguendo un processo vacuum.

Un processo vacuum può essere attivato manualmente o automaticamente. Un numero maggiore di tuple inattive è presente quando nel database vengono eseguite operazioni di aggiornamento o di eliminazione pesanti, mentre un numero minore è presente quando il database è inattivo.  La necessità di eseguire processi VACUUM più frequentemente è maggiore quando il database è sovraccarico, rendendo non conveniente l'esecuzione **manuale** di processi di tale tipo.

I processi autovacuum possono essere configurati e possono sfruttare vantaggi derivanti dall'ottimizzazione. I valori predefiniti di PostgreSQL provano a garantire che il prodotto funzioni su tutti i tipi di dispositivi, tra cui Raspberry Pis, e che i valori di configurazione ideale dipendano da numerosi fattori:
- Totale risorse disponibili - SKU e dimensioni di archiviazione.
- Uso delle risorse.
- Caratteristiche del singolo oggetto.

## <a name="autovacuum-benefits"></a>Vantaggi dei processi autovacuum
Se i processi non vengono eseguiti con una certa frequenza, le tuple inattive che si accumulano possono provocare i problemi seguenti:
- Aumento della quantità di dati - database e tabelle di dimensioni maggiori.
- Indici di dimensioni maggiori non ottimali.
- Aumento delle operazioni di I/O.

## <a name="monitoring-bloat-with-autovacuum-queries"></a>Aumento del monitoraggio con query sui processi autovacuum
La seguente query di esempio è progettata per identificare il numero di tuple attive e inattive in una tabella denominata "XYZ": "SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;"

## <a name="autovacuum-configurations"></a>Configurazioni di processi autovacuum
I parametri di configurazione che controllano i processi autovacuum rispondono a due domande fondamentali:
- Quando è necessario iniziare?
- Quanti elementi è necessario eliminare dopo l'avvio?

Di seguito vengono indicati alcuni parametri di configurazione di processi autovacuum che è possibile aggiornare in base le domande precedenti, con alcune indicazioni:
Parametro|DESCRIZIONE|Valore predefinito
---|---|---
autovacuum_vacuum_threshold|Specifica il numero minimo di tuple aggiornate o eliminate necessarie per attivare un processo VACUUM in una tabella. Il valore predefinito è 50 tuple. Questo parametro può essere impostato solo nel file postgresql.conf o nella riga di comando del server. Questa impostazione può essere sostituita per le singole tabelle modificando i parametri di archiviazione della tabella stessa.|50
autovacuum_vacuum_scale_factor|Specifica una frazione delle dimensioni della tabella da aggiungere a autovacuum_vacuum_threshold quando si decide di attivare un processo VACUUM. Il valore predefinito è 0,2 (20 percento delle dimensioni della tabella). Questo parametro può essere impostato solo nel file postgresql.conf o nella riga di comando del server. Questa impostazione può essere sostituita per le singole tabelle modificando i parametri di archiviazione della tabella stessa.|5 percento
autovacuum_vacuum_cost_limit|Specifica il valore di limite di costo usato nelle operazioni VACUUM automatiche. Se viene specificato -1 (ovvero l'impostazione predefinita), viene usato il valore vacuum_cost_limit regolare. Il valore viene distribuito in modo proporzionale tra ruoli di lavoro autovacuum in esecuzione, se è presente più di un ruolo di lavoro. La somma dei limiti per ogni ruolo di lavoro non supera il valore di questa variabile. Questo parametro può essere impostato solo nel file postgresql.conf o nella riga di comando del server. Questa impostazione può essere sostituita per le singole tabelle modificando i parametri di archiviazione della tabella stessa.|-1
autovacuum_vacuum_cost_delay|Specifica il valore di intervallo di costo usato nelle operazioni VACUUM automatiche. Se viene specificato -1, viene usato il valore vacuum_cost_delay regolare. Il valore predefinito è 20 millisecondi. Questo parametro può essere impostato solo nel file postgresql.conf o nella riga di comando del server. Questa impostazione può essere sostituita per le singole tabelle modificando i parametri di archiviazione della tabella stessa.|20 ms
autovacuum_nap_time|Specifica l'intervallo minimo tra le esecuzioni di processi autovacuum su un database specifico. In ogni ciclo il daemon esamina il database e genera i comandi VACUUM e ANALYZE in base alle necessità per le tabelle nel database. L'intervallo viene misurato in secondi e il valore predefinito è 1 minuto (1 min). Questo parametro può essere impostato solo nel file postgresql.conf o nella riga di comando del server.|15 s
autovacuum_max_workers|Specifica il numero massimo di processi autovacuum (diversi dall'utilità di avvio autovacuum) che possono essere in esecuzione in qualsiasi momento. Il valore predefinito è tre. Questo parametro può essere impostato solo all'avvio del server.|3
Le impostazioni precedenti possono essere sostituite per le singole tabelle modificando i parametri di archiviazione della tabella stessa.  

## <a name="autovacuum-cost"></a>Costo dei processi autovacuum
Di seguito vengono indicati i costi di esecuzione di un'operazione vacuum:
- Sulle pagine di dati su cui si esegue il processo vacuum viene inserito un blocco.
- Calcolo e memoria vengono usati quando il processo vacuum è in esecuzione.

Ciò implica che tale processo non deve essere eseguito troppo spesso o troppo raramente, ma che deve essere adattato al carico di lavoro. È consigliabile testare tutte le modifiche dei parametri dei processi autovacuum a causa dei compromessi che possono verificarsi per ognuna.

## <a name="autovacuum-start-trigger"></a>Trigger di avvio dei processi autovacuum
Il processo autovacuum viene attivato quando il numero di tuple inattive supera il valore autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples (reltuples qui è una costante).

La pulizia eseguita dal processo autovacuum deve essere adeguata al carico del database. In caso contrario, è possibile che si verifichino una mancanza di spazio di archiviazione e un rallentamento generale delle query. Ammortizzata nel tempo, la frequenza con cui il processo vacuum elimina le tuple inattive deve corrispondere a quella con cui tali tuple vengono create.

Nei database con molte operazione di aggiornamento/eliminazione sono presenti più tuple inattive e necessitano di più spazio. In generale tali database beneficiano di valori bassi di autovacuum_vacuum_scale_factor e di autovacuum_vacuum_threshold per evitare l'accumulo prolungato di tuple inattive. È possibile usare valori più alti per entrambi i parametri con database di dimensioni ridotte perché la necessità di processi vacuum è meno urgente. Tenere sempre presente che l'esecuzione frequente di processi vacuum comporta un costo in termini di calcolo e memoria.

Il fattore di scala predefinito pari al 20% funziona bene in tabelle con una bassa percentuale di tuple inattive, ma non in quelle in cui tale percentuale è elevata. In una tabella di 20 GB, ad esempio, questa situazione si traduce in 4 GB di tuple inattive, mentre in una tabella di 1 TB le tuple inattive sono pari a 200 GB.

Con PostgreSQL, è possibile impostare questi parametri a livello di tabella oppure a livello di istanza. Attualmente questi parametri possono essere impostati a livello di tabella solo nel Database di Azure per PostgreSQL.

## <a name="estimating-the-cost-of-autovacuum"></a>Stimare i costi dei processi autovacuum
L'esecuzione di processi autovacuum comporta un costo e sono disponibili parametri per il controllo del runtime delle operazioni autovacuum. I parametri seguenti consentono di stimare il costo di esecuzione dei un processo autovacuum:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Il processo vacuum legge pagine fisiche e verifica la presenza di tuple inattive. Per ogni pagina in shared_buffers viene considerato un costo pari a 1 (vacuum_cost_page_hit), mentre per tutte le altre pagine viene considerato un costo pari a 20 (vacuum_cost_page_dirty) se sono presenti tuple inattive o pari a 10 (vacuum_cost_page_miss) se non è presente alcuna tupla. L'operazione vacuum si interrompe quando il processo supera il valore di autovacuum_vacuum_cost_limit.  

Dopo che il limite è stato raggiunto, il processo viene sospeso per la durata specificata dal parametro autovacuum_vacuum_cost_delay prima di essere avviato nuovamente. Se non viene raggiunto il limite, il processo autovacuum viene avviato dopo un intervallo specificato dal parametro autovacuum_nap_time.

In breve, i parametri autovacuum_vacuum_cost_delay e autovacuum_vacuum_cost_limit controllano la quantità di operazioni di pulizia dei dati consentita per unità di tempo. Si noti che i valori predefiniti sono insufficienti per la maggior parte dei piani tariffari. I valori ottimali per questi parametri sono dipendenti dal piano tariffario e devono essere configurati di conseguenza.

Il parametro autovacuum_max_workers determina il numero massimo di processi autovacuum che possono essere eseguiti simultaneamente.

Con PostgreSQL, è possibile impostare questi parametri a livello di tabella oppure a livello di istanza. Attualmente questi parametri possono essere impostati a livello di tabella solo nel Database di Azure per PostgreSQL.

## <a name="optimizing-autovacuum-per-table"></a>Ottimizzazione dei processi autovacuum per ogni tabella
Tutti i parametri di configurazione precedenti possono essere configurati per ogni tabella, ad esempio:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Il processo autovacuum è un processo sincrono per tabella. Maggiore è la percentuale di tuple inattive, maggiore è il costo di esecuzione di un processo autovacuum.  La suddivisione di tabelle con una frequenza elevata di aggiornamenti/eliminazioni in più tabelle consente di parallelizzare i processi autovacuum e di ridurne il costo per completarli in una tabella. È anche possibile aumentare il numero di ruoli di lavoro autovacuum paralleli per assicurarsi che i ruoli di lavoro siano pianificati in modo libero.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso e sull'ottimizzazione dei processi autovacuum, vedere la documentazione di PostgreSQL seguente:
 - Documentazione di PostgreSQL - [Chapter 18, Server Configuration](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html) (Capitolo 18 - Configurazione server)
 - Documentazione di PostgreSQL - [Chapter 24, Routine Database Maintenance Tasks](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html) (Capitolo 24, Attività di gestione del database di routine)
