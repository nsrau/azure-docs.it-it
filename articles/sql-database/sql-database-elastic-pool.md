---
title: "Informazioni sui pool elastici Gestire più database SQL: Azure | Microsoft Docs"
description: "Gestire e ridimensionare centinaia o migliaia di database SQL usando i pool elastici. Un unico prezzo per le risorse che è possibile distribuire in base alle esigenze."
keywords: "più database, risorse di database, prestazioni di database"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 05/31/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: de0c9969ec28e7b6e57bc384ac3ec47191096a9b
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---

# <a name="elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>Gestire e ridimensionare più database SQL usando i pool elastici

I pool elastici di database SQL offrono una soluzione semplice e conveniente per la gestione e il ridimensionamento di più database con esigenze di utilizzo variabili e imprevedibili. I database in un pool elastico si trovano in un server di database SQL di Azure singolo e condividono un determinato numero di risorse ([unità di transazione di database elastico](sql-database-what-is-a-dtu.md) (eDTU)) a un prezzo specifico. I pool elastici nel database SQL di Azure consentono agli sviluppatori di SaaS di ottimizzare i costi per un gruppo di database all'interno di un budget definito, garantendo allo stesso tempo prestazioni elastiche per ogni database.   

> [!NOTE]
> I pool elastici sono disponibili a livello generale in tutte le aree di Azure tranne India occidentale, dove sono attualmente in anteprima.  I pool elastici verranno resi disponibili a livello generale in quest'area non appena possibile.
>

## <a name="overview-of-elastic-pools"></a>Panoramica dei pool elastici 

Gli sviluppatori di SaaS compilano applicazioni basate su livelli di dati su larga scala costituiti da più database. Un modello di applicazione comune è il provisioning di un database singolo per ogni cliente. Clienti diversi, tuttavia, hanno spesso modelli di utilizzo differenti e non prevedibili. Per questo motivo, è difficile stabilire a priori i requisiti di risorse di ogni singolo utente di database. In genere, le opzioni erano due: 

- Effettuare il provisioning di risorse eccessivo in base all'utilizzo massimo e pagare più del necessario, o
- Ricorrere al provisioning insufficiente per risparmiare sui costi, a scapito delle prestazioni e della soddisfazione del cliente durante i picchi. 

I pool elastici risolvono il problema assicurando ai database l'ottenimento delle risorse di prestazioni necessarie, quando richieste. Forniscono un meccanismo semplice di allocazione delle risorse che rientra in un budget prevedibile. Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

I pool elastici consentono agli sviluppatori di acquistare [unità di transazione di database elastico](sql-database-what-is-a-dtu.md) (eDTU) per un pool condiviso da più database, per supportare periodi di utilizzo imprevisti da parte dei singoli database. Il requisito di eDTU di un pool è determinato dall'utilizzo aggregato dei relativi database. Il numero di eDTU disponibile per il pool dipende dal budget dello sviluppatore. Lo sviluppatore aggiunge semplicemente database al pool, imposta il limite minimo e massimo di eDTU per i database e quindi imposta il numero di eDTU del pool in base al budget. Utilizzando i pool, lo sviluppatore può aumentare con facilità i servizi offerti da una piccola nuova impresa fino a un'azienda matura in continua crescita.

All'interno del pool i singoli database sono sufficientemente flessibili da assicurare una scalabilità automatica nell'ambito di parametri prefissati. Se il carico di lavoro è importante, un database può utilizzare più eDTU per soddisfare la domanda. Se invece il carico di lavoro è più leggero, i database in assenza di carico non utilizzano gli eDTU. La possibilità di effettuare il provisioning delle risorse per l'intero pool e non per i singoli database semplifica le attività di gestione. Il budget del pool, inoltre, è facilmente prevedibile. È possibile aggiungere altre eDTU a un pool esistente senza causare tempi di inattività del database, tranne per il fatto che potrebbe essere necessario spostare i database per fornire risorse di calcolo aggiuntive per le nuove eDTU riservate. Analogamente, se gli eDTU aggiuntivi non sono più necessari, è possibile rimuoverli da un pool esistente in qualsiasi momento. È possibile aggiungere e rimuovere database dal pool. Se si prevede che un database sottoutilizzerà le proprie risorse, è possibile rimuoverlo.

