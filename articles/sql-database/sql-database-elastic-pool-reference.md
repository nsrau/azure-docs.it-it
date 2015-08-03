<properties 
	pageTitle="Riferimento al pool database elastico di SQL Azure" 
	description="In questo riferimento vengono forniti collegamenti e dettagli relativi agli articoli sui pool di database elastici e informazioni di programmabilità." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/14/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Riferimento al pool di database elastico di database SQL (anteprima)

Per gli sviluppatori di SaaS che dispongono di decine, centinaia o persino di migliaia di database, un pool di database elastico semplifica il processo di creazione, manutenzione e gestione delle prestazioni e dei costi dell'intero gruppo di database.

In questo riferimento vengono forniti collegamenti e dettagli relativi agli articoli sui pool di database elastici e informazioni di programmabilità.

## Panoramica

Un pool di database elastici è una raccolta di unità di database (DTU) e di risorse di archiviazione (GB) che sono condivise da più database. I database elastici possono essere aggiunti al pool o rimossi da questo in qualsiasi momento. I database elastici nel pool utilizzano solo le risorse richieste dal pool, liberando le risorse disponibili solo per i database attivi che ne hanno bisogno. Per determinare se i database possono costituire un vantaggio in un pool di database elastici, vedere [Considerazioni di prezzo e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).



## Prerequisiti per la creazione e la gestione dei pool di database elastici


