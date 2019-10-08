---
title: Livelli di servizio del database SQL di Azure - Modello di acquisto basato su DTU | Microsoft Docs
description: Informazioni sui livelli di servizio nel modello di acquisto basato su DTU per database singoli e in pool per offrire dimensioni di calcolo e di archiviazione.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 09/06/2019
ms.openlocfilehash: e735d8832912f5b512b7cfe015ca47efbe641da7
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72000438"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Livelli di servizio nel modello di acquisto basato su DTU

I livelli di servizio nel modello di acquisto basato su DTU si differenziano in base a diverse dimensioni di calcolo con una quantità fissa di risorse di archiviazione, un periodo di conservazione fisso per i backup e un prezzo fisso. Tutti i livelli di servizio nel modello di acquisto basato su DTU offrono flessibilità per modificare le dimensioni di calcolo con [tempi di inattività](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)minimi. Tuttavia, esiste un passaggio rispetto al periodo in cui la connettività viene persa nel database per un breve intervallo di tempo, che può essere mitigato usando la logica di ripetizione dei tentativi. La fatturazione per i database singoli e i pool elastici viene effettuata con frequenza oraria in base al livello di servizio e alle dimensioni di calcolo.

> [!IMPORTANT]
> L'istanza gestita del database SQL non supporta un modello di acquisto basato su DTU. Per altre informazioni, vedere l'articolo relativo a [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md).
> [!NOTE]
> Per informazioni sui livelli di servizio basati su vCore, vedere [Livelli di servizio basati su vCore](sql-database-service-tiers-vcore.md). Per informazioni sulle differenze tra i livelli di servizio basati su DTU e quelli basati su vCore, vedere [Modelli di acquisto del database SQL di Azure](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Confronto tra i livelli di servizio basati su DTU

La scelta di un livello di servizio dipende soprattutto dai requisiti in termini di continuità aziendale, archiviazione e prestazioni.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|Carico di lavoro di destinazione|Sviluppo e produzione|Sviluppo e produzione|Sviluppo e produzione|
|Contratto di servizio relativo al tempo di attività|99,99%|99,99%|99,99%|
|Conservazione del backup massima|7 giorni|35 giorni|35 giorni|
|CPU|Low|Basso, medio, elevato|Medio, elevato|
|Velocità effettiva di I/O (approssimativa) |1-5 IOPS per DTU| 1-5 IOPS per DTU | 25 IOPS per DTU|
|Latenza di I/O (approssimativa)|5 ms (lettura), 10 ms (scrittura)|5 ms (lettura), 10 ms (scrittura)|2 ms (lettura/scrittura)|
|Indicizzazione ColumnStore |N/D|S3 e superiore|Supportato|
|OLTP in memoria|N/D|N/D|Supportato|
|||||

> [!NOTE]
> È possibile ottenere un database SQL di Azure gratuito a livello di servizio Basic insieme a un account Azure gratuito per esplorare Azure. Per informazioni, vedere [Crea un database cloud gestito con il tuo account Azure gratuito](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>DTU database singolo e limiti di archiviazione

Le dimensioni di calcolo per i database singoli sono espresse in unità di transazione di database (DTU), quelle per i pool elastici sono espresse in unità di transazione di database elastico (eDTU). Per altre informazioni su DTU ed EDTU, vedere [Modello di acquisto basato su DTU](sql-database-purchase-models.md#dtu-based-purchasing-model).

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| Dimensioni massime archiviazione | 2 GB | 1 TB | 4 TB  |
| DTU massime | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>eDTU pool elastico, archiviazione e limiti del database in pool

| | **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| Dimensioni massime di archiviazione per ogni database  | 2 GB | 1 TB | 1 TB |
| Dimensioni massime di archiviazione per ogni pool | 156 GB | 4 TB | 4 TB |
| Numero massimo di eDTU per ogni database | 5 | 3000 | 4000 |
| Numero massimo di eDTU per ogni pool | 1600 | 3000 | 4000 |
| Numero massimo di database per pool | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Nel livello Premium è attualmente disponibile uno spazio di archiviazione superiore a 1 TB in tutte le aree tranne Cina orientale, Cina settentrionale, Germania centrale, Germania nord-orientale, Stati Uniti centro-occidentali, aree US DoD e US Government (area centrale). In queste aree la quantità massima di spazio di archiviazione nel livello Premium è limitata a 1 TB.  Per altre informazioni, vedere le [limitazioni correnti di P11 e P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>Benchmark DTU

Caratteristiche fisiche (CPU, memoria, IO) associate a ogni misura DTU vengono calibrate usando un benchmark che simula il carico di lavoro di database reali.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlazione tra i risultati e le prestazioni del database nel mondo reale

È importante comprendere che tutti i benchmark, ha una funzione esclusivamente rappresentativa e indicativa. Le frequenze di transazioni raggiunte con l'applicazione benchmark non saranno uguali a quelle che è possibile ottenere con altre applicazioni. Il benchmark include una raccolta di diversi tipi di transazioni eseguiti a fronte di uno schema contenente una gamma di tabelle e tipi di dati. Anche se il benchmark esegue le stesse operazioni di base comuni a tutti i carichi di lavoro OLTP, non rappresenta una specifica classe di database o applicazione. L'obiettivo del benchmark è fornire un'indicazione ragionevole delle prestazioni relative di un database previste in caso di riduzione o aumento delle dimensioni di calcolo. Nella realtà i database hanno dimensioni e complessità diverse, gestiscono combinazioni diverse di carichi di lavoro e rispondono in modi diversi. Ad esempio, un'applicazione con un utilizzo elevato di I/O può raggiungere le soglie di I/O prima, così come un'applicazione con un utilizzo elevato di CPU può raggiungere i limiti di CPU in tempi più brevi. Non vi sono garanzie che la scalabilità di un determinato database corrisponda a quella del benchmark in caso di aumento del carico.

Il benchmark e la relativa metodologia sono descritti con maggiori dettagli più avanti.

### <a name="benchmark-summary"></a>Riepilogo del benchmark

Il benchmark misura le prestazioni di una combinazione di operazioni di database di base che si verificano con maggiore frequenza in carichi di lavoro di elaborazione di transazioni online (OLTP). Benché il benchmark sia stato progettato tenendo conto del cloud computing, lo schema del database, il popolamento di dati e le transazioni sono stati progettati in modo da rappresentare a grandi linee gli elementi di base usati con maggiore frequenza con carichi di lavoro OLTP.

### <a name="schema"></a>Schema

Lo schema è progettato in modo da prevedere una varietà e una complessità sufficienti per supportare una vasta gamma di operazioni. Il benchmark viene eseguito a fronte di un database costituito da sei tabelle. Le tabelle rientrano in tre categorie, ovvero a dimensione fissa, ridimensionabili ed espandibili. Sono presenti due tabelle a dimensione fissa, tre tabelle ridimensionabili e una tabella espandibile. Le tabelle a dimensione fissa includono un numero costante di righe. Le tabelle ridimensionabili prevedono una cardinalità proporzionale alle prestazioni del database che però non cambia durante l'esecuzione del benchmark. La tabella espandibile ha le dimensioni di una tabella ridimensionabile con carico iniziale, ma successivamente la cardinalità cambia nel corso dell'esecuzione del benchmark con l'inserimento e l'eliminazione di righe.

Lo schema include una combinazione di tipi di dati, tra cui valori integer, valori numerici, caratteri e valori di data/ora. Sono incluse chiavi primarie e secondarie, ma non chiavi esterne e non esistono pertanto vincoli di integrità referenziale tra le tabelle.

Un programma di generazione di dati genera i dati per il database iniziale. I dati integer e numeric vengono generati con diverse strategie. In alcuni casi, i valori vengono distribuiti in modo casuale su un intervallo. In altri casi, un insieme di valori viene permutato in modo casuale per garantire che venga mantenuta una distribuzione specifica. I campi di testo vengono generati da un elenco ponderato di parole per produrre dati realistici.

Le dimensioni del database si basano su un "fattore di scala" (SF), che determina la cardinalità delle tabelle ridimensionabili ed espandibili. Come descritto più avanti nella sezione Utenti e velocità, la dimensione del database, il numero di utenti e le prestazioni massime vengono tutti adattati in proporzione.

### <a name="transactions"></a>Transazioni

Il carico di lavoro è costituito da nove tipi di transazioni, come illustrato nella tabella riportata di seguito. Ogni transazione è progettata per evidenziare un insieme specifico di caratteristiche di sistema nel motore di database e nell'hardware del sistema, con un contrasto elevato rispetto alle altre transazioni. Questo approccio consente di valutare l'impatto dei diversi componenti sulle prestazioni globali. La transazione "Operazioni lettura intense" ad esempio produce un numero significativo di operazioni di lettura dal disco.

| Tipo di transazione | Descrizione |
| --- | --- |
| Operazioni lettura leggere |SELECT, in memoria, sola lettura |
| Operazioni lettura medie |SELECT, principalmente in memoria, sola lettura |
| Operazioni lettura intense |SELECT, principalmente non in memoria, sola lettura |
| Operazioni aggiornamento leggere |UPDATE, in memoria, lettura/scrittura |
| Operazioni aggiornamento intense |UPDATE, principalmente non in memoria, lettura/scrittura |
| Operazioni inserimento leggere |INSERT, in memoria, lettura/scrittura |
| Operazioni inserimento intense |INSERT, principalmente non in memoria, lettura/scrittura |
| Eliminazione |DELETE, combinazione in memoria e non in memoria, lettura/scrittura |
| Operazioni CPU intense |SELECT, in memoria, carico CPU relativamente pesante, sola lettura |

### <a name="workload-mix"></a>Combinazione di carichi di lavoro

Le transazioni vengono selezionate casualmente da una distribuzione ponderata con la seguente combinazione globale. La combinazione globale presenta un rapporto di lettura/scrittura di circa 2:1.

| Tipo transazione | % di combinazione |
| --- | --- |
| Operazioni lettura leggere |35 |
| Operazioni lettura medie |20 |
| Operazioni lettura intense |5 |
| Operazioni aggiornamento leggere |20 |
| Operazioni aggiornamento intense |3 |
| Operazioni inserimento leggere |3 |
| Operazioni inserimento intense |2 |
| Eliminazione |2 |
| Operazioni CPU intense |10 |

### <a name="users-and-pacing"></a>Utenti e velocità

Il carico di lavoro del benchmark si basa su uno strumento che invia transazioni attraverso un insieme di connessioni per simulare il comportamento di numerosi utenti simultanei. Benché tutte le connessioni e transazioni siano generate da un computer, per semplicità vengono indicate come "utenti". Sebbene ogni utente agisca in modo indipendente da tutti gli altri, tutti gli utenti eseguono lo stesso ciclo di passaggi illustrato di seguito:

1. Stabilire una connessione di database.
2. Ripetere le seguenti operazioni fino al segnale di uscita:
   - Selezionare una transazione in modo casuale (da una distribuzione ponderata).
   - Eseguire la transazione selezionata e misurare il tempo di risposta.
   - Attendere un ritardo velocità.
3. Chiudere la connessione di database.
4. Uscire.

Il ritardo velocità (passaggio 2c) viene selezionato in modo casuale, ma con una distribuzione che presenta una media di 1,0 secondi. Pertanto, ogni utente in media può generare al massimo una transazione al secondo.

### <a name="scaling-rules"></a>Regole di ridimensionamento

Il numero di utenti è determinato dalle dimensioni del database (in unità di fattore di conversione). Esiste un utente per ogni unità di fattore di scala. A causa del ritardo velocità, un utente in media può generare al massimo una transazione al secondo.

Ad esempio, un database con fattore di scala pari a 500 (SF=500) avrà 100 utenti e potrà raggiungere una frequenza massima di 100 TPS. Per ottenere un valore TPS più elevato, sono necessari più utenti e un database di dimensioni maggiori.

### <a name="measurement-duration"></a>Durata della misurazione

Per l'esecuzione di un benchmark valido è necessaria una durata della misurazione in condizioni stabili di almeno un'ora.

### <a name="metrics"></a>metrics

La metrica di base del benchmark è rappresentata dalla velocità effettiva e dal tempo di risposta.

- La velocità effettiva è l'unità di misura di base delle prestazioni nel benchmark. Viene misurata in transazioni per unità di tempo, conteggiando tutti i tipi di transazioni.
- Il tempo di risposta consente di misurare la prevedibilità delle prestazioni. Il vincolo del tempo di risposta varia in base alla classe di servizio. I servizi di classe superiore prevedono requisiti di tempi di risposta più rigorosi, come illustrato di seguito.

| Classe di servizio | Misura della velocità effettiva | Requisito di tempi di risposta |
| --- | --- | --- |
| Premium |Transazioni al secondo |95° percentile a 0,5 secondi |
| Standard |Transazioni al minuto |90° percentile a 1,0 secondi |
| Basic |Transazioni all'ora |80° percentile a 2,0 secondi |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per database singoli, vedere [Limiti delle risorse basate su DTU del database SQL per database singoli](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per i pool elastici, vedere [Limiti delle risorse basate su DTU del database SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
