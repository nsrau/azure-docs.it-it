---
title: Informazioni generali sul benchmark del database SQL di Azure
description: Questo argomento illustra il benchmark ASDB (Azure SQL Database Benchmark) usato per la misurazione delle prestazioni del database SQL di Azure.
services: sql-database
documentationcenter: na
author: jan-eng
manager: jhubbard
editor: monicar
ms.assetid: e26f8a66-2c12-49d7-8297-45b4d48a5c01
ms.service: sql-database
ms.custom: resources
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/21/2016
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 2681dd3792a351fecc0c72eb7fe546113a451d24
ms.openlocfilehash: 7b9b222be1a131d2a80d37404cbdd309b61785f3
ms.contentlocale: it-it
ms.lasthandoff: 01/13/2017


---
# <a name="azure-sql-database-benchmark-overview"></a>Informazioni generali sul benchmark del database SQL di Azure
## <a name="overview"></a>Panoramica
Il database SQL di Microsoft Azure offre tre [livelli di servizio](sql-database-service-tiers.md) con diversi livelli di prestazioni. Ogni livello di prestazioni fornisce un maggiore set di risorse, o potenza, progettato per garantire un aumento della velocità effettiva.

È importante riuscire a quantificare il modo in cui la potenza crescente di ogni livello di prestazioni corrisponda a un aumento delle prestazioni del database. A tale scopo, Microsoft ha sviluppato il benchmark ASDB (Azure SQL Database Benchmark), che esegue una combinazione di operazioni di base presenti in tutti i carichi di lavoro OLTP. Viene misurata la velocità effettiva raggiunta per i database in esecuzione in ogni livello di prestazioni.

Le risorse e la potenza di ogni livello di servizio e di prestazioni sono espressi in termini di [unità di velocità effettiva database (DTU, Database Throughput Unit)](sql-database-what-is-a-dtu.md). Le DTU consentono di descrivere la capacità relativa di un livello di prestazioni in base alla misurazione combinata di CPU, memoria e frequenza di lettura e scrittura offerte da ogni livello di prestazioni. Raddoppiare la classificazione DTU di un database equivale a raddoppiarne la potenza. Il benchmark consente di valutare l'impatto prodotto sulle prestazioni del database dall'incremento di potenza offerto da ogni livello di prestazioni effettuando operazioni di database effettive e modificando al tempo stesso le dimensioni del database, il numero di utenti e la frequenza transazioni in rapporto alle risorse fornite al database.

Esprimendo la velocità effettiva del livello di servizio Basic con transazioni per ora, del livello di servizio Standard con transazioni per minuto e del livello di servizio Premium con transazioni per secondo, consente di associare più rapidamente il potenziale delle prestazioni di ogni livello di servizio ai requisiti di un'applicazione.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlazione tra i risultati e le prestazioni del database nel mondo reale
È importante comprendere che ASDB, come tutti i benchmark, ha una funzione esclusivamente rappresentativa e indicativa. Le frequenze di transazioni raggiunte con l'applicazione benchmark non saranno uguali a quelle che è possibile ottenere con altre applicazioni. Il benchmark include una raccolta di diversi tipi di transazioni eseguiti a fronte di uno schema contenente una gamma di tabelle e tipi di dati. Anche se il benchmark esegue le stesse operazioni di base comuni a tutti i carichi di lavoro OLTP, non rappresenta una specifica classe di database o applicazione. L'obiettivo del benchmark è fornire un'indicazione ragionevole delle prestazioni relative di un database previste in caso di riduzione o aumento dei livelli di prestazioni. Nella realtà i database hanno dimensioni e complessità diverse, gestiscono combinazioni diverse di carichi di lavoro e rispondono in modi diversi. Ad esempio, un'applicazione con un utilizzo elevato di I/O può raggiungere le soglie di I/O prima, così come un'applicazione con un utilizzo elevato di CPU può raggiungere i limiti di CPU in tempi più brevi. Non vi sono garanzie che la scalabilità di un determinato database corrisponda a quella del benchmark in caso di aumento del carico.

Il benchmark e la relativa metodologia sono descritti con maggiori dettagli più avanti.

## <a name="benchmark-summary"></a>Riepilogo del benchmark
Il benchmark ASDB misura le prestazioni di una combinazione di operazioni di database di base che si verificano con maggiore frequenza con carichi di lavoro di elaborazione di transazioni online (OLTP). Benché il benchmark sia stato progettato tenendo conto del cloud computing, lo schema del database, il popolamento di dati e le transazioni sono stati progettati in modo da rappresentare a grandi linee gli elementi di base usati con maggiore frequenza con carichi di lavoro OLTP.

## <a name="schema"></a>Schema
Lo schema è progettato in modo da prevedere una varietà e una complessità sufficienti per supportare una vasta gamma di operazioni. Il benchmark viene eseguito a fronte di un database costituito da sei tabelle. Le tabelle rientrano in tre categorie, ovvero a dimensione fissa, ridimensionabili ed espandibili. Sono presenti due tabelle a dimensione fissa, tre tabelle ridimensionabili e una tabella espandibile. Le tabelle a dimensione fissa includono un numero costante di righe. Le tabelle ridimensionabili prevedono una cardinalità proporzionale alle prestazioni del database che però non cambia durante l'esecuzione del benchmark. La tabella espandibile ha le dimensioni di una tabella ridimensionabile con carico iniziale, ma successivamente la cardinalità cambia nel corso dell'esecuzione del benchmark con l'inserimento e l'eliminazione di righe.