- I pool di database elastici sono disponibili unicamente nei server database SQL V12 di Azure.   
- La creazione e gestione di pool di database elastici è supportata tramite il [portale di Microsoft Azure](https://portal.azure.com) PowerShell e API REST in Azure Resource Manager (ARM), solo il [portale classico](https://manage.windowsazure.com/) e comandi di gestione del servizio (RDFE) non sono supportati. 


## Restrizioni per l'anteprima corrente

- Il livello di prezzo per un database di pool elastici nell'anteprima corrente è Standard.  
- L’importazione di un database direttamente in un pool elastico non è supportata. È possibile eseguire l’importazione in un database autonomo e quindi spostare il database in un pool. L’esportazione di un database dall’interno di un pool è supportata.
- Ogni pool può avere un massimo di 100 database.


## Elenco di articoli

Gli articoli seguenti consentiranno di iniziare a utilizzare database elastici e processi elastici:

| Articolo | Descrizione |
| :-- | :-- |
| [Pool di database elastici SQL](sql-database-elastic-pool.md) | Panoramica dei pool elastici |
| [Creare e gestire un database di pool elastici di database SQL con il portale di Azure](sql-database-elastic-pool-portal.md) | Come creare e gestire un pool elastico tramite il portale di Azure |
| [Creare e gestire un database di pool elastici di database SQL tramite PowerShell](sql-database-elastic-pool-powershell.md) | Come creare e gestire un pool elastico tramite i cmdlet PowerShell |
| [Panoramica dei processi dei database elastici](sql-database-elastic-jobs-overview.md) | Panoramica del servizio dei processi elastici, che consente l'esecuzione di script T-SQL in tutti i database elastici in un pool |
| [Installazione del componente del processo di database elastico](sql-database-elastic-jobs-service-installation.md) | Come installare il servizio del processo di database elastico |
| [Creazione dell'utente richiesto per il servizio dei processi elastici](sql-database-elastic-jobs-add-logins-to-dbs.md) | Per eseguire uno script del processo di database elastico, è necessario aggiungere un utente con le autorizzazioni appropriate a ogni database nel pool. |
| [Come disinstallare i componenti del processo di database elastico](sql-database-elastic-jobs-uninstall.md) | Recupero dagli errori quando si tenta di installare il servizio processo di database elastico |



## Dettagli dello spazio dei nomi e dell’endpoint
Un pool elastico è una risorsa ARM di tipo "ElasticPool" nel Database SQL di Microsoft Azure.

- **spazio dei nomi**: Microsoft.Sql/ElasticPool
- **endpoint secondario** per le chiamate API REST (Gestione risorse di Azure): https://management.azure.com



## Proprietà pool di database elastico

| Proprietà | Descrizione |
| :-- | :-- |
| creationDate | Data di creazione del pool. |
| databaseDtuMax | Numero massimo di DTU che può essere utilizzato da un singolo database nel pool. Il numero massimo di DTU database non è una garanzia di risorse. Il numero massimo di DTU si applica a tutti i database del pool. |
| databaseDtuMin | Numero minimo di DTU garantito a un singolo database nel pool. Il numero minimo di DTU database può essere impostato su 0. Il numero minimo di DTU si applica a tutti i database nel pool. Si noti che il prodotto del numero di database nel pool e il valore minimo di DTU database non può superare le DTU del pool stesso. |
| Dtu | Numero di DTU condivise da tutti i database nel pool. |
| edition | Livello di servizio del pool. Ogni database all'interno del pool presenta lo stesso valore per edition. |
| elasticPoolId | GUID dell'istanza del pool. |
| elasticPoolName | Nome del pool. Il nome è univoco rispetto al server padre. |
| location | Posizione del data center in cui è stato creato il pool. |
| state | Lo stato è “Disabled” se il pagamento della fattura per la sottoscrizione è insolvente, ed è “Ready” in caso contrario. |
| storageMB | Limite di archiviazione in MB per il pool. Ogni singolo database nel pool può utilizzare fino al limite di archiviazione Standard Edition (250 GB), ma lo spazio di archiviazione totale utilizzato da tutti i database nel pool non può superare questo limite del pool. |


## Limiti di archiviazione e di DTU dei pool elastici e dei database elastici

Il limite di archiviazione del pool è determinato dalla quantità di DTU del pool. Ogni DTU = 1 GB di archiviazione. Ad esempio, un pool di DTU 200 ha un limite di archiviazione di 200 GB.

| proprietà | valore predefinito | valori validi |
| :-- | :-- | :-- |
| Dtu | 100 | 100, 200, 400, 800, 1200 |
| databaseDtuMax | 100 | 10, 20, 50 100 |
| databaseDtuMin | 0 | 0, 10, 20, 50 |
| storageMB | 100 GB* | 100 GB, 200 GB, 400 GB, 800 GB, 1200 GB |

*le unità nell'API sono MB, non GB

## Limiti di sessione e di lavoro

Il numero massimo di thread di lavoro simultanei e di sessioni simultanee supportato per tutti i database in un pool elastico dipende dall'impostazione DTU per il pool:

| DTU | Numero massimo di thread di lavoro simultanei | Numero massimo di sessioni simultanee |
| :-- | :-- | :-- |
| 100 | 200 | 2.400 |
| 200 | 400 | 4.800 |
| 400 | 800 | 9.600 |
| 800 | 1.600 | 19.200 |
| 1.200 | 2.400 | 28.800 |


## Limiti di Gestione risorse di Azure

Azure V12 del Database di SQL Server si trovano in gruppi di risorse.

- Ciascun gruppo di risorse può avere un massimo di 800 server.
- Ogni server può avere al massimo 800 pool elastici.


## Latenza delle operazioni dei pool elastici

- La modifica delle DTU garantite per database (databaseDtuMin) o il numero massimo di DTU per database (databaseDtuMax) in genere viene completata in 5 minuti o meno. 
- La modifica del limite di DTU/ risorse di archiviazione (storageMB) del pool di dipende dalla quantità totale di spazio utilizzato da tutti i database nel pool. Le modifiche richiedono una media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale utilizzato da tutti i database nel pool è pari a 200 GB, la latenza prevista per la modifica del limite di DTU / risorse di archiviazione è di 3 ore o meno. 



## Cmdlet PowerShell dei pool di database elastici e i comandi dell’API REST (solo Gestione risorse di Azure)

I cmdlet PowerShell e i comandi dell’API REST seguenti sono disponibili per la creazione e la gestione dei pool elastici:

| [Cmdlet PowerShell](https://msdn.microsoft.com/library/mt125356.aspx) | [Comandi API REST](https://msdn.microsoft.com/library/azure/mt163571.aspx) |
| :-- | :-- |
| Get-AzureSqlDatabase | Get Azure SQL database |
| Get-AzureSqLElasticPool | Get Azure SQL Database elastic database pool |
| Get-AzureSqlElasticPoolActivity | Get Azure SQL Database elastic database pool operations |
| Get-AzureSqlElasticPoolDatabase | Get Azure SQL Database elastic database |
| Get-AzureSqlElasticPoolDatabaseActivity | Get Azure SQL Database elastic database operations |
| Get-AzureSqlServer | Get Azure SQL Database server |
| Get-AzureSqlServerFirewallRule | Get Azure SQL Database server firewall rule |
| Get-AzureSqlServerServiceObjective | Get Azure SQL Database server service objective |
| New-AzureSqlDatabase | Create Azure SQL database |
| New-AzureSqlElasticPool | Create Azure SQL Database elastic database pool |
| New-AzureSqlServer | Create Azure SQL Database server |
| New-AzureSqlServerFirewallRule | Create Azure SQL Database server firewall rule) |
| Remove-AzureSqlDatabase | Remove Azure SQL database |
| Remove-AzureSqlElasticPool | Remove Azure SQL Database elastic database pool |
| Remove-AzureSqlServer | Remove Azure SQL Database server |
| Set-AzureSqlDatabase | Set Azure SQL database |
| Set-AzureSqlElasticPool | Set Azure SQL Database elastic database pool |
| Set-AzureSqlServer | Set Azure SQL Database server |
| Set-AzureSqlServerFirewallRule | Set Azure SQL Database server firewall rule |
| Get-Metrics | Get Metrics |


## Informazioni sui prezzi e sulla fatturazione

I pool di database elastici vengono fatturati in base alle caratteristiche seguenti:

- Un pool elastico viene fatturato al momento della creazione, persino quando nel pool non sono presenti database. 
- Un pool elastico viene fatturato su base oraria. Si tratta della stessa frequenza di controllo dei livelli di prestazioni dei database autonomi. 
- Se un pool elastico viene ridimensionato per un nuovo intervallo di DTU, non viene fatturato in base alla nuova quantità di DTU fino al completamento dell'operazione di ridimensionamento. Tale comportamento segue lo stesso modello della modifica del livello di prestazioni dei database autonomi. 


- Il prezzo di un pool elastico è basato sul numero di DTU del pool e sul numero di database del pool.
- Il prezzo viene calcolato nel modo seguente: (numero di DTU del pool)x(prezzo unitario per DTU) + (numero di database)x(prezzo unitario per database)

Il prezzo unitario delle DTU per un pool elastico è superiore al prezzo unitario delle DTU per un database autonomo nello stesso livello di servizio. Per ulteriori informazioni, vedere [Database SQL Prezzi](http://azure.microsoft.com/pricing/details/sql-database/).

## Errori dei pool di database elastici

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | Il pool elastico ha raggiunto il limite di archiviazione. L'utilizzo dell'archiviazione per il pool elastico non può superare (%d) MB. | Limite di spazio del pool elastico in MB. | Tentativo di scrittura dei dati in un database quando viene raggiunto il limite di archiviazione del pool elastico. | Prendere in considerazione l’aumento delle DTU del pool elastico, se possibile, per aumentare il limite di archiviazione, ridurre la memoria utilizzata dai singoli database all'interno del pool elastico o rimuovere i database dal pool elastico. |
| 10929 | EX_USER | La %s di garanzia minima è %d, il limite massimo è %d e l'utilizzo corrente per il database è %d. Tuttavia, il server attualmente è troppo occupato per supportare richieste superiori a %d per questo database. Per assistenza, vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Altrimenti, riprovare più tardi. | Numero minimo DTU per database. Numero massimo DTU per database | Il numero totale dei processi di lavoro simultanei (richieste) in tutti i database nel pool elastico ha tentato di superare il limite del pool. | Prendere in considerazione l’aumento delle DTU del pool elastico, se possibile, per aumentare il limite dei processi di lavoro o rimuovere i database dal pool elastico. |
| 40844 | EX_USER | Il database '%ls' sul Server '%ls' è un database versione '%ls' in un pool elastico e non può avere una relazione di copia continua. | nome database, versione database, nome server | Viene emesso un comando StartDatabaseCopy per un database non premium in un pool elastico. | Presto disponibile |
| 40857 | EX_USER | Pool elastico non trovato per il server: '%ls', nome del pool elastico: '%ls'. | nome del server; nome del pool elastico | Il pool elastico specificato non esiste nel server specificato. | Fornire un nome pool elastico valido. |
| 40858 | EX_USER | Il pool elastico '%ls' esiste già nel server: '%ls' | nome del pool elastico, nome del server | Il pool elastico specificato esiste già nel server logico specificato. | Fornire un nuovo nome pool elastico. |
| 40859 | EX_USER | Il pool elastico non supporta il livello di servizio '%ls'. | livello di servizio del pool elastico | Il livello di servizio specificato non è supportato per il provisioning del pool elastico. | Fornire l'edizione corretta oppure lasciare vuoto il livello di servizio per utilizzare il livello di servizio predefinito. |
| 40860 | EX_USER | La combinazione di pool elastico '%ls' e di obiettivo di servizio '%ls' non è valida. | nome pool elastico; nome obiettivo del livello di servizio | Il pool elastico e l’obiettivo del servizio possono essere specificati insieme solo se l’obiettivo di servizio viene specificato come 'ElasticPool'. | Specificare la combinazione corretta di pool elastico e obiettivo di servizio. |
| 40861 | EX_USER | L’edizione del database '%. * ls' non può essere diversa dal livello di servizio del pool elastico, che è '%.*ls'. | edizione del database, livello di servizio del pool elastico | L'edizione del database è diversa dal livello di servizio del pool elastico. | Non specificare un’edizione di database diversa dal livello di servizio del pool elastico. Si noti che non è necessario specificare l'edizione del database. |
| 40862 | EX_USER | Il nome del pool elastico deve essere specificato se viene specificato l'obiettivo di servizio del pool elastico. | Nessuno | L’obiettivo di servizio del pool elastico non identifica in modo univoco un pool elastico. | Specificare il nome del pool elastico se si utilizza l'obiettivo di servizio del pool elastico. |
| 40864 | EX_USER | Le DTU per il pool elastico devono essere almeno (%d) DTU per il livello di servizio '%.*ls'. | DTU per il pool elastico; livello di servizio del pool elastico. | Tentativo di impostare le DTU per il pool elastico al di sotto del limite minimo. | Riprovare a impostare le DTU per il pool elastico almeno al limite minimo. |
| 40865 | EX_USER | Le DTU per il pool elastico non possono superare (%d) DTU per il livello di servizio '%.*ls'. | DTU per il pool elastico; livello di servizio del pool elastico. | Tentativo di impostare le DTU per il pool elastico al di sopra del limite massimo. | Riprovare a impostare le DTU per il pool elastico non oltre il limite massimo. |
| 40867 | EX_USER | Il valore di DTU max per database deve essere almeno (%d) per il livello di servizio '%.*ls'. | numero massimo di DTU per database; livello di servizio del pool elastico | Tentativo di impostare il numero massimo di DTU per database al di sotto del limite supportato. | Prendere in considerazione l’utilizzo del livello di servizio del pool elastico che supporta l'impostazione desiderata. |
| 40868 | EX_USER | Il numero massimo di DTU per database non può superare (%d) per il livello di servizio '%.*ls'. | Numero massimo di DTU per database; livello di servizio del pool elastico. | Tentativo di impostare il numero massimo di DTU per database oltre il limite supportato. | Prendere in considerazione l’utilizzo del livello di servizio del pool elastico che supporta l'impostazione desiderata. |
| 40870 | EX_USER | Il numero minimo di DTU per database non può superare (%d) per il livello di servizio '%.*ls'. | numero minimo di DTU per database; livello di servizio del pool elastico. | Tentativo di impostare il numero minimo di DTU per database oltre il limite supportato. | Prendere in considerazione l’utilizzo del livello di servizio del pool elastico che supporta l'impostazione desiderata. |
| 40873 | EX_USER | Il numero di database (%d) e il numero minimo di DTU per ogni database (%d) non può superare le DTU del pool elastico (%d). | Numero di database nel pool elastico; numero minimo di DTU per ogni database; DTU del pool elastico. | Il tentativo di specificare il numero minimo di DTU per i database nel pool elastico che supera il numero di DTU del pool elastico. | Prendere in considerazione l’aumento delle DTU del pool elastico, ridurre il numero minimo di DTU per database o diminuire il numero di database nel pool elastico. |
| 40877 | EX_USER | Impossibile eliminare un pool elastico, a meno che non contenga alcun database. | Nessuno | Il pool elastico contiene uno o più database e pertanto non può essere eliminato. | Rimuovere i database dal pool elastico per eliminarlo. |
| 40881 | EX_USER | Il pool elastico '%.*ls' ha raggiunto il limite del numero di database. Il limite per il numero di database per il pool elastico non può superare (%d) per un pool elastico con (%d) DTU. | Nome del pool elastico. limite numero database del pool elastico; DTU per il pool di risorse. | Tentativo di creare o aggiungere il database al pool elastico quando è stato raggiunto il limite del numero di database del pool elastico. | Prendere in considerazione l’aumento delle DTU del pool elastico, se possibile, per aumentare il limite dei relativi database o rimuovere i database dal pool elastico. |
| 40889 | EX_USER | Impossibile ridurre il limite delle DTU o della memoria per il pool elastico '%.*ls' dal momento che tale operazione non fornirebbe spazio di archiviazione sufficiente per i relativi database. | Nome del pool elastico. | Tentativo di ridurre il limite di archiviazione del pool elastico al di sotto del relativo utilizzo di memoria. | Prendere in considerazione la riduzione dell'utilizzo della memoria dei singoli database nel pool elastico o rimuovere i database dal pool per ridurre le relative DTU o il limite di archiviazione. |
| 40891 | EX_USER | Il numero minimo di DTU per database (%d) non può superare il numero massimo DTU per database (%d). | Numero minimo DTU per database; numero massimo DTU per database | Tentativo di impostare il numero minimo di DTU per database su un valore superiore al numero massimo di DTU per database. | Verificare che il numero minimo di DTU per database non superi il numero massimo di DTU per database. |
| Da definire | EX_USER | Le dimensioni di archiviazione di un singolo database in un pool elastico non possono superare le dimensioni massime consentite dal pool elastico del livello di servizio '%.*ls'. | livello di servizio del pool elastico | Le dimensioni massime per il database superano le dimensioni massime consentite per il livello di servizio del pool elastico. | Impostare le dimensioni massime del database entro i limiti delle dimensioni massime consentite dal livello di servizio del pool elastico. |

<!---HONumber=July15_HO4-->