È possibile creare e gestire un pool elastico mediante il [portale di Azure](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) e l'API REST. 

## <a name="when-to-consider-a-pool"></a>Quando prendere in considerazione un pool
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
Se la somma di DTU di livelli di prestazioni per singoli database è superiore di 1,5x rispetto ai DTU necessari per il pool, allora un pool elastico è più conveniente. Per le dimensioni disponibili, vedere [Limiti di archiviazione e di eDTU per i pool elastici e dei database elastici](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

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

## <a name="sizing-an-elastic-pool"></a>Ridimensionamento di un pool elastico
La dimensione ottimale per un pool dipende dalle eDTU di aggregazione e dalle risorse di archiviazione necessarie per tutti i database nel pool. Questo richiede di stabilire il valore maggiore tra i seguenti:

* Dtu massima utilizzata da tutti i database nel pool.
* Byte di archiviazione massima utilizzati da tutti i database nel pool.

Per le dimensioni disponibili, vedere [Limiti di archiviazione e di eDTU per i pool elastici e dei database elastici](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

Database SQL valuta automaticamente la cronologia d’utilizzo delle risorse dei database in un server di database SQL esistente e consiglia una configurazione appropriata del pool nel portale di Azure. Oltre alle raccomandazioni, una funzionalità incorporata stima l'utilizzo di eDTU per un gruppo personalizzato di database del server. Ciò consente di eseguire un'analisi di simulazione tramite l'aggiunta interattiva di database al pool e la relativa rimozione in modo da ottenere un'analisi di utilizzo delle risorse e suggerimenti di ridimensionamento prima di eseguire il commit delle modifiche. Per le procedure, vedere [Monitorare e gestire un pool di database elastici con il portale di Azure](sql-database-elastic-pool-manage-portal.md).

Nei casi in cui non è possibile utilizzare gli strumenti, le seguenti istruzioni dettagliate consentono di stimare se un pool è più conveniente rispetto ai database singoli:

1. Stimare le eDTU necessarie per il pool come segue:

   MAX (<*numero totale di database* X *utilizzo medio di DTU per DB*>,<br>
   <*numero di database in picco contemporaneamente* X *picco di utilizzo di DTU per DB*)
2. Stimare lo spazio di archiviazione necessario per il pool aggiungendo il numero di byte necessari per tutti i database nel pool. Determinare quindi la dimensione del pool in eDTU che fornisce la quantità di spazio di archiviazione. Per i limiti di archiviazione del pool in base alla dimensione del pool espressa in eDTU, vedere [Limiti di archiviazione e di eDTU dei pool elastici e dei database elastici](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).
3. Considerare la stima eDTU maggiore tra il Passaggio 1 e il Passaggio 2.
4. Vedere la pagina [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/) e trovare la dimensione di pool in eDTU più piccola, che sia maggiore della stima del Passaggio 3.
5. Confrontare il prezzo di pool dal Passaggio 5 con il prezzo dell'utilizzo di livelli di prestazioni appropriati per database singoli.

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>Limiti di archiviazione e di eDTU per i pool elastici

Le tabelle seguenti descrivono i limiti delle risorse dei pool elastici.  Si noti che i limiti delle risorse di database singoli nei pool elastici sono in genere identici a quelli di database singoli all'esterno dei pool in base alle DTU e al livello di servizio.  Ad esempio, il numero massimo di ruoli di lavoro simultanei per un database S2 è 120.  Pertanto, anche il numero massimo di ruoli di lavoro simultanei per un database in un pool Standard è 120 se il numero massimo di DTU per ogni database nel pool è 50 (che è equivalente a S2).

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Se vengono utilizzate tutte le DTU di un pool elastico, ogni database del pool riceve un'uguale quantità di risorse per l'elaborazione di query.  Il servizio di database SQL suddivide equamente le risorse fra i database con intervalli equivalenti per i tempi di calcolo. La condivisione equa delle risorse del pool elastico accompagna ogni quantità di risorse altrimenti garantita per ogni database quando il numero minimo di DTU per ogni database è impostato su un valore diverso da zero.

## <a name="database-properties-for-pooled-databases"></a>Proprietà del database per i database in pool

La tabella seguente descrive le proprietà per i database in pool.

| Proprietà | Descrizione |
|:--- |:--- |
| Numero massimo di eDTU per database |Il numero massimo di eDTU di cui un database può usufruire nel pool se disponibili sulla base dell'uso da parte di altri database nel pool.  Il numero massimo di eDTU per database non è una garanzia di risorse per un database.  Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Impostare il numero massimo di eDTU per database sufficiente per gestire i picchi di utilizzo dei database. È previsto un certo grado di overcommit perché il pool in genere presuppone modelli di utilizzo dei database a freddo e a caldo in cui i database non raggiungono il picco contemporaneamente. Si pensi al caso in cui il picco di utilizzo per ogni database sia di 20 eDTU e solo il 20% dei 100 database nel pool raggiunga il picco nello stesso momento.  Se il numero massimo di eDTU per ogni database è impostato su 20 eDTU, è ragionevole eseguire l'overcommit del pool moltiplicando per 5 e impostare il numero di eDTU su 400. |
| Numero minimo di eDTU per database |Il numero minimo di eDTU garantito a ogni database nel pool.  Si tratta di un'impostazione globale che si applica a tutti i database nel pool. Il numero minimo di eDTU per database può essere impostato su 0, che corrisponde anche al valore predefinito. Questa proprietà è impostata su un valore compreso tra 0 e l'utilizzo medio di eDTU per ogni database. Il prodotto tra il numero di database nel pool e il numero minimo di eDTU per database non può superare il numero di eDTU per pool.  Ad esempio, se un pool dispone di 20 database e di un numero minimo di eDTU per database impostato su 10 eDTU, il numero di eDTU per pool deve essere almeno pari a 200. |
| Spazio di archiviazione dati massimo per database |Lo spazio di archiviazione massimo per un database in un pool. I database in pool condividono lo spazio di archiviazione del pool, quindi lo spazio di archiviazione del database è limitato allo spazio di archiviazione del pool rimanente e allo spazio massimo per database. Lo spazio di archiviazione massimo per database indica le dimensioni massime dei file di dati e non include lo spazio usato dai file di log. |
|||

## <a name="elastic-jobs"></a>Processi elastici
L'uso di un pool permette di semplificare le attività di gestione con l'esecuzione di script in **[processi elastici](sql-database-elastic-jobs-overview.md)**. Un processo elastico elimina la maggior parte delle attività ripetitive associate a un elevato numero di database. Per iniziare, vedere l'[introduzione ai processi elastici](sql-database-elastic-jobs-getting-started.md).

Per altre informazioni sugli altri strumenti di database per usare più database, vedere [Aumentare il numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md).

## <a name="business-continuity-features-for-databases-in-a-pool"></a>Funzionalità di continuità aziendale per i database in un pool
I database in pool supportano in genere le stesse [funzionalità di continuità aziendale](sql-database-business-continuity.md) disponibili per i singoli database.

### <a name="point-in-time-restore"></a>Ripristino temporizzato
Il ripristino temporizzato usa i backup automatici del database per ripristinare un database in un pool a un punto specifico nel tempo. Vedere [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>Ripristino geografico
Il ripristino geografico fornisce un'opzione predefinita di ripristino quando un database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>Replica geografica attiva
Per le applicazioni che hanno requisiti di ripristino più elevati di quelli supportati dal ripristino geografico, configurare la [replica geografica attiva](sql-database-geo-replication-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* È possibile creare e gestire un pool elastico mediante il [portale di Azure](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) e l'API REST.
* Un video è disponibile in [Esercitazione video di Microsoft Virtual Academy sulle funzionalità di elasticità del database SQL di Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

