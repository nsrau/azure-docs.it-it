---
title: Schede di riferimento rapido Azure SQL Data Warehouse | Documenti Microsoft
description: Collegamenti, procedure consigliate per compilare rapidamente soluzioni di Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: dc55f4333bef1c609978887f293539bbee8b1c29
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Schede di riferimento rapido per Azure SQL Data Warehouse
Questa pagina consentono di progettare per l'utilizzo principale casi la soluzione di data warehouse. Questo foglio informativo deve essere un supporto ottimale in consentirti di creare un livello mondiale di data warehouse, ma è consigliabile ottenere ulteriori informazioni su ogni passaggio nei dettagli. In primo luogo, si consiglia di leggere questo articolo grande sulle quali SQL Data Warehouse  **[e non]**.

Di seguito è un sketch del processo di cui che è necessario seguire quando si inizia a Progettazione SQL Data Warehouse.

![Sketch]

## <a name="queries-and-operations-across-tables"></a>Query e le operazioni tra le tabelle

È veramente importante sapere in anticipo le operazioni più importanti e le query eseguite nel data warehouse. L'architettura di data warehouse deve avere una priorità per tali operazioni. Esempi comuni di operazioni può essere:
* Unione di uno o due tabelle dei fatti con dimensioni di tabelle, il filtro di questa tabella per un periodo di tempo e aggiungere i risultati in un data mart
* Gli aggiornamenti di grandi o piccoli nelle vendite dei fatti
* L'accodamento dei dati solo per le tabelle

Conoscere il tipo di operazioni consente di ottimizzare la progettazione delle tabelle.

## <a name="data-migration"></a>Migrazione dei dati

È consigliabile caricare prima i dati  **[in ADLS]**  o archiviazione Blob di Azure. Quindi, si deve usare Polybase per caricare i dati in SQL Data Warehouse in una tabella di gestione temporanea. Si consiglia la seguente configurazione:

| Progettazione | Raccomandazione |
|:--- |:--- |
| Distribuzione | Round robin |
| Indicizzazione | Heap |
| Partizionamento | Nessuna |
| Classe di risorse | largerc o xlargerc |

Altre informazioni, vedere  **[la migrazione dei dati], [il caricamento dei dati]**  con  **[indicazioni più approfondita] caricamento**. 

## <a name="distributed-or-replicated-tables"></a>Tabelle replicate o distribuite

È consigliabile le strategie seguenti a seconda delle proprietà di tabella:

| type | Ideale per | Controllare se...|
|:--- |:--- |:--- |
| Replicato | • Le tabelle di dimensioni ridotte in uno schema star con meno di 2 GB di spazio di archiviazione dopo la compressione (compressione x ~ 5) |• Molte transazioni di scrittura sulla tabella (ad esempio: insert, upsert, delete, update)<br></br>• Modificare provisioning spesso dati Warehouse unità (DWU)<br></br>• Utilizzare solo 2-3 colonne e la tabella contiene molte colonne<br></br>• Per indicizzare una tabella replicata |
| Round Robin (impostazione predefinita) | Tabella di gestione temporanea/temporaneo •<br></br> • Non è ovvio aggiunta colonna chiave o buon candidato |• Un rallentamento delle prestazioni a causa lo spostamento dei dati |
| Hash | • Le tabelle dei fatti<br></br>• Le tabelle di grandi dimensioni |• Non è possibile aggiornare la chiave di distribuzione |

**Suggerimenti:**
* Iniziare con Round Robin ma mirare per una strategia di distribuzione hash possa sfruttare i vantaggi di un'architettura parallela Massive
* Assicurarsi che le chiavi di hash comune abbiano lo stesso formato di dati
* Non distribuire sul formato varchar
* Tabelle delle dimensioni con chiave hash comune in una tabella dei fatti con le operazioni di join frequenti può essere distribuita hash
* Utilizzare  *[sys.dm_pdw_nodes_db_partition_stats]*  per analizzare qualsiasi asimmetria nei dati
* Utilizzare  *[sys.dm_pdw_request_steps]*  per analizzare spostamenti dei dati protetti da query, monitorare la trasmissione di tempo e richiedono operazioni casuale. Utile per esaminare la strategia di distribuzione.