Lo schema include una combinazione di tipi di dati, tra cui valori integer, valori numerici, caratteri e valori di data/ora. Sono incluse chiavi primarie e secondarie, ma non chiavi esterne e non esistono pertanto vincoli di integrità referenziale tra le tabelle.

Un programma di generazione di dati genera i dati per il database iniziale. I dati integer e numeric vengono generati con diverse strategie. In alcuni casi, i valori vengono distribuiti in modo casuale su un intervallo. In altri casi, un insieme di valori viene permutato in modo casuale per garantire che venga mantenuta una distribuzione specifica. I campi di testo vengono generati da un elenco ponderato di parole per produrre dati realistici.

Le dimensioni del database si basano su un "fattore di scala" (SF), che determina la cardinalità delle tabelle ridimensionabili ed espandibili. Come descritto più avanti nella sezione Utenti e velocità, la dimensione del database, il numero di utenti e le prestazioni massime vengono tutti adattati in proporzione.

## <a name="transactions"></a>Transazioni
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

## <a name="workload-mix"></a>Combinazione di carichi di lavoro
Le transazioni vengono selezionate casualmente da una distribuzione ponderata con la seguente combinazione globale. La combinazione globale presenta un rapporto di lettura/scrittura di circa 2:1.

| Tipo di transazione | % di combinazione |
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

## <a name="users-and-pacing"></a>Utenti e velocità
Il carico di lavoro del benchmark si basa su uno strumento che invia transazioni attraverso un insieme di connessioni per simulare il comportamento di numerosi utenti simultanei. Benché tutte le connessioni e transazioni siano generate da un computer, per semplicità vengono indicate come "utenti". Sebbene ogni utente agisca in modo indipendente da tutti gli altri, tutti gli utenti eseguono lo stesso ciclo di passaggi illustrato di seguito:

1. Stabilire una connessione di database.
2. Ripetere le seguenti operazioni fino al segnale di uscita:
   * Selezionare una transazione in modo casuale (da una distribuzione ponderata).
   * Eseguire la transazione selezionata e misurare il tempo di risposta.
   * Attendere un ritardo velocità.
3. Chiudere la connessione di database.
4. Uscire.

Il ritardo velocità (passaggio 2c) viene selezionato in modo casuale, ma con una distribuzione che presenta una media di 1,0 secondi. Pertanto, ogni utente in media può generare al massimo una transazione al secondo.

## <a name="scaling-rules"></a>Regole di ridimensionamento
Il numero di utenti è determinato dalle dimensioni del database (in unità di fattore di conversione). Esiste un utente per ogni unità di fattore di scala. A causa del ritardo velocità, un utente in media può generare al massimo una transazione al secondo.

Ad esempio, un database con fattore di scala pari a 500 (SF=500) avrà 100 utenti e potrà raggiungere una frequenza massima di 100 TPS. Per ottenere un valore TPS più elevato, sono necessari più utenti e un database di dimensioni maggiori.

La tabella seguente illustra il numero di utenti effettivamente supportati per ogni livello di servizio e ogni livello di prestazioni.

| Livello di servizio (livello di prestazioni) | Utenti | Dimensioni database |
| --- | --- | --- |
| Basic |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2,1 GB |
| Standard (S2) |50 |7,1 GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| Premium (P6/P3) |800 |114 GB |

## <a name="measurement-duration"></a>Durata della misurazione
Per l'esecuzione di un benchmark valido è necessaria una durata della misurazione in condizioni stabili di almeno un'ora.

## <a name="metrics"></a>Metriche
La metrica di base del benchmark è rappresentata dalla velocità effettiva e dal tempo di risposta.

* La velocità effettiva è l'unità di misura di base delle prestazioni nel benchmark. Viene misurata in transazioni per unità di tempo, conteggiando tutti i tipi di transazioni.
* Il tempo di risposta consente di misurare la prevedibilità delle prestazioni. Il vincolo del tempo di risposta varia in base alla classe di servizio. I servizi di classe superiore prevedono requisiti di tempi di risposta più rigorosi, come illustrato di seguito.

| Classe di servizio | Misura della velocità effettiva | Requisito di tempi di risposta |
| --- | --- | --- |
| Premium |Transazioni al secondo |95° percentile a 0,5 secondi |
| Standard |Transazioni al minuto |90° percentile a 1,0 secondi |
| Basic |Transazioni all'ora |80° percentile a 2,0 secondi |

## <a name="conclusion"></a>Conclusione
Il benchmark ASDB misura le prestazioni relative del database SQL di Azure in esecuzione con i diversi livelli di servizio e di prestazioni disponibili. Il benchmark testa una combinazione di operazioni di database di base che si verificano con maggiore frequenza con carichi di lavoro di elaborazione di transazioni online (OLTP). Misurando le prestazioni effettive, il benchmark fornisce una valutazione più significativa dell'impatto prodotto sulla velocità effettiva dalla modifica del livello di prestazioni rispetto alla valutazione che è possibile ottenere elencando semplicemente le risorse fornite da ogni livello, ad esempio la velocità della CPU, le dimensioni della memoria e le operazioni di input/output al secondo (IOPS). In futuro il benchmark verrà esteso in modo da ampliarne l'ambito ed espandere i dati forniti.

## <a name="resources"></a>Risorse
[Introduzione al database SQL](sql-database-technical-overview.md)

[Livelli di servizio e livelli di prestazioni](sql-database-service-tiers.md)

[Indicazioni sulle prestazioni per database singoli](sql-database-performance-guidance.md)

