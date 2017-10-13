---
title: "Gestire più database SQL con i pool di database elastici: Azure | Microsoft Docs"
description: "Gestire e ridimensionare centinaia o migliaia di database SQL usando i pool elastici. Un unico prezzo per le risorse che è possibile distribuire in base alle esigenze."
keywords: "più database, risorse di database, prestazioni di database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.date: 08/25/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 2af5be1c2250c3ec363b97c21bc3fa05c8ace984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>I pool di database elastici consentono di gestire e ridimensionare più database SQL

I pool elastici di database SQL offrono una soluzione semplice e conveniente per la gestione e il ridimensionamento di più database con esigenze di utilizzo variabili e imprevedibili. I database in un pool elastico si trovano in un server di database SQL di Azure singolo e condividono un determinato numero di risorse ([unità di transazione di database elastico](sql-database-what-is-a-dtu.md) (eDTU)) a un prezzo specifico. I pool elastici nel database SQL di Azure consentono agli sviluppatori di SaaS di ottimizzare i costi per un gruppo di database all'interno di un budget definito, garantendo allo stesso tempo prestazioni elastiche per ogni database. 

> [!NOTE]
> I pool elastici sono disponibili a livello generale in tutte le aree di Azure tranne India occidentale, dove sono attualmente in anteprima. I pool elastici verranno resi disponibili a livello generale in quest'area non appena possibile.
>

## <a name="what-are-sql-elastic-pools"></a>Definizione di pool elastici SQL 

Gli sviluppatori di SaaS compilano applicazioni basate su livelli di dati su larga scala costituiti da più database. Un modello di applicazione comune è il provisioning di un database singolo per ogni cliente. Clienti diversi, tuttavia, hanno spesso modelli di utilizzo differenti e non prevedibili. Per questo motivo, è difficile stabilire a priori i requisiti di risorse di ogni singolo utente di database. In genere, le opzioni erano due: 

- Effettuare il provisioning di risorse eccessivo in base all'utilizzo massimo e pagare più del necessario, o
- Ricorrere al provisioning insufficiente per risparmiare sui costi, a scapito delle prestazioni e della soddisfazione del cliente durante i picchi. 

I pool elastici risolvono il problema assicurando ai database l'ottenimento delle risorse di prestazioni necessarie, quando richieste. Forniscono un meccanismo semplice di allocazione delle risorse che rientra in un budget prevedibile. Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

I pool elastici consentono agli sviluppatori di acquistare [unità di transazione di database elastico](sql-database-what-is-a-dtu.md) (eDTU) per un pool condiviso da più database, per supportare periodi di utilizzo imprevisti da parte dei singoli database. Il requisito di eDTU di un pool è determinato dall'utilizzo aggregato dei relativi database. Il numero di eDTU disponibile per il pool dipende dal budget dello sviluppatore. Lo sviluppatore aggiunge semplicemente database al pool, imposta il limite minimo e massimo di eDTU per i database e quindi imposta il numero di eDTU del pool in base al budget. Utilizzando i pool, lo sviluppatore può aumentare con facilità i servizi offerti da una piccola nuova impresa fino a un'azienda matura in continua crescita.

All'interno del pool i singoli database sono sufficientemente flessibili da assicurare una scalabilità automatica nell'ambito di parametri prefissati. Se il carico di lavoro è importante, un database può utilizzare più eDTU per soddisfare la domanda. Se invece il carico di lavoro è più leggero, i database in assenza di carico non utilizzano gli eDTU. La possibilità di effettuare il provisioning delle risorse per l'intero pool e non per i singoli database semplifica le attività di gestione. Il budget del pool, inoltre, è facilmente prevedibile. È possibile aggiungere altre eDTU a un pool esistente senza causare tempi di inattività del database, tranne per il fatto che potrebbe essere necessario spostare i database per fornire risorse di calcolo aggiuntive per le nuove eDTU riservate. Analogamente, se gli eDTU aggiuntivi non sono più necessari, è possibile rimuoverli da un pool esistente in qualsiasi momento. È possibile aggiungere e rimuovere database dal pool. Se si prevede che un database sottoutilizzerà le proprie risorse, è possibile rimuoverlo.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando considerare un pool elastico del database SQL