Altre informazioni, vedere  **[tabelle replicate al fine] e [distributed tabelle]**.

## <a name="indexing-your-table"></a>L'indicizzazione della tabella

L'indicizzazione è **grande** per leggere rapidamente le tabelle. È un set univoco di tecnologie, che è possibile utilizzare in base alle proprie esigenze:

| type | Ideale per | Controllare se...|
|:--- |:--- |:--- |
| Heap | Tabella di gestione temporanea o di temporanea •<br></br>• Tabelle di piccole dimensioni con ricerche di piccole dimensioni |• Qualsiasi ricerca analizza la tabella completa |
| Indice cluster | • Fino a tabella m-100 righe<br></br>• Tabelle di grandi dimensioni (oltre 100-m righe) con solo le colonne 1-2 vengono utilizzate. |• Utilizzato in una tabella replicata<br></br>• Query complesse che includono più operazioni di Join, Group By<br></br>• Aggiornare nelle colonne indicizzate, richiede memoria |
| Indice Columnstore cluster (CCI) (impostazione predefinita) | • Tabelle di grandi dimensioni (righe di più di 100-m) | • Utilizzato in una tabella replicata<br></br>• Apportate massive Aggiorna le operazioni in una tabella<br></br>• Eccessiva partizionare la tabella: gruppi di righe si estendono su più tra i nodi di distribuzione diverso e partizioni |

**Suggerimenti:**
* All'inizio di un indice cluster, si potrebbe voler aggiungere l'indice non cluster a una colonna utilizzata per il filtro. 
* Prestare attenzione a come gestire la memoria in una tabella con indice ColumnStore cluster. Quando si caricano dati, è opportuno l'utente o la query per trarre vantaggio da una classe di risorse di grandi dimensioni. Assicurarsi che evitare la rimozione e la creazione di molti gruppi di piccole dimensioni di riga compresso
* Ottimizzato per livello di calcolo Massi con indice ColumnStore cluster
* Per CCI, può verificarsi un rallentamento delle prestazioni a causa della compressione di una riduzione dei gruppi di righe, si potrebbe voler ricompilare o riorganizzare l'indice ColumnStore cluster. È necessario almeno 100 righe di k per gruppi di righe compresso. La soluzione ideale è 1-m righe in un gruppo di righe.
* In base la frequenza di caricamento incrementale e le dimensioni, di cui si desidera automatizzare quando si riorganizzare o ricompilare gli indici. Pulizia di primavera è sempre utile.
* Essere strategico quando si desidera rimuovere un gruppo di righe: le dimensioni sono i gruppi di righe aperti? La quantità di dati si intende caricare nei prossimi giorni?

Altre informazioni, vedere  **[indici]**.

## <a name="partitioning"></a>Partizionamento
È possibile partizionare la tabella quando si dispongono di un tabelle dei fatti di grandi dimensioni (> tabella riga 1B). 99% dei casi, la chiave di partizione deve essere basata sul data. Prestare attenzione a non in eccesso partizione, specialmente quando si dispone di un indice Columnstore cluster.
Con le tabelle che richiedono ETL di gestione temporanea, è possibile trarre vantaggio dal partizionamento. Facilita la gestione del ciclo di vita dei dati.
Prestare attenzione a non overpartition i dati, in particolare in un indice Columnstore cluster.

Altre informazioni, vedere  **[partizioni]**.

## <a name="incremental-load"></a>Caricamento incrementale

Innanzitutto, assicurarsi di allocare le classi di risorse maggiore per il caricamento dei dati. È consigliabile usare Polybase e V2 ADF per automatizzare le pipeline di ETL nel data Warehouse di SQL.

