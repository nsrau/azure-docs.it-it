---
title: 'Gestire più database SQL con i pool di database elastici: Azure | Microsoft Docs'
description: Gestire e ridimensionare centinaia o migliaia di database SQL usando i pool elastici. Un unico prezzo per le risorse che è possibile distribuire in base alle esigenze.
services: sql-database
ms.service: sql-database
subservice: elastic-pool
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: c89fa6614afec6b67f4d6445db49efa1e2248cfb
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430052"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>I pool di database elastici consentono di gestire e ridimensionare più database SQL

I pool elastici di database SQL offrono una soluzione semplice e conveniente per la gestione e il ridimensionamento di più database con esigenze di utilizzo variabili e imprevedibili. I database in un pool elastico si trovano in un server di database SQL di Azure singolo e condividono un determinato numero di risorse a un prezzo specifico. I pool elastici nel database SQL di Azure consentono agli sviluppatori di SaaS di ottimizzare i costi per un gruppo di database all'interno di un budget definito, garantendo allo stesso tempo prestazioni elastiche per ogni database.

## <a name="what-are-sql-elastic-pools"></a>Definizione di pool elastici SQL

Gli sviluppatori di SaaS compilano applicazioni basate su livelli di dati su larga scala costituiti da più database. Un modello di applicazione comune è il provisioning di un database singolo per ogni cliente. Clienti diversi, tuttavia, hanno spesso modelli di utilizzo differenti e non prevedibili. Per questo motivo, è difficile stabilire a priori i requisiti di risorse di ogni singolo utente di database. In genere, le opzioni erano due:

- Effettuare il provisioning di risorse eccessivo in base all'utilizzo massimo e pagare più del necessario, o
- Ricorrere al provisioning insufficiente per risparmiare sui costi, a scapito delle prestazioni e della soddisfazione del cliente durante i picchi.

I pool elastici risolvono il problema assicurando ai database l'ottenimento delle risorse di prestazioni necessarie, quando richieste. Forniscono un meccanismo semplice di allocazione delle risorse che rientra in un budget prevedibile. Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

