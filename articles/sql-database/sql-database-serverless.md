---
title: Senza server
description: Questo articolo descrive il nuovo livello di calcolo serverless e lo confronta con il livello di calcolo con provisioning esistente
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 3/11/2020
ms.openlocfilehash: 00b9da150569db2972289468b1405e5087ee3321
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549151"
---
# <a name="azure-sql-database-serverless"></a>Database SQL di Azure senza server

Il server del database SQL di Azure è un livello di calcolo per singoli database che ridimensiona automaticamente il calcolo in base alla domanda del carico di lavoro e alle fatture per la quantità di calcolo usata al secondo. Il livello di calcolo senza server sospende automaticamente i database durante i periodi di inattività quando viene fatturata solo l'archiviazione e riprende automaticamente i database al reso in corso.

## <a name="serverless-compute-tier"></a>Livello di elaborazione serverless

Il livello di calcolo senza server per un singolo database è parametrizzato da un intervallo di scalabilità automatica di calcolo e da un ritardo di autopause.  La configurazione di questi parametri modella l'esperienza delle prestazioni del database e il costo di calcolo.

![Fatturazione serverless](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configurazione delle prestazioni

- I **vCore minimi** e il numero massimo di vCore sono parametri **configurabili** che definiscono l'intervallo di capacità di calcolo disponibile per il database. I limiti di memoria e I/O sono proporzionali all'intervallo vCore specificato.  
- Il ritardo di **autopause** è un parametro configurabile che definisce il periodo di tempo in cui il database deve essere inattivo prima di essere automaticamente sospeso. Il database viene ripreso automaticamente quando si verifica l'account di accesso successivo o un'altra attività.  In alternativa, l'autopapautilizzando può essere disabilitato.

### <a name="cost"></a>Costi

- Il costo per un database senza server è la somma dei costi di calcolo e di archiviazione.
- Quando l'utilizzo del calcolo è compreso tra i limiti min e max configurati, il costo di calcolo si basa su vCore e sulla memoria utilizzata.
- Quando l'utilizzo del calcolo è inferiore ai limiti medi configurati, il costo di calcolo si basa sui valori min vCore e sulla memoria min configurata.
- Quando il database viene sospeso, il costo di calcolo è zero e vengono sostenuti solo i costi di archiviazione.
- Il costo di archiviazione viene determinato nello stesso modo del livello di calcolo di cui è stato eseguito il provisioning.

Per ulteriori informazioni sui costi, vedere [Fatturazione](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scenari

Il modello serverless è caratterizzato da un rapporto qualità-prezzo ottimizzato per database singoli con modelli di utilizzo intermittenti e imprevedibili che possono permettersi qualche ritardo all'avvio del calcolo dopo periodi di inattività. Al contrario, il livello di calcolo di cui è stato eseguito il provisioning è ottimizzato per singoli database o più database in pool elastici con un utilizzo medio più elevato che non può permettersi alcun ritardo nel riscaldamento del calcolo.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenari particolarmente adatti per il calcolo serverless

- Database singoli con modelli di utilizzo intermittenti e imprevedibili intervallati da periodi di inattività e un utilizzo medio inferiore del calcolo nel tempo.
- Database singoli nel livello di calcolo di cui è stato eseguito il provisioning, con spesso ridimensionamento e di ridimensionamento dei clienti che preferiscono delegare il ridimensionamento del calcolo al servizio.
- Nuovi database singoli senza cronologia di utilizzo in cui il dimensionamento del calcolo è difficile o non è possibile stimare prima della distribuzione nel database SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenari particolarmente adatti per il calcolo con provisioning

- Database singoli con modelli di utilizzo più regolari e prevedibili e un maggiore utilizzo medio del calcolo nel tempo.
- Database che non possono tollerare compromessi delle prestazioni dovuti a blocchi di memoria più frequenti o ritardi nella ripresa automatica da uno stato di sospensione.
- Più database con modelli di utilizzo intermittenti e imprevedibili che possono essere consolidati in pool elastici per una migliore ottimizzazione delle prestazioni dei prezzi.

## <a name="comparison-with-provisioned-compute-tier"></a>Confronto con il livello di calcolo con provisioning

La tabella seguente riepiloga le differenze tra il livello di calcolo serverless e il livello di calcolo con provisioning:

| | **Calcolo serverless** | **Calcolo con provisioning** |
|:---|:---|:---|
|**Modello di utilizzo del database**| Utilizzo intermittente e imprevedibile con un utilizzo medio inferiore del calcolo nel tempo. |  Modelli di utilizzo più regolari con un utilizzo medio di calcolo più elevato nel tempo o più database che utilizzano pool elastici.|
| **Impegno per la gestione delle prestazioni** |Minore|Maggiore|
|**Ridimensionamento delle risorse di calcolo**|Automatico|Manuale|
|**Tempo di risposta per le risorse di calcolo**|Inferiore dopo periodi di inattività|Immediato|
|**Granularità della fatturazione**|Al secondo|All'ora|

## <a name="purchasing-model-and-service-tier"></a>Modello di acquisto e livello di servizio

Il database SQL serverless è attualmente supportato solo nel livello per utilizzo generico su hardware di quinta generazione nel modello di acquisto vCore.

## <a name="autoscaling"></a>Scalabilità automatica

### <a name="scaling-responsiveness"></a>Tempo di risposta per il ridimensionamento

In generale, i database senza server vengono eseguiti in un computer con capacità sufficiente per soddisfare la domanda di risorse senza interruzioni per qualsiasi quantità di calcolo richiesto entro i limiti impostati dal valore max vCores. Di tanto in tanto, viene eseguito automaticamente il bilanciamento del carico se il computer non è in grado di soddisfare la richiesta di risorse entro pochi minuti. Ad esempio, se la domanda di risorse è 4 vCore, ma sono disponibili solo 2 vCore, il bilanciamento del carico potrebbe richiedere alcuni minuti prima che vengano forniti 4 vCore. Durante il bilanciamento del carico il database rimane online ad eccezione di un breve periodo al termine dell'operazione, quando vengono rilasciate le connessioni.

### <a name="memory-management"></a>Gestione della memoria

La memoria per i database senza server viene recuperata con maggiore frequenza rispetto ai database di calcolo di cui è stato eseguito il provisioning. Questo comportamento è importante per controllare i costi in serverless e può influire sulle prestazioni.

#### <a name="cache-reclamation"></a>Recupero della cache

A differenza dei database di calcolo di cui è stato eseguito il provisioning, la memoria dalla cache SQL viene recuperata da un database senza server quando l'utilizzo della CPU o della cache è basso.

- L'utilizzo della cache è considerato basso quando la dimensione totale delle voci della cache utilizzate più di recente scende al di sotto di una soglia per un periodo di tempo.
- Quando viene attivato il recupero della cache, la dimensione della cache di destinazione viene ridotta in modo incrementale a una frazione della dimensione precedente e il recupero continua solo se l'utilizzo rimane basso.
- Quando si verifica il recupero della cache, i criteri per la selezione delle voci della cache da rimuovere sono gli stessi criteri di selezione dei database di calcolo di cui è stato eseguito il provisioning quando l'utilizzo eccessivo della memoria è elevato.
- La dimensione della cache non viene mai ridotta al di sotto del limite di memoria min come definito da min vCores che può essere configurato.

Nei database di calcolo senza server e di cui è stato eseguito il provisioning, le voci della cache possono essere rimosse se viene utilizzata tutta la memoria disponibile.

#### <a name="cache-hydration"></a>Idratazione cache

La cache SQL aumenta man mano che i dati vengono recuperati dal disco nello stesso modo e con la stessa velocità dei database di cui è stato eseguito il provisioning. Quando il database è occupato, la cache può crescere senza vincoli fino al limite massimo di memoria.

## <a name="autopausing-and-autoresuming"></a>Autopautilizzando e riprendendo l'autore

### <a name="autopausing"></a>Correzione automatica

L'autopaposizione viene attivata se tutte le condizioni seguenti sono vere per la durata del ritardo di pausa automatica:

- Numero di sessioni = 0
- CPU: 0 per il carico di lavoro degli utenti in esecuzione nel pool di utenti

Se lo si desidera, viene fornita un'opzione per disabilitare l'autopautilizzando.

Le seguenti funzionalità non supportano l'autopaping.  Ovvero, se viene utilizzata una delle seguenti funzionalità, il database rimane online indipendentemente dalla durata dell'inattività del database:

- Replica geografica (gruppi di replica geografica e failover automatico attivi).
- Conservazione dei backup a lungo termine (LTR).
- Database di sincronizzazione utilizzato nella sincronizzazione dei dati SQL.  A differenza dei database di sincronizzazione, i database hub e membri supportano l'autopalusing.
- Database dei processi utilizzato nei processi elastici.

L'autopaposizione viene temporaneamente impedita durante la distribuzione di alcuni aggiornamenti del servizio che richiedono che il database sia online.  In questi casi, l'autopausing diventa nuovamente consentito al termine dell'aggiornamento del servizio.

### <a name="autoresuming"></a>Rallonvi

L'autoreposizione viene attivata se una delle seguenti condizioni è vera in qualsiasi momento:

|Funzionalità|Trigger di ripresa automatica|
|---|---|
|Autenticazione e autorizzazione|Login|
|Introduzione al rilevamento delle minacce|Abilitazione/disabilitazione delle impostazioni di rilevamento delle minacce a livello di database o server.<br>Modifica delle impostazioni di rilevamento delle minacce a livello di database o di server.|
|Individuazione e classificazione dei dati|Aggiunta, modifica, eliminazione o visualizzazione delle etichette di riservatezza|
|Controllo|Visualizzazione dei record di controllo<br>Aggiornamento o visualizzazione dei criteri di controllo.|
|Maschera dati|Aggiunta, modifica, eliminazione o visualizzazione delle regole di maschera dati|
|Transparent Data Encryption|Visualizzazione dello stato di Transparent Data Encryption|
|Esecuzione di query sulle prestazioni dell'archivio dati|Modifica o visualizzazione delle impostazioni dell'archivio query|
|Ottimizzazione automatica|Applicazione e verifica delle indicazioni di ottimizzazione automatica, ad esempio l'indicizzazione automatica|
|Copia del database|Creare il database come copia.<br>Esportare in un file BACPAC.|
|Sincronizzazione dati SQL|Sincronizzazione tra database hub e membro che viene eseguita secondo un calendario configurabile o manualmente|
|Modifica di alcuni metadati del database|Aggiunta di nuovi tag di database.<br>Modifica di max vCores, min vCore s o ritardo di autopause.|
|SQL Server Management Studio (SSMS)|Utilizzando le versioni di SSMS precedenti alla 18.1 e l'apertura di una nuova finestra di query per qualsiasi database nel server riprenderà qualsiasi database in pausa automatica nello stesso server. Questo comportamento non si verifica se si utilizza SSMS versione 18.1 o successiva.|

L'autoreposizione viene attivata anche durante la distribuzione di alcuni aggiornamenti del servizio che richiedono che il database sia online.

### <a name="connectivity"></a>Connettività

Se un database senza server viene sospeso, il primo account di accesso riprenderà il database e restituirà un errore che indica che il database non è disponibile con codice di errore 40613. Dopo la ripresa del database è necessario ripetere l'accesso per stabilire la connettività. I client del database con la logica di ripetizione dei tentativi di connessione non devono essere modificati.

### <a name="latency"></a>Latenza

La latenza per la rigenerazione automatica e la sospensione automatica di un database senza server è in genere l'ordine di 1 minuto per la ripresa automatica e 1-10 minuti per la sospensione automatica.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Crittografia dei dati trasparenti gestita dal cliente (BYOK)

Se si utilizza la crittografia dei [dati trasparenti gestita](transparent-data-encryption-byok-azure-sql.md) dal cliente (BYOK) e il database senza server viene automaticamente sospeso quando si verifica l'eliminazione o la revoca della chiave, il database rimane nello stato di sospensione automatica.  In questo caso, dopo la ripresa del database, il database diventa inaccessibile entro circa 10 minuti.  Quando il database diventa inaccessibile, il processo di ripristino è lo stesso dei database di calcolo di cui è stato eseguito il provisioning.  Se il database senza server è online quando si verifica l'eliminazione o la revoca delle chiavi, anche il database diventa inaccessibile dopo circa 10 minuti o meno nello stesso modo in cui viene eseguito il provisioning dei database di calcolo.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding nel livello di calcolo senza serverOnboarding into serverless compute tier

La creazione di un nuovo database o lo spostamento di un database esistente in un livello di calcolo senza server segue lo stesso modello della creazione di un nuovo database nel livello di calcolo di cui è stato eseguito il provisioning e prevede i due passaggi seguenti.

1. Specificare il nome dell'obiettivo di servizio. L'obiettivo del servizio prescrive il livello di servizio, la generazione dell'hardware e il numero massimo di vCore. La tabella seguente mostra le opzioni relative all'obiettivo di servizio:

   |Nome dell'obiettivo di servizio|Livello di servizio|Generazione dell'hardware|Numero massimo vCore|
   |---|---|---|---|
   |GP_S_Gen5_1|Utilizzo generico|Quinta generazione|1|
   |GP_S_Gen5_2|Utilizzo generico|Quinta generazione|2|
   |GP_S_Gen5_4|Utilizzo generico|Quinta generazione|4|
   |GP_S_Gen5_6|Utilizzo generico|Quinta generazione|6|
   |GP_S_Gen5_8|Utilizzo generico|Quinta generazione|8|
   |GP_S_Gen5_10|Utilizzo generico|Quinta generazione|10|
   |GP_S_Gen5_12|Utilizzo generico|Quinta generazione|12|
   |GP_S_Gen5_14|Utilizzo generico|Quinta generazione|14|
   |GP_S_Gen5_16|Utilizzo generico|Quinta generazione|16|

2. Facoltativamente, specificare i valori min vCores e autopause delay per modificare i valori predefiniti. La tabella seguente illustra i valori disponibili per questi parametri.

   |Parametro|Valori disponibili|Valore predefinito|
   |---|---|---|---|
   |Min vCores|Dipende da max vCore configurati - vedi [limiti delle risorse](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 vCore|
   |Ritardo di sospensione automatica|Minimo: 60 minuti (1 ora)<br>Massimo: 10080 minuti (7 giorni)<br>Incrementi: 60 minuti<br>Disabilita la sospensione automatica: -1|60 minuti|


### <a name="create-new-database-in-serverless-compute-tier"></a>Creare un nuovo database nel livello di calcolo senza serverCreate new database in serverless compute tier 

Negli esempi seguenti viene creato un nuovo database nel livello di calcolo senza server. Gli esempi specificano in modo esplicito i valori min vCores, max vCores e autopause delay.

#### <a name="use-azure-portal"></a>Usare il portale di Azure

Vedere [Guida introduttiva: Creare un singolo database nel database SQL](sql-database-single-database-get-started.md)di Azure usando il portale di Azure.See Quickstart: Create a single database in Azure SQL Database using the Azure portal .


#### <a name="use-powershell"></a>Usare PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Usare Transact-SQLTransact-SQL (T-SQL)Use Transact-SQL (T-SQL)

Nell'esempio seguente viene creato un nuovo database nel livello di calcolo senza server.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Per informazioni dettagliate, vedere [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Spostare il database dal livello di calcolo di cui è stato eseguito il provisioning al livello di calcolo senza serverMove database from provisioned compute tier to serverless compute tier

Gli esempi seguenti spostano un database dal livello di calcolo di cui è stato eseguito il provisioning nel livello di calcolo senza server. Gli esempi specificano in modo esplicito i valori min vCores, max vCores e autopause delay.

#### <a name="use-powershell"></a>Usare PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Usare Transact-SQLTransact-SQL (T-SQL)Use Transact-SQL (T-SQL)

Nell'esempio seguente viene spostato un database dal livello di calcolo di cui è stato eseguito il provisioning nel livello di calcolo senza server.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Per informazioni dettagliate, vedere [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Spostare il database dal livello di calcolo senza server al livello di calcolo di cui è stato eseguito il provisioningMove database from serverless compute tier to provisioned compute tier

La procedura per spostare un database serverless in un livello di calcolo con provisioning è analoga a quella per spostare un database di calcolo con provisioning in un livello di calcolo serverless.

## <a name="modifying-serverless-configuration"></a>Modifica della configurazione senza server

### <a name="use-powershell"></a>Usare PowerShell

La modifica dei vCore massimi o minimi e del ritardo di autopause viene `MaxVcore` `MinVcore`eseguita `AutoPauseDelayInMinutes` utilizzando il comando [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) in PowerShell utilizzando gli argomenti , e .

### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

La modifica dei vCore massimi o minimi e del ritardo di autopause `capacity`viene `min-capacity`eseguita utilizzando il comando az sql [db update](/cli/azure/sql/db#az-sql-db-update) nell'interfaccia della riga di comando di Azure usando gli argomenti , e `auto-pause-delay` .


## <a name="monitoring"></a>Monitoraggio

### <a name="resources-used-and-billed"></a>Risorse usate e fatturate

Le risorse di un database senza server sono incapsulate da entità del pacchetto dell'app, dell'istanza SQL e del pool di risorse utente.

#### <a name="app-package"></a>Pacchetto dell'app

Il pacchetto app è il limite più esterno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Il pacchetto dell'app contiene i servizi esterni e l'istanza di SQL che definiscono congiuntamente l'ambito di tutte le risorse utente e di sistema usate da un database nel database SQL. R e la ricerca full-text sono esempi di servizi esterni. L'istanza di SQL è in genere la voce più onerosa nell'utilizzo complessivo delle risorse nel pacchetto dell'app.

#### <a name="user-resource-pool"></a>Pool di risorse utente

Il pool di risorse utente è il limite più interno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Il pool di risorse utente inscope CPU e IO per il carico di lavoro utente generato da query DDL, ad esempio query CREATE e ALTER e DML quali SELECT, INSERT, UPDATE e DELETE. Queste domande rappresentano in genere la percentuale più consistente di utilizzo all'interno del pacchetto dell'app.

### <a name="metrics"></a>Metriche

Le metriche per il monitoraggio dell'utilizzo delle risorse del pacchetto dell'app e del pool di utenti di un database senza server sono elencate nella tabella seguente:

|Entità|Metrica|Descrizione|Unità|
|---|---|---|---|
|Pacchetto dell'app|app_cpu_percent|Percentuale del numero di vCore usati dall'app rispetto al numero massimo di vCore consentito per l'app.|Percentuale|
|Pacchetto dell'app|app_cpu_billed|Quantità di risorse di calcolo fatturata per l'app durante il periodo di riferimento. L'importo pagato durante questo periodo è dato dal prodotto di questa metrica per il prezzo unitario dei vCore. <br><br>I valori di questa metrica sono determinati dall'aggregazione nel tempo del numero massimo di CPU usate e dalla memoria usata al secondo. Se la quantità usata è inferiore a quella minima di cui è stato effettuato il provisioning in base al valore impostato per il numero minimo di vCore e la quantità minima di memoria, viene fatturata la quantità minima di cui è stato effettuato il provisioning.Per confrontare la quantità di CPU e di memoria ai fini della fatturazione, la memoria viene normalizzata in unità di vCore ridimensionando la quantità di memoria in GB in base a 3 GB per vCore.|Secondi per vCore|
|Pacchetto dell'app|app_memory_percent|Percentuale di memoria usata dall'app rispetto alla memoria massima consentita per l'app.|Percentuale|
|Pool di utenti|cpu_percent|Percentuale di vCore usati dal carico di lavoro utente rispetto al numero massimo di vCore consentiti per il carico di lavoro utente.|Percentuale|
|Pool di utenti|data_IO_percent|Percentuale di operazioni di I/O al secondo sui dati usate dal carico di lavoro utente rispetto al numero massimo di operazioni di I/O al secondo sui dati consentite per il carico di lavoro utente.|Percentuale|
|Pool di utenti|log_IO_percent|Percentuale di MB/s di log usati dal carico di lavoro utente rispetto al numero massimo di MB/s di log consentiti per il carico di lavoro utente.|Percentuale|
|Pool di utenti|workers_percent|Percentuale di ruoli di lavoro usati dal carico di lavoro utente rispetto al numero massimo di ruoli di lavoro consentiti per il carico di lavoro utente.|Percentuale|
|Pool di utenti|sessions_percent|Percentuale di sessioni usate dal carico di lavoro utente rispetto al numero massimo di sessioni consentite per il carico di lavoro utente.|Percentuale|

### <a name="pause-and-resume-status"></a>Stato di sospensione e ripresa

Nel portale di Azure lo stato del database è visualizzato nel riquadro della panoramica del server in cui sono elencati i database in esso contenuti. Lo stato del database è anche visualizzato nel riquadro della panoramica relativo al database.

Utilizzando i comandi seguenti per eseguire una query sullo stato di sospensione e ripresa di un database:

#### <a name="use-powershell"></a>Usare PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Limiti delle risorse

Per i limiti delle risorse, vedere livello di [calcolo senza server](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Fatturazione

La quantità di risorse di calcolo fatturata corrisponde alla quantità massima di CPU e di memoria usata ogni secondo. Se la quantità di CPU e di memoria usata è inferiore alla quantità minima del provisioning per ognuna, viene fatturata la quantità del provisioning. Per confrontare la quantità di CPU e di memoria ai fini della fatturazione, la memoria viene normalizzata in unità di vCore ridimensionando la quantità di memoria in GB in base a 3 GB per vCore.

- **Risorsa fatturata**: CPU e memoria
- **Importo fatturato**: prezzo unitario vCore : max (min vCores, vCores utilizzati, min memory GB - 1/3, MEMORY GB utilizzati : 1/3) 
- **Frequenza di fatturazione**: Al secondo

Il prezzo unitario vCore è il costo per vCore al secondo. Per i prezzi unitari in una determinata area, vedere la [pagina dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

La quantità di risorse di calcolo fatturata è esposta dalla metrica seguente:

- **Metrica**: app_cpu_billed (secondi per vCore)
- **Definizione**: massimo (numero minimo di vCore, numero di vCore usati, quantità minima di memoria in GB * 1/3, quantità di memoria in GB usata * 1/3)
- **Frequenza di segnalazione**: Al minuto

Questa quantità viene calcolata ogni secondo e aggregata in un minuto.

Si consideri un database senza server configurato con 1 min vCore e 4 max vCores.  Ciò corrisponde a circa 3 GB di memoria min e 12 GB di memoria massima.  Si supponga che il ritardo di sospensione automatica sia impostato su 6 ore e che il carico di lavoro del database sia attivo durante le prime 2 ore di un periodo di 24 ore e altrimenti inattivo.    

In questo caso, il database viene fatturato per il calcolo e l'archiviazione durante le prime 8 ore.  Anche se il database è inattivo a partire dalla seconda ora, viene comunque fatturato per il calcolo nelle 6 ore successive in base al calcolo minimo di cui è stato eseguito il provisioning mentre il database è online.  Solo lo spazio di archiviazione viene fatturato durante il resto del periodo di 24 ore mentre il database viene sospeso.

Più precisamente, la fattura di calcolo in questo esempio viene calcolata come segue:

|Intervallo di tempo|vCores utilizzati ogni secondo|GB utilizzati ogni secondo|Calcola dimensione fatturata|secondi vCore fatturati nell'intervallo di tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores utilizzati|4 vCores - 3600 secondi - 14400 vCore secondi|
|1:00-2:00|1|12|Memoria utilizzata|12 GB : 1/3 , 3600 secondi e 14400 vCore secondi|
|2:00-8:00|0|0|Memoria min di cui è stato eseguito il provisioning|3 GB : 1/3 , 21600 secondi e 21600 secondi vCore|
|8:00-24:00|0|0|Nessun calcolo fatturato durante la pausa|0 secondi vCore|
|Totale secondi vCore fatturati su 24 ore||||50400 secondi vCore|

Si supponga che il prezzo unitario di calcolo sia di 0,000145 USD/vCore/secondo.  Quindi il calcolo fatturato per questo periodo di 24 ore è il prodotto del prezzo unitario di calcolo e dei secondi vCore fatturati: 0,000145/vCore/secondo, 50400 vCore secondi, 7,31 USD

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Vantaggi di Azure Hybrid e capacità riservataAzure Hybrid Benefit and reserved capacity

I vantaggi ibridi di Azure (AHB) e gli sconti sulla capacità riservata non si applicano al livello di calcolo senza server.

## <a name="available-regions"></a>Aree disponibili

Il livello di calcolo senza server è disponibile in tutto il mondo, ad eccezione delle seguenti aree geografiche: Cina orientale, Cina settentrionale, Germania centrale, Germania nord-est, Regno Unito nord, Regno Unito South 2, Stati Uniti centro-occidentali e US Gov Central (Iowa).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere Guida introduttiva: Creare un singolo database nel database SQL di Azure usando il portale di Azure.To get started, see [Quickstart: Create a single database in Azure SQL Database using the Azure portal](sql-database-single-database-get-started.md).
- Per i limiti delle risorse, vedere [Limiti delle risorse del livello di calcolo serverless](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
