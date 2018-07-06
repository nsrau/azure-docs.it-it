---
title: Modelli di acquisto del database SQL di Azure | Microsoft Docs
description: Modello di acquisto basato su DTU per il database SQL di Azure.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 1a5424b69fc70f69359b12beac86060f4e23ff27
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083992"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Modelli di acquisto e risorse del database SQL di Azure | Microsoft Docs 

I server logici nel [database SQL di Azure](sql-database-technical-overview.md) offre due modelli di acquisto per le risorse di calcolo, archiviazione e I/O basati rispettivamente su DTU e su vCore (anteprima). 

> [!NOTE]
> Le [istanze gestite](sql-database-managed-instance.md) nel database SQL di Azure offrono solo il modello di acquisto basato sui vCore.

La tabella e il grafico seguenti mettono a confronto questi due modelli.

> [!IMPORTANT]
> Per informazioni sul modello di acquisto basato su vCore (anteprima), vedere [Modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

|**Modello di acquisto**|**Descrizione**|**Ideale per**|
|---|---|---|
|Modello basato su DTU|Questo modello è basato su una misura combinata di risorse di calcolo, archiviazione e I/O. I livelli di prestazioni per i database singoli sono espressi in unità di transazione di database (DTU), quelli per i pool elastici sono espressi in unità di transazione di database elastico (eDTU). Per altre informazioni su DTU ed eDTU, vedere [Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).|Ideale per i clienti che desiderano opzioni di risorse semplici e preconfigurate.| 
|Modello basato su vCore|Questo modello consente di ridimensionare in modo indipendente le risorse di calcolo e archiviazione. Offre inoltre la possibilità di usare il Vantaggio Azure Hybrid per SQL Server per ottenere un risparmio sui costi.|Ideale per i clienti che danno valore alla trasparenza, al controllo e alla flessibilità.|
||||  

![modello di prezzi](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>Modello di acquisto basato su vCore (anteprima)

Un vCore, o memoria centrale virtuale, rappresenta la CPU logica offerta con la possibilità di scegliere tra generazioni di hardware. Il modello di acquisto basato su vCore (anteprima) offre flessibilità, controllo, trasparenza nell'utilizzo individuale delle risorse e un metodo diretto per convertire i requisiti dei carichi di lavoro locali nel cloud. Questo modello consente di ridimensionare le risorse di calcolo, memoria e archiviazione in base ai requisiti dei carichi di lavoro. Nel modello di acquisto basato su vCore (anteprima) i clienti possono scegliere tra livelli di servizio Utilizzo generico e business critical (anteprima) sia per [database singoli](sql-database-single-database-scale.md) sia per [pool elastici](sql-database-elastic-pool.md). 

Il modello di acquisto basato su vCore (anteprima) consente di ridimensionare le risorse di calcolo e archiviazione in modo indipendente, soddisfare le esigenze di prestazioni locali e ottimizzare i costi. Se il database o il pool elastico utilizza più di 300 DTU, la conversione a vCore può consentire di ridurre i costi. È possibile eseguire questa conversione usando l'API preferita o il portale di Azure, senza tempi di inattività. La conversione non è tuttavia obbligatoria. Se il modello di acquisto basato su DTU soddisfa i requisiti aziendali e di prestazioni, è consigliabile continuare a usarlo. Se si decide di eseguire la conversione dal modello basato su DTU a quello basato su vCore, selezionare il livello di prestazioni usando la regola empirica seguente: è necessario almeno 1 vCore nel livello per utilizzo generico per ogni 100 DTU nel livello Standard; per ogni 125 DTU nel livello Premium è necessario almeno 1 vCore nel livello business critical.

Nel modello di acquisto basato su vCore (anteprima) i clienti pagano per le risorse seguenti:
- Calcolo (livello di servizio + numero di vCore + generazione di hardware)*
- Tipo e quantità di risorse di archiviazione per dati e log 
- Numero di operazioni di I/O**
- Risorse di archiviazione per i backup (RA-GRS)** 

\* Nell'anteprima pubblica iniziale, le CPU logiche Generazione 4 si basano sui processori Intel E5-2673 v3 (Haswell) a 2,4 GHz.

\*\* Durante l'anteprima, 7 giorni di backup e operazioni di I/O sono gratuite.

> [!IMPORTANT]
> I costi delle risorse di calcolo, I/O e archiviazione di dati e log vengono addebitati per database singolo o pool elastico. Il costo delle risorse di archiviazione per i backup viene addebitato per ogni database. Per informazioni dettagliate sull'addebito dei costi di Istanza gestita, vedere la sezione relativa all'[istanza gestita di database SQL di Azure](sql-database-managed-instance.md).
> **Limitazioni regionali:** Il modello di acquisto basato sui vCore (anteprima) non è disponibile nelle seguenti regioni: Europa occidentale, Francia centrale, Regno Unito meridionale, Regno Unito occidentale e Australia sud-orientale.

## <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

La DTU (Database Throughput Unit) rappresenta una misura combinata di CPU, memoria e operazioni di lettura e scrittura. Il modello di acquisto basato su DTU offre un set di bundle preconfigurati di risorse di calcolo e archiviazione per diversi livelli di prestazioni dell'applicazione. I clienti che preferiscono la semplicità di un bundle preconfigurato, pagando un importo fisso mensile, possono considerare il modello basato su DTU come più adatto alle proprie esigenze. In questo modello i clienti possono scegliere tra livelli di servizio **Basic**, **Standard** e **Premium** sia per [database singoli](sql-database-single-database-scale.md) sia per [pool elastici](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>Cosa sono le unità di transazione di database (DTU)?
Per un singolo database SQL di Azure a un livello di prestazioni specifico all'interno di un [livello di servizio](sql-database-single-database-scale.md), Microsoft garantisce un certo livello di risorse per il database (a prescindere da qualsiasi altro database nel cloud di Azure), fornendo così un livello di prestazioni prevedibile. La quantità di risorse viene calcolata come numero di unità di transazione di database o DTU ed è una misura combinata delle risorse di calcolo, archiviazione e I/O. Il rapporto tra queste risorse è stato originariamente stabilito da un [carico di lavoro di benchmark OLTP](sql-database-benchmark-overview.md) progettato per essere rappresentativo dei carichi di lavoro OLTP reali. Quando il carico di lavoro supera la quantità di una di queste risorse, la velocità effettiva viene limitata, generando timeout e prestazioni più lente. Le risorse usate dal carico di lavoro non incidono sulle risorse disponibili per gli altri database SQL nel cloud di Azure e le risorse usate dagli altri carichi di lavoro non incidono sulle risorse disponibili per il database SQL.

![rettangolo di selezione](./media/sql-database-what-is-a-dtu/bounding-box.png)

Le DTU sono particolarmente utili per comprendere la quantità relativa di risorse tra i database SQL di Azure a diversi livelli di prestazioni e livelli di servizio. Ad esempio, raddoppiare le DTU aumentando il livello di prestazioni di un database equivale a raddoppiare il set di risorse disponibili per quel database. Ad esempio, un database Premium P11 con 1750 DTU fornisce 350 volte più potenza di calcolo DTU di un database Basic con 5 DTU.  

Per ottenere maggiori dettagli sul consumo di risorse (DTU) del carico di lavoro, usare [Query Performance Insight del database SQL di Azure](sql-database-query-performance.md) per:

- Identificare le query principali a livello di CPU/durata/conteggio delle esecuzioni, che possono essere potenzialmente ottimizzate per migliorare le prestazioni. Una query con uso intensivo dell'I/O, ad esempio, può trarre vantaggio dall'uso di [tecniche di ottimizzazione in memoria](sql-database-in-memory.md) per usare in modo più efficiente la memoria disponibile per un livello di servizio e di prestazioni specifico.
- Eseguire il drill-down dei dettagli di una query, visualizzarne il testo e la cronologia di utilizzo delle risorse.
- Accedere alle raccomandazioni relative all'ottimizzazione delle prestazioni che descrivono le azioni eseguite da [Advisor per database SQL](sql-database-advisor.md).

È possibile [modificare i livelli di servizio delle DTU](sql-database-service-tiers-dtu.md) in qualsiasi momento con tempi di inattività minimi per l'applicazione, in genere meno di 4 secondi. Per molte aziende e app, la possibilità di creare database e connettere o disconnettere prestazioni su richiesta è sufficiente, specialmente se i modelli d'uso sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. Per questo scenario usare un pool elastico con un determinato numero di eDTU condivise tra più database nel pool.

![Introduzione al database SQL: DTU di database singolo in base al livello](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Cosa sono le unità di transazione di database elastico (eDTU)?
Anziché fornire un set di risorse (DTU) dedicato che potrebbe non essere sempre necessario per un database SQL sempre disponibile, è possibile inserire i database in un [pool elastico](sql-database-elastic-pool.md) in un server di database SQL che condivide un pool di risorse tra tali database. Le risorse condivise in un pool elastico sono misurate dalle unità di transazione di database elastiche o eDTU. I pool elastici offrono una soluzione semplice e conveniente per gestire gli obiettivi di prestazioni per più database con modelli di utilizzo estremamente mutevoli e imprevedibili. Un pool elastico garantisce che le risorse non possano essere utilizzate da un solo database nel pool, assicurando allo stesso tempo che per ogni database del pool sia sempre disponibile una quantità minima di risorse necessaria. 

![Introduzione al database SQL: eDTU in base al livello](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A un pool viene assegnato un numero definito di eDTU per un prezzo prestabilito. All'interno del pool elastico, i singoli database sono sufficientemente flessibili da assicurare la scalabilità automatica nell'ambito di limiti configurati. Un database con un carico di lavoro più importante utilizzerà più eDTU per soddisfare la domanda. I database con carichi più leggeri utilizzeranno meno eDTU. I database senza alcun carico di lavoro non utilizzeranno eDTU. Effettuando il provisioning delle risorse per l'intero pool e non per i singoli database, le attività di gestione si semplificano e si ha a disposizione un budget prevedibile per il pool.

È possibile aggiungere altre eDTU a un pool esistente senza causare tempi di inattività del database o effetti negativi sui database nel pool. Analogamente, se le eDTU aggiuntive non sono più necessarie, è possibile rimuoverle da un pool esistente in qualsiasi momento. È possibile aggiungere o sottrarre database al pool oppure limitare la quantità di eDTU che un database può usare in condizioni di carico elevato per riservare eDTU per altri database. Se si prevede un sottoutilizzo di risorse per un database, il database può essere rimosso dal pool e configurato come database singolo con una quantità prevedibile di risorse necessarie.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Come si determina il numero di DTU necessarie per il carico di lavoro?
Se si intende eseguire la migrazione di un carico di lavoro locale o di un carico di lavoro di macchine virtuali di SQL Server a un database SQL di Azure, è possibile usare lo [strumento di calcolo DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessarie. Per un carico di lavoro esistente del database SQL di Azure, è possibile usare [Informazioni dettagliate prestazioni query del database SQL](sql-database-query-performance.md) per comprendere il consumo di risorse di database (DTU) e ottenere dati più approfonditi per l'ottimizzazione del carico di lavoro. È anche possibile usare la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) per visualizzare il consumo di risorse per l'ultima ora. In alternativa, la vista del catalogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) visualizza il consumo di risorse per gli ultimi 14 giorni, ma a una fedeltà inferiore di medie di cinque minuti.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Come è possibile sapere se un pool elastico di risorse può essere utile?
I pool sono adatti per un numero elevato di database con modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti. Database SQL valuta automaticamente la cronologia d’utilizzo delle risorse dei database in un server di database SQL esistente e consiglia una configurazione appropriata del pool nel portale di Azure. Per altre informazioni, vedere [Quando usare un pool elastico](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Cosa succede se si raggiunge il numero massimo di DTU?
I livelli di prestazioni vengono calibrati e gestiti per offrire le risorse necessarie per eseguire il carico di lavoro del database fino ai limiti massimi consentiti per il livello di servizio/livello di prestazioni selezionato. Se il carico di lavoro raggiunge uno dei limiti di CPU/I/O dati/I/O log, si continuerà a ricevere il livello massimo di risorse consentito, ma probabilmente si sperimenteranno anche latenze di query più elevate. Con questi limiti non si verificheranno errori, ma solo un rallentamento nel carico di lavoro, a meno che il rallentamento non diventi così grave da provocare il timeout delle query. Se si raggiunge il limite massimo di richieste o di sessioni utente simultanee (thread di lavoro), verranno visualizzati errori espliciti. Vedere [Limiti delle risorse del database SQL di Azure]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached) per informazioni sui limiti delle risorse non correlati a CPU, memoria, I/O dei dati o I/O del log delle transazioni.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlazione tra i risultati e le prestazioni del database nel mondo reale
È importante comprendere che tutti i benchmark, ha una funzione esclusivamente rappresentativa e indicativa. Le frequenze di transazioni raggiunte con l'applicazione benchmark non saranno uguali a quelle che è possibile ottenere con altre applicazioni. Il benchmark include una raccolta di diversi tipi di transazioni eseguiti a fronte di uno schema contenente una gamma di tabelle e tipi di dati. Anche se il benchmark esegue le stesse operazioni di base comuni a tutti i carichi di lavoro OLTP, non rappresenta una specifica classe di database o applicazione. L'obiettivo del benchmark è fornire un'indicazione ragionevole delle prestazioni relative di un database previste in caso di riduzione o aumento dei livelli di prestazioni. Nella realtà i database hanno dimensioni e complessità diverse, gestiscono combinazioni diverse di carichi di lavoro e rispondono in modi diversi. Ad esempio, un'applicazione con un utilizzo elevato di I/O può raggiungere le soglie di I/O prima, così come un'applicazione con un utilizzo elevato di CPU può raggiungere i limiti di CPU in tempi più brevi. Non vi sono garanzie che la scalabilità di un determinato database corrisponda a quella del benchmark in caso di aumento del carico.

Il benchmark e la relativa metodologia sono descritti con maggiori dettagli più avanti.

### <a name="benchmark-summary"></a>Riepilogo del benchmark
Il benchmark ASDB misura le prestazioni di una combinazione di operazioni di database di base che si verificano con maggiore frequenza con carichi di lavoro di elaborazione di transazioni online (OLTP). Benché il benchmark sia stato progettato tenendo conto del cloud computing, lo schema del database, il popolamento di dati e le transazioni sono stati progettati in modo da rappresentare a grandi linee gli elementi di base usati con maggiore frequenza con carichi di lavoro OLTP.

### <a name="schema"></a>SCHEMA
Lo schema è progettato in modo da prevedere una varietà e una complessità sufficienti per supportare una vasta gamma di operazioni. Il benchmark viene eseguito a fronte di un database costituito da sei tabelle. Le tabelle rientrano in tre categorie, ovvero a dimensione fissa, ridimensionabili ed espandibili. Sono presenti due tabelle a dimensione fissa, tre tabelle ridimensionabili e una tabella espandibile. Le tabelle a dimensione fissa includono un numero costante di righe. Le tabelle ridimensionabili prevedono una cardinalità proporzionale alle prestazioni del database che però non cambia durante l'esecuzione del benchmark. La tabella espandibile ha le dimensioni di una tabella ridimensionabile con carico iniziale, ma successivamente la cardinalità cambia nel corso dell'esecuzione del benchmark con l'inserimento e l'eliminazione di righe.

Lo schema include una combinazione di tipi di dati, tra cui valori integer, valori numerici, caratteri e valori di data/ora. Sono incluse chiavi primarie e secondarie, ma non chiavi esterne e non esistono pertanto vincoli di integrità referenziale tra le tabelle.

Un programma di generazione di dati genera i dati per il database iniziale. I dati integer e numeric vengono generati con diverse strategie. In alcuni casi, i valori vengono distribuiti in modo casuale su un intervallo. In altri casi, un insieme di valori viene permutato in modo casuale per garantire che venga mantenuta una distribuzione specifica. I campi di testo vengono generati da un elenco ponderato di parole per produrre dati realistici.

Le dimensioni del database si basano su un "fattore di scala" (SF), che determina la cardinalità delle tabelle ridimensionabili ed espandibili. Come descritto più avanti nella sezione Utenti e velocità, la dimensione del database, il numero di utenti e le prestazioni massime vengono tutti adattati in proporzione.

### <a name="transactions"></a>Transazioni
Il carico di lavoro è costituito da nove tipi di transazioni, come illustrato nella tabella riportata di seguito. Ogni transazione è progettata per evidenziare un insieme specifico di caratteristiche di sistema nel motore di database e nell'hardware del sistema, con un contrasto elevato rispetto alle altre transazioni. Questo approccio consente di valutare l'impatto dei diversi componenti sulle prestazioni globali. La transazione "Operazioni lettura intense" ad esempio produce un numero significativo di operazioni di lettura dal disco.

| Tipo di transazione | DESCRIZIONE |
| --- | --- |
| Operazioni lettura leggere |SELECT, in memoria, sola lettura |
| Operazioni lettura medie |SELECT, principalmente in memoria, sola lettura |
| Operazioni lettura intense |SELECT, principalmente non in memoria, sola lettura |
| Operazioni aggiornamento leggere |UPDATE, in memoria, lettura/scrittura |
| Operazioni aggiornamento intense |UPDATE, principalmente non in memoria, lettura/scrittura |
| Operazioni inserimento leggere |INSERT, in memoria, lettura/scrittura |
| Operazioni inserimento intense |INSERT, principalmente non in memoria, lettura/scrittura |
| Delete |DELETE, combinazione in memoria e non in memoria, lettura/scrittura |
| Operazioni CPU intense |SELECT, in memoria, carico CPU relativamente pesante, sola lettura |

### <a name="workload-mix"></a>Combinazione di carichi di lavoro
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
| Delete |2 |
| Operazioni CPU intense |10 |

### <a name="users-and-pacing"></a>Utenti e velocità
Il carico di lavoro del benchmark si basa su uno strumento che invia transazioni attraverso un insieme di connessioni per simulare il comportamento di numerosi utenti simultanei. Benché tutte le connessioni e transazioni siano generate da un computer, per semplicità vengono indicate come "utenti". Sebbene ogni utente agisca in modo indipendente da tutti gli altri, tutti gli utenti eseguono lo stesso ciclo di passaggi illustrato di seguito:

1. Stabilire una connessione di database.
2. Ripetere le seguenti operazioni fino al segnale di uscita:
   * Selezionare una transazione in modo casuale (da una distribuzione ponderata).
   * Eseguire la transazione selezionata e misurare il tempo di risposta.
   * Attendere un ritardo velocità.
3. Chiudere la connessione di database.
4. Uscire.

Il ritardo velocità (passaggio 2c) viene selezionato in modo casuale, ma con una distribuzione che presenta una media di 1,0 secondi. Pertanto, ogni utente in media può generare al massimo una transazione al secondo.

### <a name="scaling-rules"></a>Regole di ridimensionamento
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
| Premium (P6) |800 |114 GB |

### <a name="measurement-duration"></a>Durata della misurazione
Per l'esecuzione di un benchmark valido è necessaria una durata della misurazione in condizioni stabili di almeno un'ora.

### <a name="metrics"></a>Metriche
La metrica di base del benchmark è rappresentata dalla velocità effettiva e dal tempo di risposta.

* La velocità effettiva è l'unità di misura di base delle prestazioni nel benchmark. Viene misurata in transazioni per unità di tempo, conteggiando tutti i tipi di transazioni.
* Il tempo di risposta consente di misurare la prevedibilità delle prestazioni. Il vincolo del tempo di risposta varia in base alla classe di servizio. I servizi di classe superiore prevedono requisiti di tempi di risposta più rigorosi, come illustrato di seguito.

| Classe di servizio | Misura della velocità effettiva | Requisito di tempi di risposta |
| --- | --- | --- |
| Premium |Transazioni al secondo |95° percentile a 0,5 secondi |
| Standard |Transazioni al minuto |90° percentile a 1,0 secondi |
| Basic |Transazioni all'ora |80° percentile a 2,0 secondi |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul modello di acquisto basato su vCore (anteprima), vedere [Modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- Per il modello di acquisto basato su DTU, vedere [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md).