I pool elastici consentono agli sviluppatori di acquistare risorse per un pool condiviso da più database, in modo da supportare periodi di utilizzo imprevisti da parte dei singoli database. È possibile configurare le risorse per il pool in base al [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o al [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md). Il requisito di risorse per un pool è determinato dall'utilizzo aggregato dei relativi database. La quantità di risorse disponibili per il pool dipende dal budget dello sviluppatore. Lo sviluppatore aggiunge semplicemente database al pool, imposta la quantità minima e massima di risorse per i database (DTU minimi o massimi oppure vCore minimi o massimi a seconda della scelta del modello di risorse), quindi imposta le risorse del pool in base al proprio budget. Utilizzando i pool, lo sviluppatore può aumentare con facilità i servizi offerti da una piccola nuova impresa fino a un'azienda matura in continua crescita.

All'interno del pool i singoli database sono sufficientemente flessibili da assicurare una scalabilità automatica nell'ambito di parametri prefissati. Se il carico di lavoro è importante, un database può utilizzare più risorse per soddisfare la domanda. Se invece il carico di lavoro è più leggero, i database in assenza di carico non utilizzano risorse. La possibilità di effettuare il provisioning delle risorse per l'intero pool e non per i singoli database semplifica le attività di gestione. È inoltre disponibile un budget per il pool prevedibile. È possibile aggiungere altre risorse a un pool esistente senza causare tempi di inattività, ad eccezione del caso in cui sia necessario spostare i database per fornire risorse di calcolo aggiuntive per le nuove eDTU riservate. Analogamente, se le risorse aggiuntive non sono più necessarie, è possibile rimuoverle da un pool esistente in qualsiasi momento. È possibile aggiungere e rimuovere database dal pool. Se si prevede che un database sottoutilizzerà le proprie risorse, è possibile rimuoverlo.

> [!NOTE]
> Quando si spostano database all'interno o all'esterno di un pool elastico, non si verifica alcun tempo di inattività, ad eccezione di un breve periodo (nell'ordine di secondi) alla fine dell'operazione, quando vengono rilasciate le connessioni al database.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando considerare un pool elastico del database SQL

I pool sono adatti per un numero elevato di database con modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti.

Più database è possibile aggiungere a un pool, maggiori diventano i risparmi. In base al modello di uso dell'applicazione, è possibile osservare un risparmio con soli due database S3.

Le sezioni seguenti aiutano a comprendere se l'insieme specifico di database può trarre vantaggio dall'uso di un pool. Gli esempi utilizzano pool Standard, ma gli stessi principi si applicano anche ai pool Basic e Premium.

### <a name="assessing-database-utilization-patterns"></a>Valutazione dei modelli di utilizzo di database

Nella figura seguente viene illustrato un esempio di un database che trascorre il tempo di inattività, ma anche periodicamente picchi di attività. Si tratta di un modello di utilizzo adatto per un pool:

   ![un database singolo adatto per un pool](./media/sql-database-elastic-pool/one-database.png)

Per il periodo di cinque minuti illustrato, DB1 raggiunge picchi fino a 90 DTU, ma l'utilizzo medio complessivo è inferiore a cinque DTU. L'esecuzione di questo carico di lavoro in un database singolo richiede la dimensione di calcolo S3, ma in questo modo la maggior parte delle risorse rimane inutilizzata durante i periodi di minore attività.

Un pool consente la condivisione tra più database di queste DTU inutilizzate e quindi riduce le DTU richieste e i costi complessivi.

Compila l'esempio precedente, si supponga che vi sono altri database con i modelli di utilizzo simili come DB1. Nelle due figure seguenti vengono presentati in parallelo l'utilizzo di quattro database e quello di 20 database all'interno di uno stesso grafico per mostrare l'assenza di sovrapposizione degli elementi che rappresentano l'utilizzo dei database nel tempo quando viene usato il modello di acquisto basato su DTU:

   ![quattro database con un modello di utilizzo adatto per un pool](./media/sql-database-elastic-pool/four-databases.png)

   ![venti database con un modello di utilizzo adatto per un pool](./media/sql-database-elastic-pool/twenty-databases.png)

Dalla riga di colore nera nella figura precedente viene illustrato l'utilizzo di DTU di aggregazione in tutti i database di 20. Viene illustrato che l'utilizzo di DTU aggregato non mai supera le 100 DTU, ciò indica che i 20 database possono condividere 100 eDTU nel corso di tale periodo di tempo. Questo comporta una riduzione di 20 volte delle DTU e di 13 volte del prezzo rispetto all'inserimento di ogni database in dimensioni di calcolo S3 per singoli database.

In questo esempio è ideale per i motivi seguenti:

* Esistono grandi differenze tra i picchi di utilizzo e l'utilizzo medio per ogni database.
* Il picco di utilizzo per ogni database si verifica in diversi momenti nel tempo.
* Le eDTU vengono condivise tra più database.

Il prezzo di un pool è una funzione delle eDTU del pool. Mentre il prezzo unitario delle eDTU di un pool è 1,5 volte maggiore del prezzo unitario delle DTU per un database singolo, le **eDTU del pool possono essere condivise da molti database ed è necessario un minor numero totale di eDTU**. Queste distinzioni nella determinazione dei prezzi e nella condivisione di eDTU costituiscono la base del potenziale risparmio sul prezzo che il pool è in grado di fornire.

Le seguenti regole relative al numero e all'uso del database consentono di garantire che un pool offra una riduzione dei costi rispetto all'uso di dimensioni di calcolo per database singoli.

### <a name="minimum-number-of-databases"></a>Numero minimo di database

Se la quantità aggregata di risorse per i singoli database è maggiore di 1,5 volte rispetto alle risorse necessarie per il pool, un pool elastico è più conveniente.

***Esempio di modello di acquisto basato su DTU***<br>
Sono necessari almeno due database S3 o 15 database S0 perché un pool di 100 eDTU risulti più conveniente rispetto all'uso di dimensioni di calcolo per database singoli.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Numero massimo di picco contemporaneamente database

Condividendo le risorse, non tutti i database in un pool possono usare contemporaneamente le risorse fino al limite disponibile per i singoli database. Meno database raggiungono il picco contemporaneamente, minore è il valore da impostare per le risorse del pool e quindi più redditizio diventa il pool stesso. In generale, non più di 2/3 (o del 67%) dei database nel pool dovrebbe raggiungere contemporaneamente il picco di utilizzo delle risorse.

***Esempio di modello di acquisto basato su DTU***<br>
Per ridurre i costi relativi a tre database S3 in un pool di 200 eDTU, al massimo due dei tre database possono raggiungere il picco di utilizzo contemporaneamente. In caso contrario, se più di due di questi quattro database S3 raggiungono il picco contemporaneamente, il pool dovrebbe essere ridimensionato a più di 200 eDTU. Se il pool viene ridimensionato a più di 200 eDTU, più database S3 dovranno essere aggiunti al pool per mantenere i costi inferiori rispetto a quelli delle dimensioni di calcolo per singoli database.

Si noti in questo esempio non prende in considerazione l'utilizzo di altri database nel pool. Se tutti i database con alcune utilizzo in qualsiasi punto nel tempo, minore di 2/3 (o 67%) dei database possono picco contemporaneamente.

### <a name="resource-utilization-per-database"></a>Utilizzo delle risorse per ogni database
Una notevole differenza tra il picco e l'utilizzo medio di un database indica periodi prolungati di utilizzo ridotto e brevi periodi di utilizzo elevato. Questo modello di utilizzo è ideale per la condivisione delle risorse tra database. Un database deve essere considerato per un pool quando relativo picchi di utilizzo sono circa 1.5 volte maggiore relativo utilizzo medio.

***Esempio di modello di acquisto basato su DTU***<br>
Un database S3 con picchi di 100 DTU e un utilizzo medio di 67 DTU o meno è un buon candidato per la condivisione di eDTU in un pool. In alternativa, un database S1 con picchi di 20 DTU e utilizzo medio di 13 DTU o meno è un buon candidato per un pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Come scegliere le dimensioni più adatte del pool

La dimensione ottimale per un pool dipende dalle risorse di aggregazione e dalle risorse di archiviazione necessarie per tutti i database nel pool. È quindi necessario stabilire quanto segue:

* Quantità massima di risorse utilizzate da tutti i database nel pool, ovvero numero massimo di DTU o vCore in base al modello di risorse selezionato.
* Byte di archiviazione massima utilizzati da tutti i database nel pool.

Per i livelli di servizio disponibili per ogni modello di risorse, vedere il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

Nei casi in cui non è possibile utilizzare gli strumenti, le seguenti istruzioni dettagliate consentono di stimare se un pool è più conveniente rispetto ai database singoli:

1. Stimare le eDTU o i vCore necessari per il pool come segue:

   Per il modello di acquisto basato su DTU: MAX(<*numero totale di database* X *utilizzo medio di DTU per database*>,<br>
   < *numero di database in picco contemporaneamente* X *picco di utilizzo di DTU per DB* )

   Per il modello di acquisto basato su vCore: MAX(<*numero totale di database* X *utilizzo medio di vCore per database*>,<br>
   < *numero di database in picco contemporaneamente* X *picco di utilizzo di vCore per database* )

2. Stimare lo spazio di archiviazione necessario per il pool aggiungendo il numero di byte necessari per tutti i database nel pool. Determinare quindi la dimensione del pool in eDTU che fornisce la quantità di spazio di archiviazione.
3. Per il modello di acquisto basato su DTU, considerare la stima di eDTU maggiore tra il Passaggio 1 e il Passaggio 2. Per il modello di acquisto basato su vCore, considerare la stima di vCore del Passaggio 1.
4. Vedere la [pagina sui prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/) e trovare la dimensione di pool più piccola, che sia maggiore della stima del Passaggio 3.
5. Confrontare il prezzo di pool ottenuto nel passaggio 5 con il prezzo dell'uso di dimensioni di calcolo appropriate per database singoli.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Uso di altre funzionalità del database SQL con i pool elastici

### <a name="elastic-jobs-and-elastic-pools"></a>Processi e pool elastici

L'uso di un pool permette di semplificare le attività di gestione con l'esecuzione di script in **[processi elastici](sql-database-elastic-jobs-overview.md)**. Un processo elastico elimina la maggior parte delle attività ripetitive associate a un elevato numero di database. Per iniziare, vedere l'[introduzione ai processi elastici](sql-database-elastic-jobs-getting-started.md).

Per altre informazioni sugli altri strumenti di database per usare più database, vedere [Aumentare il numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opzioni di continuità aziendale per i database in un pool elastico
I database in pool supportano in genere le stesse [funzionalità di continuità aziendale](sql-database-business-continuity.md) disponibili per i singoli database.

- **Ripristino temporizzato**: il ripristino temporizzato usa i backup automatici del database per ripristinare un database in un pool a un punto specifico nel tempo. Vedere [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Ripristino geografico**: il ripristino geografico fornisce un'opzione predefinita di ripristino quando un database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md)

- **Replica geografica attiva**: per le applicazioni che hanno requisiti di ripristino più elevati di quelli supportati dal ripristino geografico, configurare la [replica geografica attiva](sql-database-geo-replication-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Creare un nuovo pool elastico del database SQL tramite il portale di Azure

Esistono due modi per creare un pool elastico nel portale di Azure.
1. È possibile creare un pool elastico cercando **pool elastico SQL** nel **Marketplace** o facendo clic su **+Aggiungi** nel pannello Pool elastici SQL. È possibile specificare un server nuovo o esistente nel flusso di lavoro dei provisioning del pool.
2. In alternativa, è possibile creare un pool elastico selezionando un server SQL esistente e facendo clic su **Crea pool** per creare un pool direttamente in tale server. L'unica differenza è che in questo caso si ignora il passaggio in cui si specifica il server durante il flusso di lavoro di provisioning del pool.

> [!NOTE]
> È possibile creare più pool in un server, ma non aggiungere database da diversi server nello stesso pool.

Il livello di servizio del pool determina le funzionalità disponibili per i database elastici nel pool e la quantità massima di risorse disponibili per ogni database. Per informazioni dettagliate, vedere i limiti delle risorse per i pool elastici nel [modello basato su DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Per i limiti delle risorse basate su vCore per i pool elastici, vedere [Limiti di risorse basate su vCore - pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).

Per configurare le risorse e i prezzi del pool, fare clic su **Configura pool**. Selezionare quindi un livello di servizio, aggiungere database al pool e configurare i limiti delle risorse per il pool e i relativi database.

Al termine della configurazione del pool, è possibile fare clic su Applica, assegnare un nome al pool e fare clic su OK per creare il pool.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorare un pool elastico e i relativi database

Dal portale di Azure è possibile monitorare l'uso di un pool elastico e dei database al suo interno. È anche possibile apportare un set di modifiche al pool elastico e inviare tutte le modifiche contemporaneamente. Le modifiche includono l'aggiunta o la rimozione di database, la modifica delle impostazioni del pool elastico o la modifica delle impostazioni del database.

Per avviare il monitoraggio del pool elastico, trovare e aprire un pool elastico nel portale. Verrà visualizzata una schermata che offre una panoramica dello stato del pool elastico. Sono inclusi:

* Grafici di monitoraggio che illustrano l'utilizzo delle risorse del pool elastico
* Avvisi recenti e suggerimenti, se disponibili, per il pool elastico

L'immagine seguente illustra un esempio di pool elastico:

![Visualizzazione del pool](./media/sql-database-elastic-pool-manage-portal/basic.png)

Per visualizzare altre informazioni relative al pool è possibile fare clic su una qualsiasi delle informazioni disponibili in questa panoramica. Facendo clic sul grafico **Utilizzo risorse**, viene aperta la visualizzazione di Monitoraggio di Azure in cui è possibile personalizzare le metriche e l'intervallo di tempo visualizzati nel grafico. Facendo clic su una qualsiasi delle notifiche disponibili, viene visualizzato un pannello che mostra tutti i dettagli dell'avviso o del suggerimento.

Per monitorare i database all'interno del pool, è possibile fare clic su **Utilizzo risorse database** nella sezione **Monitoraggio** del menu delle risorse a sinistra.

![Pagina Utilizzo risorse database](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Per personalizzare la visualizzazione del grafico

È possibile modificare il grafico e la pagina Metrica per visualizzare altre metriche, ad esempio la percentuale di CPU, la percentuale di IO dei dati e la percentuale di IO del log usata.

Nel modulo **Modifica grafico** è possibile specificare un intervallo di tempo fisso, oppure fare clic su **personalizzato** per selezionare un periodo qualsiasi di 24 ore nelle ultime due settimane, e quindi selezionare le risorse da monitorare.

### <a name="to-select-databases-to-monitor"></a>Per selezionare i database da monitorare

Per impostazione predefinita, il grafico nel pannello **Utilizzo risorse database** mostra i cinque database più utilizzati in base a DTU o CPU, a seconda del livello di servizio. È possibile visualizzare i dati relativi ad altri database nel grafico selezionando e deselezionando i database dall'elenco sottostante tramite le caselle di controllo a sinistra.

È inoltre possibile selezionare altre metriche da visualizzare in modalità affiancata in questa tabella di database per ottenere una visione più dettagliata delle prestazioni dei database.

Per altre informazioni, vedere [Usare il portale di Azure per creare avvisi per il database SQL di Azure](sql-database-insights-alerts-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Per ridimensionare i pool elastici, vedere [Ridimensionamento dei pool elastici](sql-database-elastic-pool.md) e [Ridimensionare un pool elastico - codice di esempio](scripts/sql-database-monitor-and-scale-pool-powershell.md)
* Un video è disponibile in [Esercitazione video di Microsoft Virtual Academy sulle funzionalità di elasticità del database SQL di Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Per un'esercitazione sul SaaS con i pool elastici, vedere [Introduzione all'applicazione SaaS Wingtip](sql-database-wtp-overview.md).
