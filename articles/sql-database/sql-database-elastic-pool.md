---
title: 'Gestire più database SQL con i pool di database elastici: Azure | Microsoft Docs'
description: Gestire e ridimensionare centinaia o migliaia di database SQL usando i pool elastici. Un unico prezzo per le risorse che è possibile distribuire in base alle esigenze.
keywords: più database, risorse di database, prestazioni di database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 04/10/2018
ms.author: ninarn
ms.topic: article
ms.openlocfilehash: 33f4430baacbe50f3d4c7da857ee4345d4f74928
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
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

I pool elastici consentono agli sviluppatori di acquistare risorse per un pool condiviso da più database, in modo da supportare periodi di utilizzo imprevisti da parte dei singoli database. È possibile configurare le risorse per il pool in base al [modello di acquisto basato su DTU (anteprima)](sql-database-service-tiers.md#dtu-based-purchasing-model) o al [modello di acquisto basato su vCore (anteprima)](sql-database-service-tiers.md#vcore-based-purchasing-model-preview). Il requisito di risorse per un pool è determinato dall'utilizzo aggregato dei relativi database. La quantità di risorse disponibili per il pool dipende dal budget dello sviluppatore. Lo sviluppatore aggiunge semplicemente database al pool, imposta la quantità minima e massima di risorse per i database (DTU minimi o massimi oppure vCore minimi o massimi a seconda della scelta del modello di risorse), quindi imposta le risorse del pool in base al proprio budget. Utilizzando i pool, lo sviluppatore può aumentare con facilità i servizi offerti da una piccola nuova impresa fino a un'azienda matura in continua crescita.

All'interno del pool i singoli database sono sufficientemente flessibili da assicurare una scalabilità automatica nell'ambito di parametri prefissati. Se il carico di lavoro è importante, un database può utilizzare più risorse per soddisfare la domanda. Se invece il carico di lavoro è più leggero, i database in assenza di carico non utilizzano risorse. La possibilità di effettuare il provisioning delle risorse per l'intero pool e non per i singoli database semplifica le attività di gestione. È inoltre disponibile un budget per il pool prevedibile. È possibile aggiungere altre risorse a un pool esistente senza causare tempi di inattività, ad eccezione del caso in cui sia necessario spostare i database per fornire risorse di calcolo aggiuntive per le nuove eDTU riservate. Analogamente, se le risorse aggiuntive non sono più necessarie, è possibile rimuoverle da un pool esistente in qualsiasi momento. È possibile aggiungere e rimuovere database dal pool. Se si prevede che un database sottoutilizzerà le proprie risorse, è possibile rimuoverlo.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando considerare un pool elastico del database SQL

I pool sono adatti per un numero elevato di database con modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti.

Più database è possibile aggiungere a un pool, maggiori diventano i risparmi. In base al modello di uso dell'applicazione, è possibile osservare un risparmio con soli due database S3.

Le sezioni seguenti aiutano a comprendere se l'insieme specifico di database può trarre vantaggio dall'uso di un pool. Gli esempi utilizzano pool Standard, ma gli stessi principi si applicano anche ai pool Basic e Premium.

### <a name="assessing-database-utilization-patterns"></a>Valutazione dei modelli di utilizzo di database

Nella figura seguente viene illustrato un esempio di un database che trascorre il tempo di inattività, ma anche periodicamente picchi di attività. Si tratta di un modello di utilizzo adatto per un pool:

   ![un database singolo adatto per un pool](./media/sql-database-elastic-pool/one-database.png)

Per il periodo di cinque minuti illustrato, DB1 raggiunge picchi fino a 90 DTU, ma l'utilizzo medio complessivo è inferiore a cinque DTU. L'esecuzione di questo carico di lavoro in un database singolo richiede il livello di prestazioni S3, ma in questo modo la maggior parte delle risorse rimane inutilizzata durante i periodi di minore attività.

Un pool consente la condivisione tra più database di queste DTU inutilizzate e quindi riduce le DTU richieste e i costi complessivi.

Compila l'esempio precedente, si supponga che vi sono altri database con i modelli di utilizzo simili come DB1. Nelle due figure seguenti vengono presentati in parallelo l'utilizzo di quattro database e quello di 20 database all'interno di uno stesso grafico per mostrare l'assenza di sovrapposizione degli elementi che rappresentano l'utilizzo dei database nel tempo quando viene usato il modello di acquisto basato su DTU:

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

Se la quantità aggregata di risorse per i singoli database è maggiore di 1,5 volte rispetto alle risorse necessarie per il pool, un pool elastico è più conveniente.

***Esempio di modello di acquisto basato su DTU***<br>
Sono necessari almeno due database S3 o 15 database S0 perché un pool di 100 eDTU risulti più conveniente rispetto all'uso di livelli di prestazioni per database singoli.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Numero massimo di picco contemporaneamente database

Condividendo le risorse, non tutti i database in un pool possono usare contemporaneamente le risorse fino al limite disponibile per i singoli database. Meno database raggiungono il picco contemporaneamente, minore è il valore da impostare per le risorse del pool e quindi più redditizio diventa il pool stesso. In generale, non più di 2/3 (o del 67%) dei database nel pool dovrebbe raggiungere contemporaneamente il picco di utilizzo delle risorse.

***Esempio di modello di acquisto basato su DTU***<br>
Per ridurre i costi relativi a tre database S3 in un pool di 200 eDTU, al massimo due dei tre database possono raggiungere il picco di utilizzo contemporaneamente. In caso contrario, se più di due di questi quattro database S3 raggiungono il picco contemporaneamente, il pool dovrebbe essere ridimensionato a più di 200 eDTU. Se il pool viene ridimensionato a più di 200 eDTU, più database S3 dovranno essere aggiunti al pool per mantenere i costi inferiori rispetto a quelli dei livelli di prestazioni per singoli database.

Si noti in questo esempio non prende in considerazione l'utilizzo di altri database nel pool. Se tutti i database con alcune utilizzo in qualsiasi punto nel tempo, minore di 2/3 (o 67%) dei database possono picco contemporaneamente.

### <a name="resource-utilization-per-database"></a>Utilizzo delle risorse per ogni database
Una notevole differenza tra il picco e l'utilizzo medio di un database indica periodi prolungati di utilizzo ridotto e brevi periodi di utilizzo elevato. Questo modello di utilizzo è ideale per la condivisione delle risorse tra database. Un database deve essere considerato per un pool quando relativo picchi di utilizzo sono circa 1.5 volte maggiore relativo utilizzo medio.

***Esempio di modello di acquisto basato su DTU***<br>
Un database S3 con picchi di 100 DTU e un utilizzo medio di 67 DTU o meno è un buon candidato per la condivisione di eDTU in un pool. In alternativa, un database S1 con picchi di 20 DTU e utilizzo medio di 13 DTU o meno è un buon candidato per un pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Come scegliere le dimensioni più adatte del pool

La dimensione ottimale per un pool dipende dalle risorse di aggregazione e dalle risorse di archiviazione necessarie per tutti i database nel pool. È quindi necessario stabilire quanto segue:

* Quantità massima di risorse utilizzate da tutti i database nel pool, ovvero numero massimo di DTU o vCore in base al modello di risorse selezionato.
* Byte di archiviazione massima utilizzati da tutti i database nel pool.

Per i livelli di servizio disponibili per ogni modello di risorse, vedere il [modello di acquisto basato su DTU](sql-database-service-tiers.md#dtu-based-purchasing-model) o il [modello di acquisto basato su vCore (anteprima)](sql-database-service-tiers.md#vcore-based-purchasing-model-preview).

Database SQL valuta automaticamente la cronologia d’utilizzo delle risorse dei database in un server di database SQL esistente e consiglia una configurazione appropriata del pool nel portale di Azure. Oltre alle raccomandazioni, una funzionalità incorporata stima l'utilizzo di eDTU per un gruppo personalizzato di database del server. Ciò consente di eseguire un'analisi di simulazione tramite l'aggiunta interattiva di database al pool e la relativa rimozione in modo da ottenere un'analisi di utilizzo delle risorse e suggerimenti di ridimensionamento prima di eseguire il commit delle modifiche. Per le procedure, vedere [Monitorare e gestire un pool di database elastici con il portale di Azure](sql-database-elastic-pool-manage-portal.md).

Nei casi in cui non è possibile utilizzare gli strumenti, le seguenti istruzioni dettagliate consentono di stimare se un pool è più conveniente rispetto ai database singoli:

1. Stimare le eDTU o i vCore necessari per il pool come segue:

   Per il modello di acquisto basato su DTU: MAX(<*numero totale di database* X *utilizzo medio di DTU per database*>,<br>
   <*numero di database in picco contemporaneamente* X *picco di utilizzo di DTU per DB*)

   Per il modello di acquisto basato su vCore: MAX(<*numero totale di database* X *utilizzo medio di vCore per database*>,<br>
   <*numero di database in picco contemporaneamente* X *picco di utilizzo di vCore per database*)

2. Stimare lo spazio di archiviazione necessario per il pool aggiungendo il numero di byte necessari per tutti i database nel pool. Determinare quindi la dimensione del pool in eDTU che fornisce la quantità di spazio di archiviazione.
3. Per il modello di acquisto basato su DTU, considerare la stima di eDTU maggiore tra il Passaggio 1 e il Passaggio 2. Per il modello di acquisto basato su vCore, considerare la stima di vCore del Passaggio 1.
4. Vedere la [pagina sui prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/) e trovare la dimensione di pool più piccola, che sia maggiore della stima del Passaggio 3.
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

Esistono due modi per creare un pool elastico nel portale di Azure.
1. È possibile creare un pool elastico cercando **pool elastico SQL** nel **Marketplace** o facendo clic su **+Aggiungi** nel pannello Pool elastici SQL. È possibile specificare un server nuovo o esistente nel flusso di lavoro dei provisioning del pool.
2. In alternativa, è possibile creare un pool elastico selezionando un server SQL esistente e facendo clic su **Crea pool** per creare un pool direttamente in tale server. L'unica differenza è che in questo caso si ignora il passaggio in cui si specifica il server durante il flusso di lavoro di provisioning del pool.

> [!NOTE]
> È possibile creare più pool in un server, ma non aggiungere database da diversi server nello stesso pool.

Il livello di servizio del pool determina le funzionalità disponibili per i database elastici nel pool e la quantità massima di risorse disponibili per ogni database. Per informazioni dettagliate, vedere i limiti delle risorse per i pool elastici nel [modello basato su DTU](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels) e nel [modello basato su vCore](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Per configurare le risorse e i prezzi del pool, fare clic su **Configura pool**. Selezionare quindi un livello di servizio, aggiungere database al pool e configurare i limiti delle risorse per il pool e i relativi database.

Al termine della configurazione del pool, è possibile fare clic su Applica, assegnare un nome al pool e fare clic su OK per creare il pool.

### <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorare un pool elastico e i relativi database

Dal portale di Azure è possibile monitorare l'uso di un pool elastico e dei database al suo interno. È anche possibile apportare un set di modifiche al pool elastico e inviare tutte le modifiche contemporaneamente. Le modifiche includono l'aggiunta o la rimozione di database, la modifica delle impostazioni del pool elastico o la modifica delle impostazioni del database.

Per avviare il monitoraggio del pool elastico, trovare e aprire un pool elastico nel portale. Verrà visualizzata una schermata che offre una panoramica dello stato del pool elastico. Sono inclusi:

* Grafici di monitoraggio che illustrano l'utilizzo delle risorse del pool elastico
* Avvisi recenti e suggerimenti, se disponibili, per il pool elastico

L'immagine seguente illustra un esempio di pool elastico:

![Visualizzazione del pool](./media/sql-database-elastic-pool-manage-portal/basic.png)

Per visualizzare altre informazioni relative al pool è possibile fare clic su una qualsiasi delle informazioni disponibili in questa panoramica. Facendo clic sul grafico **Utilizzo risorse**, viene aperta la visualizzazione di Monitoraggio di Azure in cui è possibile personalizzare le metriche e l'intervallo di tempo visualizzati nel grafico. Facendo clic su una qualsiasi delle notifiche disponibili, viene visualizzato un pannello che mostra tutti i dettagli dell'avviso o del suggerimento.

Per monitorare i database all'interno del pool, è possibile fare clic su **Utilizzo risorse database** nella sezione **Monitoraggio** del menu delle risorse a sinistra.

![Pagina Utilizzo risorse database](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

#### <a name="to-customize-the-chart-display"></a>Per personalizzare la visualizzazione del grafico

È possibile modificare il grafico e la pagina Metrica per visualizzare altre metriche, ad esempio la percentuale di CPU, la percentuale di IO dei dati e la percentuale di IO del log usata.

Nel modulo **Modifica grafico** è possibile specificare un intervallo di tempo fisso, oppure fare clic su **personalizzato** per selezionare un periodo qualsiasi di 24 ore nelle ultime due settimane, e quindi selezionare le risorse da monitorare.

#### <a name="to-select-databases-to-monitor"></a>Per selezionare i database da monitorare

Per impostazione predefinita, il grafico nel pannello **Utilizzo risorse database** mostra i cinque database più utilizzati in base a DTU o CPU, a seconda del livello di servizio. È possibile visualizzare i dati relativi ad altri database nel grafico selezionando e deselezionando i database dall'elenco sottostante tramite le caselle di controllo a sinistra.

È inoltre possibile selezionare altre metriche da visualizzare in modalità affiancata in questa tabella di database per ottenere una visione più dettagliata delle prestazioni dei database.

Per altre informazioni, vedere [Usare il portale di Azure per creare avvisi per il database SQL di Azure](sql-database-insights-alerts-portal.md).

### <a name="manage-an-elastic-pool-and-its-databases"></a>Gestire un pool elastico e i relativi database

Tutte le impostazioni del pool sono reperibili in una stessa area dell'interfaccia, ovvero il pannello **Configura pool**. Per accedervi, individuare un pool elastico nel portale e fare clic su **Configura pool** nella parte superiore del pannello o nel menu delle risorse a sinistra.

Da qui è possibile eseguire una combinazione qualsiasi di modifiche tra quelle elencate di seguito e salvarle in un unico batch:
1. Modificare il livello di servizio del pool
2. Aumentare o ridurre le prestazioni (DTU o vCore) e le risorse di archiviazione
3. Aggiungere database nel pool o rimuoverli
4. Impostare un limite di prestazioni minimo (garantito) e massimo per i database nei pool
5. Rivedere il riepilogo dei costi per visualizzare le variazioni nella fattura in seguito alle nuove selezioni

![Pannello di configurazione del pool elastico](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Gestire i pool di database elastici e i database tramite PowerShell

Per creare e gestire pool elastici del database SQL con Azure PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). Per creare e gestire i database, i server e le regole del firewall, vedere [Creare e gestire i server e i database di database SQL di Azure con PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell).

> [!TIP]
> Per gli script di esempio di PowerShell, vedere [Creare pool elastici e spostare i database tra i pool e al loro esterno usando PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) e [Usare PowerShell per il monitoraggio e il ridimensionamento di un pool elastico SQL nel database SQL di Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | DESCRIZIONE |
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

Per creare e gestire pool elastici del database SQL con l'[interfaccia della riga di comando di Azure](/cli/azure), usare i comandi seguenti del [database SQL per l'interfaccia della riga di comando di Azure](/cli/azure/sql/db). Usare [Cloud Shell](/azure/cloud-shell/overview) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows.

> [!TIP]
> Per gli script di esempio dell'interfaccia della riga di comando di Azure, vedere [Usare l'interfaccia della riga di comando per spostare un database SQL di Azure in un pool elastico SQL](scripts/sql-database-move-database-between-pools-cli.md) e [Usare l'interfaccia della riga di comando di Azure per ridimensionare un pool elastico SQL nel database SQL di Azure](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | DESCRIZIONE |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Consente di creare un pool elastico.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Restituisce un elenco di pool elastici in un server.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Restituisce un elenco di database in un pool elastico.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Include anche le impostazioni di DTU del pool disponibile, i limiti di archiviazione e per le impostazioni per ogni database. Al fine di ridurre il livello di dettaglio, i limiti di spazio di archiviazione aggiuntivo e le impostazioni per ogni database sono nascoste per impostazione predefinita.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Consente di aggiornare un pool elastico.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Consente di eliminare un pool elastico.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Gestire i database all'interno dei pool di database elastici con Transact-SQL

Per creare e spostare i database all'interno dei pool elastici esistenti o per restituire informazioni su un pool elastico del database SQL con Transact-SQL, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando il portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o qualsiasi altro programma che può connettersi a un server di database SQL di Azure e passare comandi Transact-SQL. Per creare e gestire i database, i server e le regole del firewall, vedere [Creare e gestire i server e i database di database SQL di Azure con Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Non è possibile creare, aggiornare o eliminare un pool elastico del database SQL di Azure con Transact-SQL. È possibile aggiungere o rimuovere i database da un pool elastico ed è possibile usare le DMV per restituire informazioni sui pool elastici esistenti.
>

| Comando | DESCRIZIONE |
| --- | --- |
|[CREATE DATABASE (database SQL di Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Consente di creare un nuovo database in un pool esistente o in un database singolo. Per creare un nuovo database è necessario essere connessi al database master.|
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Consente di spostare un database all'interno, all'esterno o tra pool elastici.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Questo comando elimina un database.|
|[sys.elastic_pool_resource_stats (Database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Restituisce statistiche di uso delle risorse per tutti i pool di database elastici in un server logico. Per ogni pool di database elastico c'è una riga per ogni finestra di report di 15 secondi, quattro righe per ogni minuto. Sono inclusi CPU, IO, Log, uso dell'archiviazione e uso di richieste/sessioni simultanee da parte di tutti i database nel pool.|
|[sys.database_service_objectives (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Restituisce l'edizione (livello di servizio), l'obiettivo di servizio (piano tariffario) e il nome del pool elastico, se presente, di un database SQL di Azure o un'istanza di Azure SQL Data Warehouse. Se si è connessi al database master in un server di database SQL di Azure, restituisce informazioni su tutti i database. Per Azure SQL Data Warehouse, è necessario essere connessi al database master.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Gestire i pool di database elastici e i database tramite l'API REST

Per creare e gestire i pool di database SQL elastici usare queste richieste API REST.

| Comando | DESCRIZIONE |
| --- | --- |
|[Pool di database elastici: crea o aggiorna](/rest/api/sql/elasticpools/createorupdate)|Crea un nuovo pool elastico o aggiorna un pool elastico esistente.|
|[Pool di database elastici: eliminare](/rest/api/sql/elasticpools/delete)|Consente di eliminare un pool elastico.|
|[Pool di database elastici: ottieni](/rest/api/sql/elasticpools/get)|Ottiene un pool elastico.|
|[Pool di database elastici: elenca dal server](/rest/api/sql/elasticpools/listbyserver)|Restituisce un elenco di pool elastici in un server.|
|[Pool di database elastici: aggiorna](/rest/api/sql/elasticpools/update)|Consente di aggiornare un pool elastico esistente.|
|[Pool di database elastici consigliati: ottieni](/rest/api/sql/recommendedelasticpools/get)|Consente di ottenere un pool elastico consigliato.|
|[Pool di database elastici consigliati: elenca dal server](/rest/api/sql/recommendedelasticpools/listbyserver)|Restituisce i pool di database elastici consigliati.|
|[Pool di database elastici consigliati: elenca metriche](/rest/api/sql/recommendedelasticpools/listmetrics)|Restituisce le metriche del pool elastico consigliato.|
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