I pool sono adatti per un numero elevato di database con modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti.

Più database è possibile aggiungere a un pool, maggiori diventano i risparmi. In base al modello di uso dell'applicazione, è possibile osservare un risparmio con soli due database S3. 

Le sezioni seguenti aiutano a comprendere se l'insieme specifico di database può trarre vantaggio dall'uso di un pool. Gli esempi utilizzano pool Standard, ma gli stessi principi si applicano anche ai pool Basic e Premium.

### <a name="assessing-database-utilization-patterns"></a>Valutazione dei modelli di utilizzo di database

Nella figura seguente viene illustrato un esempio di un database che trascorre il tempo di inattività, ma anche periodicamente picchi di attività. Si tratta di un modello di utilizzo adatto per un pool:

   ![un database singolo adatto per un pool](./media/sql-database-elastic-pool/one-database.png)

Per il periodo di cinque minuti illustrato, DB1 raggiunge picchi fino a 90 DTU, ma l'utilizzo medio complessivo è inferiore a cinque DTU. L'esecuzione di questo carico di lavoro in un database singolo richiede il livello di prestazioni S3, ma in questo modo la maggior parte delle risorse rimane inutilizzata durante i periodi di minore attività.

Un pool consente la condivisione tra più database di queste DTU inutilizzate e quindi riduce le DTU richieste e i costi complessivi.

Compila l'esempio precedente, si supponga che vi sono altri database con i modelli di utilizzo simili come DB1. Nelle due figure seguenti viene accostato un grafico di utilizzo di quattro database e uno di 20 database, per mostrare come non vi sia sovrapposizione nell'utilizzo nel tempo:

   ![quattro database con un modello di utilizzo adatto per un pool](./media/sql-database-elastic-pool/four-databases.png)

   ![venti database con un modello di utilizzo adatto per un pool](./media/sql-database-elastic-pool/twenty-databases.png)

Dalla riga di colore nera nella figura precedente viene illustrato l'utilizzo di DTU di aggregazione in tutti i database di 20. Viene illustrato che l'utilizzo di DTU aggregato non mai supera le 100 DTU, ciò indica che i 20 database possono condividere 100 eDTU nel corso di tale periodo di tempo. Ciò comporta una riduzione di DTU di 20x e 13x una riduzione del prezzo rispetto all'inserimento di ogni database in livelli di prestazioni S3 per singoli database.

In questo esempio è ideale per i motivi seguenti:

* Esistono grandi differenze tra i picchi di utilizzo e l'utilizzo medio per ogni database. 
* Il picco di utilizzo per ogni database si verifica in diversi momenti nel tempo.
* Le eDTU vengono condivise tra più database.

Il prezzo di un pool è una funzione delle eDTU del pool. Mentre il prezzo unitario delle eDTU di un pool è 1,5 volte maggiore del prezzo unitario delle DTU per un database singolo, le **eDTU del pool possono essere condivise da molti database ed è necessario un minor numero totale di eDTU**. Queste distinzioni nella determinazione dei prezzi e nella condivisione di eDTU costituiscono la base del potenziale risparmio sul prezzo che il pool è in grado di fornire. 

Le seguenti regole relative al numero e all'utilizzo del database consentono di garantire che un pool offra una riduzione dei costi rispetto all'utilizzo di livelli di prestazioni per database singoli.

### <a name="minimum-number-of-databases"></a>Numero minimo di database

