<properties
	pageTitle="Riferimento ai pool di database elastici per database SQL | Microsoft Azure" 
	description="In questo riferimento vengono forniti collegamenti e dettagli relativi agli articoli sui pool di database elastici e informazioni di programmabilità."
	keywords="eDTU"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Riferimento al pool di database elastici di database SQL

In questo riferimento vengono forniti collegamenti e dettagli relativi agli articoli sui pool di database elastici e informazioni di programmabilità. Per gli sviluppatori di SaaS che dispongono di decine, centinaia o persino di migliaia di database, un pool di database elastico semplifica il processo di creazione, manutenzione e gestione delle prestazioni e dei costi dell'intero gruppo di database.

Un pool di database elastici è una raccolta di unità di produttività di database elastici (eDTU) e di risorse di archiviazione (GB) che sono condivise da più database. I database elastici possono essere aggiunti al pool o rimossi da questo in qualsiasi momento. I database elastici nel pool utilizzano solo le risorse richieste dal pool, liberando le risorse disponibili solo per i database attivi che ne hanno bisogno. Per determinare se i database possono costituire un vantaggio in un pool di database elastici, vedere [Considerazioni di prezzo e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).



## Prerequisiti per la creazione e la gestione dei pool di database elastici


- I pool di database elastici sono disponibili unicamente nei server database SQL V12 di Azure. Per eseguire l'aggiornamento a V12 e la migrazione dei database direttamente in un pool, vedere [Eseguire l'aggiornamento a database SQL V12 di Azure](sql-database-upgrade-server-powershell.md).
- La creazione e la gestione di pool di database elastici è supportata tramite il [portale di Azure](https://portal.azure.com), [PowerShell](sql-database-elastic-pool-powershell.md) e la libreria client .NET (soltanto per Gestione risorse di Azure). Il [portale classico](https://manage.windowsazure.com/) e i comandi di gestione del servizio non sono supportati.
- [Transact-SQL](#transact-sql) supporta inoltre la creazione di nuovi database elastici e lo spostamento dei database esistenti da e verso i pool di database elastici.


## Elenco di articoli

Gli articoli seguenti consentiranno di iniziare a utilizzare database elastici e processi elastici:

| Articolo | Descrizione |
| :-- | :-- |
| [Pool di database elastici SQL](sql-database-elastic-pool.md) | Panoramica dei pool di database elastici |
| [Considerazioni su prezzi e prestazioni](sql-database-elastic-pool-guidance.md) | Come valutare se l’utilizzo di un pool di database elastici è redditizio |
| [Creare e gestire un database di pool elastici di database SQL con il portale di Azure](sql-database-elastic-pool-portal.md) | Come creare e gestire un pool di database elastici tramite il portale di Azure |
| [Creare e gestire un database di pool elastici di database SQL tramite PowerShell](sql-database-elastic-pool-powershell.md) | Come creare e gestire un pool di database elastici tramite i cmdlet PowerShell |
| [Creare e gestire un database SQL con la libreria di database SQL di Azure per .NET](sql-database-elastic-pool-powershell.md) | Creare e gestire un pool di database elastici utilizzando C# |
| [Panoramica dei processi dei database elastici](sql-database-elastic-jobs-overview.md) | Panoramica del servizio dei processi elastici, che consente l'esecuzione di script T-SQL in tutti i database elastici in un pool |
| [Installazione del componente del processo di database elastico](sql-database-elastic-jobs-service-installation.md) | Come installare il servizio del processo di database elastico |
| [Protezione del Database SQL](sql-database-security.md) | Per eseguire uno script del processo di database elastico, è necessario aggiungere un utente con le autorizzazioni appropriate a ogni database nel pool. |
| [Come disinstallare i componenti del processo di database elastico](sql-database-elastic-jobs-uninstall.md) | Recupero dagli errori quando si tenta di installare il servizio processo di database elastico |



## Dettagli dello spazio dei nomi e dell’endpoint
Un pool di database elastici è una risorsa Azure Resource Manager di tipo "ElasticPool" nel Database SQL di Microsoft Azure.

- **spazio dei nomi**: Microsoft.Sql/ElasticPool
- **gestione-endpoint** per le chiamate API REST (Gestione risorse): https://management.azure.com



## Proprietà pool di database elastico

| Proprietà | Descrizione |
| :-- | :-- |
| creationDate | Data di creazione del pool. |
| databaseDtuMax | Numero massimo di eDTU che può essere utilizzato da un singolo database nel pool. Il numero massimo di eDTU database non è una garanzia di risorse. Il numero massimo di eDTU si applica a tutti i database del pool. |
| databaseDtuMin | Numero minimo di eDTU garantito a un singolo database nel pool. Il numero minimo di eDTU database può essere impostato su 0. Il numero minimo di eDTU si applica a tutti i database nel pool. Si noti che il prodotto del numero di database nel pool e il valore minimo di eDTU database non possono superare le eDTU del pool stesso. |
| Dtu | Numero di eDTU condivise da tutti i database nel pool. |
| edition | Livello di servizio del pool. Ogni database all'interno del pool presenta lo stesso valore per edition. |
| elasticPoolId | GUID dell'istanza del pool. |
| elasticPoolName | Nome del pool. Il nome è univoco rispetto al server padre. |
| location | Posizione del data center in cui è stato creato il pool. |
| state | Lo stato è “Disabled” se il pagamento della fattura per la sottoscrizione è insolvente, ed è “Ready” in caso contrario. |
| storageMB | Limite di archiviazione in MB per il pool. La quantità totale di spazio di archiviazione usato da tutti i database nel pool non può superare questo limite del pool. |


## Limiti di archiviazione e di eDTU dei pool elastici e dei database elastici


[AZURE.INCLUDE [Tabella livelli di servizio di database SQL per database elastici](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]



## Limiti di Gestione risorse di Azure

Azure V12 del Database di SQL Server si trovano in gruppi di risorse.

- Ciascun gruppo di risorse può avere un massimo di 800 server.
- Ogni server può avere al massimo 800 pool elastici.


## Latenza delle operazioni dei pool elastici

- La modifica delle eDTU garantite per database (databaseDtuMin) o il numero massimo di eDTU per database (databaseDtuMax) in genere viene completata in 5 minuti o meno.
- La modifica del limite di eDTU/ risorse di archiviazione (storageMB) del pool dipende dalla quantità totale di spazio utilizzato da tutti i database nel pool. Le modifiche richiedono una media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale utilizzato da tutti i database nel pool è pari a 200 GB, la latenza prevista per la modifica del limite di eDTU / risorse di archiviazione è di 3 ore o meno.



## Libreria Client .NET, API REST e PowerShell

Molti dei cmdlet PowerShell e dei comandi dell’API REST sono disponibili per la creazione e la gestione dei pool elastici: Per informazioni dettagliate ed esempi di codice, vedere [Creare un pool di database elastici con PowerShell per la scalabilità orizzontale delle risorse per più database SQL](sql-database-elastic-pool-powershell.md) e [Creazione e gestione di database SQL con C#](sql-database-client-library.md).


| [Cmdlet PowerShell](https://msdn.microsoft.com/library/azure/mt574084.aspx) | [Comandi API REST](https://msdn.microsoft.com/library/mt163571.aspx) |
| :-- | :-- |
| [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) | [Creare un pool di database elastici](https://msdn.microsoft.com/library/mt163596.aspx) |
| [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) | [Modificare le impostazioni delle prestazioni di un pool di database elastici](https://msdn.microsoft.com/library/mt163641.aspx) |
| [Remove-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619355.aspx) | [Eliminare un pool di database elastici](https://msdn.microsoft.com/library/mt163672.aspx) |
| [Get-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603517.aspx) | [Ottiene i pool di database elastici e i relativi valori di proprietà](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) | [Ottenere lo stato delle operazioni del pool di database elastici](https://msdn.microsoft.com/library/mt163669.aspx) |
| [Get-AzureRmSqlElasticPoolDatabase](https://msdn.microsoft.com/library/azure/mt619484.aspx) | [Ottenere i database in un pool di database elastici](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolDatabaseActivity]() | [Ottiene lo stato di spostamento dei database da e verso un pool](https://msdn.microsoft.com/library/mt163669.aspx) |

## Transact-SQL

È possibile utilizzare Transact-SQL per eseguire le seguenti attività di gestione del database elastico:

| Attività | Dettagli |
| :-- | :-- |
| Creare un nuovo database elastico (direttamente in un pool): | [CREARE DATABASE (database SQL di Azure)](https://msdn.microsoft.com/library/dn268335.aspx) |
| Spostare i database esistenti da e verso un pool | [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms174269.aspx) |
| Ottenere le statistiche di utilizzo delle risorse del pool | [sys.elastic\_pool\_resource\_stats (Database SQL di Azure)](https://msdn.microsoft.com/library/mt280062.aspx) |


## Informazioni sui prezzi e sulla fatturazione

I pool di database elastici vengono fatturati in base alle caratteristiche seguenti:

- Un pool elastico viene fatturato al momento della creazione, persino quando nel pool non sono presenti database.
- Un pool elastico viene fatturato su base oraria. Si tratta della stessa frequenza di controllo dei livelli di prestazioni dei database singoli.
- Se un pool elastico viene ridimensionato per una nuova quantità di eDTU, non viene fatturato in base alla nuova quantità di eDTU fino al completamento dell'operazione di ridimensionamento. Tale comportamento segue lo stesso modello della modifica del livello di prestazioni dei database autonomi.


- Il prezzo di un pool elastico è basato sul numero di eDTU del pool. Il prezzo di un pool elastico è indipendente dall'utilizzo dei database elastici in esso contenuti.
- Il prezzo di base viene calcolato da (numero di pool eDTUs) x (prezzo unitario per eDTU).

Il prezzo unitario delle eDTU per un pool elastico è superiore al prezzo unitario delle eDTU per un database autonomo nello stesso livello di servizio. Per ulteriori informazioni, vedere [Database SQL Prezzi](https://azure.microsoft.com/pricing/details/sql-database/).

## Errori dei pool di database elastici

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX\_RESOURCE | Il pool elastico ha raggiunto il limite di archiviazione. L'utilizzo dell'archiviazione per il pool elastico non può superare (%d) MB. | Limite di spazio del pool elastico in MB. | Tentativo di scrittura dei dati in un database quando viene raggiunto il limite di archiviazione del pool elastico. | Prendere in considerazione l’aumento delle DTU del pool elastico, se possibile, per aumentare il limite di archiviazione, ridurre la memoria utilizzata dai singoli database all'interno del pool elastico o rimuovere i database dal pool elastico. |
| 10929 | EX\_USER | La %s di garanzia minima è %d, il limite massimo è %d e l'utilizzo corrente per il database è %d. Tuttavia, il server attualmente è troppo occupato per supportare richieste superiori a %d per questo database. Per assistenza, vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Altrimenti, riprovare più tardi. | Numero minimo DTU per database. Numero massimo DTU per database | Il numero totale dei processi di lavoro simultanei (richieste) in tutti i database nel pool elastico ha tentato di superare il limite del pool. | Prendere in considerazione l’aumento delle DTU del pool elastico, se possibile, per aumentare il limite dei processi di lavoro o rimuovere i database dal pool elastico. |
| 40844 | EX\_USER | Il database '%ls' sul Server '%ls' è un database versione '%ls' in un pool elastico e non può avere una relazione di copia continua. | nome database, versione database, nome server | Viene emesso un comando StartDatabaseCopy per un database non premium in un pool elastico. | Presto disponibile |
| 40857 | EX\_USER | Pool elastico non trovato per il server: '%ls', nome del pool elastico: '%ls'. | nome del server; nome del pool elastico | Il pool elastico specificato non esiste nel server specificato. | Fornire un nome pool elastico valido. |
| 40858 | EX\_USER | Il pool elastico '%ls' esiste già nel server: '%ls' | nome del pool elastico, nome del server | Il pool elastico specificato esiste già nel server logico specificato. | Fornire un nuovo nome pool elastico. |
| 40859 | EX\_USER | Il pool elastico non supporta il livello di servizio '%ls'. | livello di servizio del pool elastico | Il livello di servizio specificato non è supportato per il provisioning del pool elastico. | Fornire l'edizione corretta oppure lasciare vuoto il livello di servizio per utilizzare il livello di servizio predefinito. |
| 40860 | EX\_USER | La combinazione di pool elastico '%ls' e di obiettivo di servizio '%ls' non è valida. | nome pool elastico; nome obiettivo del livello di servizio | Il pool elastico e l’obiettivo del servizio possono essere specificati insieme solo se l’obiettivo di servizio viene specificato come 'ElasticPool'. | Specificare la combinazione corretta di pool elastico e obiettivo di servizio. |
| 40861 | EX\_USER | L'edizione del database '%.*ls' non può essere diversa dal livello di servizio del pool elastico che è '%.*ls'. | edizione del database, livello di servizio del pool elastico | L'edizione del database è diversa dal livello di servizio del pool elastico. | Non specificare un'edizione del database diversa dal livello di servizio del pool elastico. Si noti che non è necessario specificare l'edizione del database. | | 40862 | EX\_USER | Nome del pool elastico deve essere specificato quando viene specificato l'obiettivo del servizio del pool elastico. | Nessuno | L’obiettivo del servizio del pool elastico non identifica in modo univoco un pool elastico. | Specificare il nome del pool elastico se si utilizza l'obiettivo del servizio del pool elastico. | | 40864 | EX\_USER | Il numero di DTU per il pool elastico deve essere almeno (%d) DTU per il livello di servizio '%.*ls'. | DTU per il pool elastico; livello di servizio del pool elastico. | Tentativo di impostazione di un numero di DTU per il pool elastico inferiore al limite minimo. | Riprovare impostando le DTU per il pool elastico almeno al limite minimo. | | 40865 | EX\_USER | Le DTU per il pool elastico non possono superare (%d) DTU per il livello di servizio '%.*ls'. | DTU per il pool elastico; livello di servizio del pool elastico. | Tentativo di impostazione di un numero di DTU per il pool elastico superiore al limite massimo. | Riprovare impostando un numero di DTU per il pool elastico non superiore al limite massimo. | | 40867 | EX\_USER | Il numero massimo di DTU per ogni database deve essere di almeno (%d) per il livello di servizio '%.*ls'. | Numero massimo di DTU per database; livello di servizio del pool elastico | Tentativo di impostare un numero massimo di DTU per ogni database sotto il limite supportato. | È consigliabile utilizzare il livello di servizio del pool elastico che supporta l'impostazione desiderata. | | 40868 | EX\_USER | Il numero massimo di DTU per ogni database non può superare (%d) per il livello di servizio '%.*ls'. | Numero massimo di DTU per database; livello di servizio del pool elastico. | Tentativo di impostare un numero massimo di DTU per ogni database oltre il limite supportato. | È consigliabile utilizzare il livello di servizio del pool elastico che supporta l'impostazione desiderata. | | 40870 | EX\_USER | Il numero minimo di DTU per ogni database non può superare (%d) per il livello di servizio '%.*ls'. | Numero minimo di DTU per database; livello di servizio del pool elastico. | Tentativo di impostare un numero minimo di DTU per ogni database oltre il limite supportato. | È consigliabile utilizzare il livello di servizio del pool elastico che supporta l'impostazione desiderata. | | 40873 | EX\_USER | Il numero di database (%d) e DTU min per ogni database (%d) non può superare le DTU del pool elastico (%d). | Numero di database nel pool elastico; DTU minime per database; DTU del pool elastico. | Tentativo di specificare un numero minimo di DTU per i database nel pool elastico che supera le DTU del pool elastico. | Provare ad aumentare le DTU del pool elastico o a diminuire il numero minimo di DTU per ogni database oppure ridurre il numero di database nel pool elastico. | | 40877 | EX\_USER | Impossibile eliminare un pool elastico, a meno che non contenga alcun database. | Nessuno | Il pool elastico contiene uno o più database e pertanto non può essere eliminato. | Rimuovere i database dal pool elastico per eliminarlo. | | 40881 | EX\_USER | Il pool elastico '%.* ls' ha raggiunto il numero massimo di database. Il numero massimo di database per il pool elastico non può superare (%d) per un pool elastico con DTU (%d). | Nome del pool elastico; numero massimo di database di pool elastico; DTU per pool di risorse. | Tentativo di creare o aggiungere database al pool elastico quando viene raggiunto il numero massimo di database nel pool elastico. | Provare ad aumentare le DTU del pool elastico se possibile, per aumentare il limite di database o rimuovere i database dal pool elastico. | | 40889 | EX\_USER | Il limite di DTU o risorse di archiviazione per il pool elastico '%.*ls' non può essere ridotto dal momento che non si fornisce spazio di archiviazione sufficiente per i database. | Nome del pool elastico. | Tentativo di ridurre il limite di archiviazione del pool elastico sotto il relativo utilizzo dell'archiviazione. | Provare a ridurre l'utilizzo dell'archiviazione dei singoli database nel pool elastico o rimuovere i database dal pool per ridurre le DTU o il limite di archiviazione. | | 40891 | EX\_USER | Il numero minimo di DTU per ogni database (%d) non può superare il numero massimo di DTU per ogni database (%d). | Numero minimo di DTU per database; numero massimo di DTU per database. | Tentativo di impostare un numero minimo di DTU per ogni database superiore al numero massimo di DTU per ogni database. | Verificare che il numero minimo di DTU per database non superi il numero massimo di DTU per database. | | TBD | EX\_USER | Le dimensioni di archiviazione di un singolo database in un pool elastico non possono superare la dimensione massima consentita per il livello di servizio del pool elastico '%.* ls'. | Livello di servizio del pool elastico | La dimensione massima per il database supera la dimensione massima consentita per il livello di servizio del pool elastico. | Impostare la dimensione massima del database entro i limiti della dimensione massima consentita per il livello di servizio del pool elastico. |

<!---HONumber=AcomDC_0204_2016-->