Per un batch di grandi dimensioni degli aggiornamenti dei dati cronologici, è consigliabile eliminare innanzitutto i dati in questione. È quindi possibile creare un blocco inserimento dei nuovi dati. Questo approccio passaggio 2 è più efficiente.

## <a name="maintain-statistics"></a>Gestire le statistiche
Automatico delle statistiche sta per essere in genere disponibile a breve. Fino ad allora, SQL Data Warehouse richiede alcuna manutenzione manuale delle statistiche. È importante aggiornare le statistiche come **significativo** verificarsi modifiche ai dati. Ciò consente di ottimizzare i piani di query. Se si ritiene che impiega troppo tempo per gestire tutte le statistiche, si desidera in maggiore dettaglio le colonne con statistiche. È inoltre possibile definire la frequenza degli aggiornamenti. Potrebbe ad esempio essere consigliabile aggiornare le colonne di data, in cui potrebbero venire aggiunti nuovi valori ogni giorno. Il massimo vantaggio si ottengono con le statistiche su colonne coinvolte nel join, le colonne utilizzate nella clausola WHERE e colonne in GROUP BY.

Altre informazioni, vedere  **[statistiche]**.

## <a name="resource-class"></a>classe di risorse
SQL Data Warehouse usa i gruppi di risorse per allocare la memoria per le query. Se si richiedono più memoria per migliorare le velocità di caricamento o query, è consigliabile allocare più classi di risorse. Sul lato capovolto, utilizzando le classi di risorse maggiore influisce sulla concorrenza. Si desidera, prendere in considerazione prima di spostare tutti gli utenti a una classe di risorse di grandi dimensioni.

Se si nota che le query richiedono troppo tempo, verificare che gli utenti non venga eseguano in classi di risorse di grandi dimensioni. Classi di risorse di grandi dimensioni utilizzano molte gli slot di concorrenza. È possibile che altre query per mettere in coda.

Infine se si utilizza il livello di calcolo ottimizzato, ogni classe di risorse ottiene 2.5 x più memoria rispetto a nel livello elastico ottimizzato.

Altre informazioni su come lavorare con  **[classi di risorse e la concorrenza]**.

## <a name="lower-your-cost"></a>Ridurre i costi
Una funzionalità chiave di SQL Data Warehouse è la possibilità di sospendere il servizio quando non è in uso, interrompendo così la fatturazione delle risorse di calcolo. Un'altra funzionalità fondamentale è la possibilità di ridimensionare le risorse. È possibile sospendere e ridimensionare il servizio dal portale di Azure o usando i comandi di PowerShell.

La scalabilità automatica ora al momento desiderato con le funzioni di Azure:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-you-architecture-for-performance"></a>Ottimizzare l'architettura per le prestazioni

È consigliabile prendere in considerazione il database SQL e Azure Analysis Services in un'architettura di Hub e spoke. Tale soluzione può fornire l'isolamento del carico di lavoro tra gruppi di utenti diversi, sebbene anche l'utilizzo di alcune funzionalità di protezione dal database di SQL e Azure Analysis Services. Questo è un modo per fornire concorrenza illimitata agli utenti.

Altre informazioni, vedere  **[tipico architetture sfruttando SQL DW]**.

Distribuire un clic del spoke nei database di SQL Server di database dal data Warehouse di SQL:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Sketch]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[il caricamento dei dati]:./design-elt-data-loading.md
[indicazioni più approfondita]: ./guidance-for-loading-data.md
[indici]:./sql-data-warehouse-tables-index.md
[partizioni]:./sql-data-warehouse-tables-partition.md
[statistiche]:./sql-data-warehouse-tables-statistics.md
[classi di risorse e la concorrenza]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[tipico architetture sfruttando SQL DW]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[e non]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[la migrazione dei dati]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[tabelle replicate al fine]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[distributed tabelle]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[in ADLS]: https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