Se la somma di DTU di livelli di prestazioni per singoli database è superiore di 1,5x rispetto ai DTU necessari per il pool, allora un pool elastico è più conveniente. Per le dimensioni disponibili, vedere [Limiti di archiviazione e di eDTU per i pool elastici e dei database elastici](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

***Esempio***<br>
Sono necessari almeno due database S3 o 15 database S0 perché un pool di 100 eDTU risulti più conveniente rispetto all'uso di livelli di prestazioni per database singoli.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Numero massimo di picco contemporaneamente database

Condividendo eDTU, non tutti i database in un pool possono utilizzare contemporaneamente eDTU fino al limite disponibile quando si utilizzano livelli di prestazioni per singoli database. Meno database raggiungono il picco contemporaneamente, minore è il valore da impostare per le eDTU del pool e quindi più redditizio diventa il pool stesso. In generale, non più di un 2/3 (o 67%) dei database nel pool deve raggiungere il picco contemporaneamente al limite delle relative eDTU.

***Esempio***<br>
Per ridurre i costi relativi a tre database S3 in un pool di 200 eDTU, al massimo due dei tre database possono raggiungere il picco di utilizzo contemporaneamente. In caso contrario, se più di due di questi quattro database S3 raggiungono il picco contemporaneamente, il pool dovrebbe essere ridimensionato a più di 200 eDTU. Se il pool viene ridimensionato a più di 200 eDTU, più database S3 dovranno essere aggiunti al pool per mantenere i costi inferiori rispetto a quelli dei livelli di prestazioni per singoli database.

Si noti in questo esempio non prende in considerazione l'utilizzo di altri database nel pool. Se tutti i database con alcune utilizzo in qualsiasi punto nel tempo, minore di 2/3 (o 67%) dei database possono picco contemporaneamente.

### <a name="dtu-utilization-per-database"></a>Utilizzo di DTU per ogni database
Una notevole differenza tra il picco e l'utilizzo medio di un database indica periodi prolungati di utilizzo ridotto e brevi periodi di utilizzo elevato. Questo modello di utilizzo è ideale per la condivisione delle risorse tra database. Un database deve essere considerato per un pool quando relativo picchi di utilizzo sono circa 1.5 volte maggiore relativo utilizzo medio.

***Esempio***<br>
Un database S3 con picchi di 100 DTU e un utilizzo medio di 67 DTU o meno è un buon candidato per la condivisione di eDTU in un pool. In alternativa, un database S1 con picchi di 20 DTU e utilizzo medio di 13 DTU o meno è un buon candidato per un pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Come scegliere le dimensioni più adatte del pool

La dimensione ottimale per un pool dipende dalle eDTU di aggregazione e dalle risorse di archiviazione necessarie per tutti i database nel pool. Questo richiede di stabilire il valore maggiore tra i seguenti:

* Dtu massima utilizzata da tutti i database nel pool.
* Byte di archiviazione massima utilizzati da tutti i database nel pool.

Per le dimensioni disponibili, vedere [Limiti di archiviazione e di eDTU per i pool elastici e dei database elastici](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Database SQL valuta automaticamente la cronologia d’utilizzo delle risorse dei database in un server di database SQL esistente e consiglia una configurazione appropriata del pool nel portale di Azure. Oltre alle raccomandazioni, una funzionalità incorporata stima l'utilizzo di eDTU per un gruppo personalizzato di database del server. Ciò consente di eseguire un'analisi di simulazione tramite l'aggiunta interattiva di database al pool e la relativa rimozione in modo da ottenere un'analisi di utilizzo delle risorse e suggerimenti di ridimensionamento prima di eseguire il commit delle modifiche. Per le procedure, vedere [Monitorare e gestire un pool di database elastici con il portale di Azure](sql-database-elastic-pool-manage-portal.md).

Nei casi in cui non è possibile utilizzare gli strumenti, le seguenti istruzioni dettagliate consentono di stimare se un pool è più conveniente rispetto ai database singoli:

1. Stimare le eDTU necessarie per il pool come segue:

   MAX (<*numero totale di database* X *utilizzo medio di DTU per DB*>,<br>
   <*numero di database in picco contemporaneamente* X *picco di utilizzo di DTU per DB*)
2. Stimare lo spazio di archiviazione necessario per il pool aggiungendo il numero di byte necessari per tutti i database nel pool. Determinare quindi la dimensione del pool in eDTU che fornisce la quantità di spazio di archiviazione. Per i limiti di archiviazione del pool in base alla dimensione del pool espressa in eDTU, vedere [Limiti di archiviazione e di eDTU dei pool elastici e dei database elastici](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).
3. Considerare la stima eDTU maggiore tra il Passaggio 1 e il Passaggio 2.
4. Vedere la pagina [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/) e trovare la dimensione di pool in eDTU più piccola, che sia maggiore della stima del Passaggio 3.
5. Confrontare il prezzo di pool dal Passaggio 5 con il prezzo dell'utilizzo di livelli di prestazioni appropriati per database singoli.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Uso di altre funzionalità del database SQL con i pool elastici

### <a name="elastic-jobs-and-elastic-pools"></a>Processi e pool elastici

L'uso di un pool permette di semplificare le attività di gestione con l'esecuzione di script in **[processi elastici](sql-database-elastic-jobs-overview.md)**. Un processo elastico elimina la maggior parte delle attività ripetitive associate a un elevato numero di database. Per iniziare, vedere l'[introduzione ai processi elastici](sql-database-elastic-jobs-getting-started.md).

Per altre informazioni sugli altri strumenti di database per usare più database, vedere [Aumentare il numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opzioni di continuità aziendale per i database in un pool elastico
I database in pool supportano in genere le stesse [funzionalità di continuità aziendale](sql-database-business-continuity.md) disponibili per i singoli database.

- **Ripristino temporizzato**: il ripristino temporizzato usa i backup automatici del database per ripristinare un database in un pool a un punto specifico nel tempo. Vedere [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Ripristino geografico**: il ripristino geografico fornisce un'opzione predefinita di ripristino quando un database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md)

- **Replica geografica attiva**: per le applicazioni che hanno requisiti di ripristino più elevati di quelli supportati dal ripristino geografico, configurare la [replica geografica attiva](sql-database-geo-replication-overview.md).

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Gestire i pool elastici e i database SQL tramite il portale di Azure

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Creare un nuovo pool elastico del database SQL tramite il portale di Azure

Esistono due modi per creare un pool elastico nel portale di Azure. È possibile farlo da zero se si conosce la configurazione del pool da usare oppure iniziare con una raccomandazione fornita dal servizio. Il database SQL integra informazioni in base alle quali viene suggerita una configurazione del pool elastico nel caso risulti più conveniente secondo i dati di telemetria relativi all'uso precedente dei database. 

La creazione di un pool elastico da una pagina del server esistente nel portale è il modo più semplice per trasferire i database esistenti in un pool elastico. È anche possibile creare un pool elastico cercando **Pool elastico SQL** nel **Marketplace** o facendo clic su **+Aggiungi** nella pagina dei pool elastici SQL. È possibile specificare un server nuovo o esistente nel flusso di lavoro dei provisioning del pool.

> [!NOTE]
> È possibile creare più pool in un server, ma non aggiungere database da diversi server nello stesso pool.
> 

Il piano tariffario del pool determina le funzionalità disponibili per i database elastici nel pool e il numero massimo di eDTU (MAX eDTU) e la memoria (GB) disponibili per ciascun database. Per informazioni vedere [Limiti delle risorse per il pool elastico](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Per modificare il piano tariffario per il pool, fare clic su **Piano tariffario**, scegliere il piano e quindi fare clic su **Seleziona**.

> [!IMPORTANT]
> Dopo aver scelto il piano tariffario e aver eseguito il commit delle modifiche facendo clic su **OK** nell'ultimo passaggio, non sarà più possibile modificare il piano tariffario del pool. Per modificare il piano tariffario per un pool di database elastici esistente, creare un pool elastico nel piano tariffario desiderato ed eseguire la migrazione dei database in questo nuovo pool.
>

Se i dati di telemetria cronologici relativi all'utilizzo disponibili per i database correnti sono sufficienti, il grafico **Utilizzo di eDTU e GB** e il grafico a barre **Utilizzo di eDTU effettivo** vengono aggiornati per semplificare le decisioni relative alla configurazione. Il servizio potrebbe anche visualizzare un messaggio di raccomandazione per facilitare la scelta delle dimensioni corrette per il pool.

Il servizio di database SQL valuta la cronologia di utilizzo e suggerisce uno o più pool quando questo approccio è più conveniente rispetto all'uso di singoli database. Ogni raccomandazione viene configurata con un subset univoco di database del server che meglio si adatta al pool.

![pool consigliato](./media/sql-database-elastic-pool-create-portal/recommended-pool.png) 

La raccomandazione per il pool include:

- Piano tariffario per il pool (Basic, Standard, Premium o Premium RS)
- **eDTU POOL** appropriato, detto anche eDTU max per pool.
- **MAX eDTU** e **Min eDTU** per ogni database.
- Elenco di database consigliati per il pool.

> [!IMPORTANT]
> Il servizio prende in considerazione la telemetria degli ultimi 30 giorni per la raccomandazione dei pool. Per far sì che un database possa essere considerato un candidato per un pool elastico, deve esistere da almeno 7 giorni. I database che si trovano già in pool elastici non vengono considerati come possibili candidati, in linea con i consigli relativi ai pool elastici.
>

Il servizio valuta le risorse necessarie e la convenienza dello spostamento di singoli database in ogni livello di servizio nei pool dello stesso livello. Ad esempio, vengono valutati tutti i database Standard in un server per l’utilizzo in un pool elastico Standard. Ciò significa che il servizio non effettua consigli relativi a livelli diversi, ad esempio lo spostamento di un database Standard in un pool Premium.

Dopo aver aggiunto i database al pool, le indicazioni verranno generate dinamicamente in base all'uso storico dei database selezionati. Queste indicazioni vengono visualizzate nel grafico relativo all'uso di eDTU e GB, oltre che in un banner di suggerimenti nella parte superiore della pagina **Configura pool**. Queste indicazioni sono concepite per facilitare la creazione di un pool elastico ottimizzato per database specifici.

![Indicazioni dinamiche](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

### <a name="manage-and-monitor-an-elastic-pool"></a>Gestire e monitorare un pool elastico

Dal portale di Azure è possibile monitorare l'uso di un pool elastico e dei database al suo interno. È anche possibile apportare un set di modifiche al pool elastico e inviare tutte le modifiche contemporaneamente. Le modifiche includono l'aggiunta o la rimozione di database, la modifica delle impostazioni del pool elastico o la modifica delle impostazioni del database.

L'immagine seguente illustra un esempio di pool elastico. La visualizzazione include:

* Grafici per il monitoraggio dell'utilizzo delle risorse da parte del pool elastico e dei database al suo interno.
* Il pulsante **Configura pool** per apportare modifiche al pool elastico.
* Il pulsante **Crea database** per creare un database e aggiungerlo al pool elastico corrente.
* Processi elastici che consentono di gestire un numero elevato di database tramite l'esecuzione di script Transact SQL in tutti i database in un elenco.

![Visualizzazione del pool](./media/sql-database-elastic-pool-manage-portal/basic.png)

È possibile passare a un pool specifico per visualizzarne l'utilizzo delle risorse. Per impostazione predefinita, il pool è configurato per mostrare l'utilizzo di eDTU e risorse di archiviazione relativo all'ultima ora. È possibile configurare il grafico per mostrare diverse metriche in diversi intervalli di tempo. Fare clic sul grafico **Utilizzo risorse** in **Monitoraggio pool elastico** per mostrare una visualizzazione dettagliata delle metriche specificate nell'intervallo di tempo specificato.

![Monitoraggio di pool elastici](./media/sql-database-elastic-pool-manage-portal/basic-2.png)

![Pagina metrica](./media/sql-database-elastic-pool-manage-portal/metric.png)

### <a name="to-customize-the-chart-display"></a>Per personalizzare la visualizzazione del grafico

È possibile modificare il grafico e la pagina Metrica per visualizzare altre metriche, ad esempio la percentuale di CPU, la percentuale di IO dei dati e la percentuale di IO del log usata.

![Fare clic su Modifica](./media/sql-database-elastic-pool-manage-portal/edit-metric.png)

Nel modulo **Modifica grafico** selezionare un intervallo di tempo, ad esempio ora precedente, oggi o settimana precedente, oppure fare clic su **personalizzato** per selezionare un intervallo di tempo nelle due settimane precedenti. È possibile scegliere tra un grafico a barre o un grafico a linee e quindi selezionare le risorse da monitorare.

> [!Note]
> Solo le metriche con la stessa unità di misura possono essere visualizzate nel grafico nello stesso momento. Se ad esempio si seleziona "eDTU percentage" (Percentuale eDTU), sarà possibile selezionare solo altre metriche con percentuale come unità di misura.
>

![Fare clic su Modifica](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

### <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>Gestire e monitorare database in un pool elastico

È possibile monitorare anche i singoli database per potenziali problemi. In **Monitoraggio database elastico**è disponibile un grafico che mostra le metriche relative a cinque database. Per impostazione predefinita, il grafico mostra i primi cinque database nel pool per utilizzo di eDTU medio nell'ora precedente. 

![Monitoraggio di pool elastici](./media/sql-database-elastic-pool-manage-portal/basic-3.png)

Fare clic su **eDTU usage for databases for the past hour** (Uso di eDTU per i database nell'ora precedente) in **Monitoraggio database elastico**. Verrà visualizzato **Utilizzo risorse database** che offre una visualizzazione dettagliata dell'uso del database nel pool. La griglia nella parte inferiore della pagina consente di selezionare fino a cinque database nel pool per visualizzarne l'uso nel grafico. È anche possibile personalizzare le metriche e l'intervallo di tempo visualizzati nel grafico facendo clic su **Modifica grafico**.

![Pagina Utilizzo risorse database](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-view"></a>Per personalizzare la visualizzazione

È possibile modificare il grafico per selezionare un intervallo di tempo, ad esempio ora precedente o ultime 24 ore, oppure fare clic su **personalizzato** per selezionare un giorno diverso nelle 2 settimane precedenti.

![Fare clic su Modifica grafico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

![Fare clic su personalizzato](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

È anche possibile fare clic sull'elenco a discesa **Confronta database in base a** e selezionare una metrica diversa da usare per il confronto dei database.

![Modificare il grafico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Per selezionare i database da monitorare

Nell'elenco dei database della pagina **Utilizzo risorse database** è possibile trovare database specifici scorrendo le pagine dell'elenco o digitando il nome di un database. Usare la casella di controllo per selezionare il database.

![Cercare i database da monitorare](./media/sql-database-elastic-pool-manage-portal/select-dbs.png)


### <a name="add-an-alert-to-an-elastic-pool-resource"></a>Aggiungere un avviso a una risorsa di pool elastico

È possibile aggiungere regole a un pool elastico per l'invio di messaggi di posta elettronica a persone oppure stringhe di avviso a endpoint di URL quando il pool elastico raggiunge la soglia d'uso impostata.

**Per aggiungere un avviso a una risorsa qualsiasi:**

1. Fare clic sul grafico **Utilizzo risorse** per aprire la pagina **Metrica**. Fare clic su **Aggiungi avviso** e inserire le informazioni nella pagina **Aggiungi una regola di avviso**. La **risorsa** viene impostata automaticamente come pool corrente.
2. Inserire un **Nome** e una **Descrizione** che serviranno a identificare l'avviso per l'utente e i destinatari.
3. Scegliere una **Metrica** in base alla quale creare un avviso dall'elenco.

   Il grafico mostra in modo dinamico l'utilizzo delle risorse per la metrica selezionata in modo da scegliere una soglia.

4. Scegliere una **Condizione**, ad esempio maggiore di, minore di e così via, e una **Soglia**.
5. Scegliere un **Periodo** di tempo entro il quale la regola della metrica deve essere soddisfatta prima dell'attivazione dell'avviso.
6. Fare clic su **OK**.

Per altre informazioni, vedere [Usare il portale di Azure per creare avvisi per il database SQL di Azure](sql-database-insights-alerts-portal.md).

### <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool elastico

È possibile aggiungere o rimuovere i database da un pool esistente. I database possono trovarsi in altri pool. Tuttavia, è possibile aggiungere solo i database che sono nello stesso server logico.

 ![Fare clic su Configura pool](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

![Fare clic su Aggiungi al pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

![Selezionare i database da aggiungere](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

![Aggiunte di pool in sospeso](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

![Fare clic su Salva.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

### <a name="move-a-database-out-of-an-elastic-pool"></a>Spostare un database da un pool elastico

![elenchi di database](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

### <a name="change-performance-settings-of-an-elastic-pool"></a>Modificare le impostazioni delle prestazioni di un pool elastico

Durante il monitoraggio dell'utilizzo delle risorse di un pool elastico possono rendersi necessarie alcune modifiche, ad esempio dei limiti di archiviazione o di prestazioni. Si potrebbe voler modificare le impostazioni del database nel pool. È possibile modificare la configurazione del pool in qualsiasi momento per ottenere il miglior compromesso tra prestazioni e costi. Per altre informazioni, vedere [Quando usare un pool elastico](sql-database-elastic-pool.md).

Per modificare i limiti di archiviazione o eDTU per il pool e il numero di eDTU per il database:

![Utilizzo delle risorse del pool elastico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Gestire i pool di database elastici e i database tramite PowerShell

Per creare e gestire pool elastici del database SQL con Azure PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). Per creare e gestire i database, i server e le regole del firewall, vedere [Creare e gestire i server e i database di database SQL di Azure con PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell). 

> [!TIP]
> Per gli script di esempio di PowerShell, vedere [Creare pool elastici e spostare i database tra i pool e al loro esterno usando PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) e [Usare PowerShell per il monitoraggio e il ridimensionamento di un pool elastico SQL nel database SQL di Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Descrizione |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Consente di creare un pool di database elastico all'interno del server SQL logico.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Consente di ottenere pool elastici e i relativi valori di proprietà in un server SQL logico.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Consente di modificare le proprietà di un pool di database elastico all'interno del server SQL logico. Ad esempio, usare la proprietà **StorageMB** per modificare l'archiviazione massima di un pool elastico.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Consente di eliminare un pool di database elastico all'interno del server SQL logico.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Consente di ottenere lo stato delle operazioni in un pool elastico in un server SQL logico.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Consente di creare un nuovo database in un pool esistente o in un database singolo. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Ottiene uno o più database.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Consente di impostare le proprietà per un database oppure sposta un database esistente all'interno o all'esterno di in un pool elastico.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Rimuove un database.|


> [!TIP]
> La creazione di molti database in un pool elastico può richiedere tempo quando viene eseguita tramite il portale o i cmdlet di PowerShell che creano un database singolo alla volta. Per automatizzare la creazione in un pool elastico, vedere [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Gestire i pool elastici e i database SQL tramite l'interfaccia della riga di comando di Azure

Per creare e gestire pool elastici del database SQL con l'[interfaccia della riga di comando di Azure](/cli/azure/overview), usare i comandi seguenti del [database SQL per l'interfaccia della riga di comando di Azure](/cli/azure/sql/db). Usare [Cloud Shell](/azure/cloud-shell/overview) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows. 

> [!TIP]
> Per gli script di esempio dell'interfaccia della riga di comando di Azure, vedere [Usare l'interfaccia della riga di comando per spostare un database SQL di Azure in un pool elastico SQL](scripts/sql-database-move-database-between-pools-cli.md) e [Usare l'interfaccia della riga di comando di Azure per ridimensionare un pool elastico SQL nel database SQL di Azure](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Descrizione |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#create)|Consente di creare un pool elastico.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#list)|Restituisce un elenco di pool elastici in un server.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#list-dbs)|Restituisce un elenco di database in un pool elastico.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#list-editions)|Include anche le impostazioni di DTU del pool disponibile, i limiti di archiviazione e per le impostazioni per ogni database. Al fine di ridurre il livello di dettaglio, i limiti di spazio di archiviazione aggiuntivo e le impostazioni per ogni database sono nascoste per impostazione predefinita.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#update)|Consente di aggiornare un pool elastico.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#delete)|Consente di eliminare un pool elastico.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Gestire i database all'interno dei pool di database elastici con Transact-SQL

Per creare e spostare i database all'interno dei pool elastici esistenti o per restituire informazioni su un pool elastico del database SQL con Transact-SQL, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando il portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o qualsiasi altro programma che può connettersi a un server di database SQL di Azure e passare comandi Transact-SQL. Per creare e gestire i database, i server e le regole del firewall, vedere [Creare e gestire i server e i database di database SQL di Azure con Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Non è possibile creare, aggiornare o eliminare un pool elastico del database SQL di Azure con Transact-SQL. È possibile aggiungere o rimuovere i database da un pool elastico ed è possibile usare le DMV per restituire informazioni sui pool elastici esistenti.
>

| Comando | Descrizione |
| --- | --- |
|[CREATE DATABASE (database SQL di Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Consente di creare un nuovo database in un pool esistente o in un database singolo. Per creare un nuovo database è necessario essere connessi al database master.|
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Consente di spostare un database all'interno, all'esterno o tra pool elastici.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Questo comando elimina un database.|
|[sys.elastic_pool_resource_stats (Database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Restituisce statistiche di uso delle risorse per tutti i pool di database elastici in un server logico. Per ogni pool di database elastico c'è una riga per ogni finestra di report di 15 secondi, quattro righe per ogni minuto. Sono inclusi CPU, IO, Log, uso dell'archiviazione e uso di richieste/sessioni simultanee da parte di tutti i database nel pool.|
|[sys.database_service_objectives (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Restituisce l'edizione (livello di servizio), l'obiettivo di servizio (piano tariffario) e il nome del pool elastico, se presente, di un database SQL di Azure o un'istanza di Azure SQL Data Warehouse. Se si è connessi al database master in un server di database SQL di Azure, restituisce informazioni su tutti i database. Per Azure SQL Data Warehouse, è necessario essere connessi al database master.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Gestire i pool di database elastici e i database tramite l'API REST

Per creare e gestire i pool di database SQL elastici usare queste richieste API REST.

| Comando | Descrizione |
| --- | --- |
|[Pool di database elastici: crea o aggiorna](/rest/api/sql/elasticpools/createorupdate)|Crea un nuovo pool elastico o aggiorna un pool elastico esistente.|
|[Pool di database elastici: eliminare](/rest/api/sql/elasticpools/delete)|Consente di eliminare un pool elastico.|
|[Pool di database elastici: ottieni](/rest/api/sql/elasticpools/get)|Ottiene un pool elastico.|
|[Pool di database elastici: elenca dal server](/rest/api/sql/elasticpools/listbyserver)|Restituisce un elenco di pool elastici in un server.|
|[Pool di database elastici: aggiorna](/rest/api/sql/elasticpools/update)|Consente di aggiornare un pool elastico esistente.|
|[Pool di database elastici consigliati: ottieni](/rest/api/sql/recommendedelasticpools/get)|Consente di ottenere un pool elastico consigliato.|
|[Pool di database elastici consigliati: elenca dal server](/rest/api/sql/recommendedelasticpools/listbyserver)|Restituisce i pool di database elastici consigliati.|
|[Pool di database elastici consigliati: elenca metriche](/rest/api/sql/recommendedelasticpools/listmetrics)|Restituisce le metriche dei pool di database elastici consigliati.|
|[Attività del pool elastico](/rest/api/sql/elasticpoolactivities)|Restituisce le attività del pool elastico.|
|[Attività del database del pool elastico](/rest/api/sql/elasticpooldatabaseactivities)|Restituisce l'attività sul database all'interno di un pool elastico.|
|[Database: crea o aggiorna](/rest/api/sql/databases/createorupdate)|Crea un nuovo database o ne aggiorna uno esistente.|
|[Databases - Get](/rest/api/sql/databases/get)|Ottiene un database.|
|[Database: ottieni da pool elastico](/rest/api/sql/databases/getbyelasticpool)|Ottiene un database all'interno di un pool elastico.|
|[Database: ottieni da pool elastico consigliato](/rest/api/sql/databases/getbyrecommendedelasticpool)|Ottiene un database all'interno di un pool elastico consigliato.|
|[Database: elenca da pool elastico](/rest/api/sql/databases/listbyelasticpool)|Restituisce un elenco di database in un pool elastico.|
|[Database: elenca da pool elastico consigliato](/rest/api/sql/databases/listbyrecommendedelasticpool)|Restituisce un elenco di database in un pool elastico consigliato.|
|[Databases - List By Server](/rest/api/sql/databases/listbyserver)|Restituisce un elenco di database in un server.|
|[Databases - Update](/rest/api/sql/databases/update)|Aggiorna un database esistente.|

## <a name="next-steps"></a>Passaggi successivi

* Un video è disponibile in [Esercitazione video di Microsoft Virtual Academy sulle funzionalità di elasticità del database SQL di Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Per un'esercitazione sul SaaS con i pool elastici, vedere [Introduzione all'applicazione SaaS Wingtip](sql-database-wtp-overview.md).
