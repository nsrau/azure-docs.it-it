---
title: Livello di elaborazione serverless
description: Questo articolo descrive il nuovo livello di calcolo senza server e lo confronta con il livello di calcolo con provisioning esistente per il database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 7/9/2020
ms.openlocfilehash: 38ca6528b77d9f36c84f5aacaa34a64d113b5978
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206941"
---
# <a name="azure-sql-database-serverless"></a>Database SQL di Azure senza server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Senza server è un livello di calcolo per i database singoli nel database SQL di Azure che ridimensiona automaticamente le risorse di calcolo in base alla domanda del carico di lavoro e alle fatture per la quantità di calcolo usata al secondo. Il livello di calcolo senza server inoltre sospende automaticamente i database durante i periodi di inattività quando viene addebitata solo l'archiviazione e riprende automaticamente i database quando l'attività restituisce.

## <a name="serverless-compute-tier"></a>Livello di elaborazione serverless

Il livello di calcolo senza server per database singoli nel database SQL di Azure è parametrizzato da un intervallo di scalabilità automatica di calcolo e da un ritardo di sospensione automatica. La configurazione di questi parametri forma le prestazioni del database e i costi di calcolo.

![Fatturazione serverless](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Configurazione delle prestazioni

- Il valore **minimo di Vcore** e il **numero massimo di Vcore** sono parametri configurabili che definiscono l'intervallo di capacità di calcolo disponibile per il database. I limiti di memoria e I/O sono proporzionali all'intervallo vCore specificato.  
- Il **ritardo di sospensione** automatica è un parametro configurabile che definisce il periodo di tempo in cui il database deve rimanere inattivo prima che venga sospeso automaticamente. Il database viene ripreso automaticamente quando si verifica il successivo accesso o un'altra attività.  In alternativa, è possibile disabilitare l'autosospensione.

### <a name="cost"></a>Costo

- Il costo di un database senza server è la somma del costo di calcolo e dei costi di archiviazione.
- Quando l'utilizzo delle risorse di calcolo è compreso tra i limiti minimo e massimo configurati, il costo di calcolo è basato su vCore e sulla memoria usata.
- Quando l'utilizzo delle risorse di calcolo è inferiore ai limiti minimi configurati, il costo di calcolo è basato sul numero minimo di Vcore e sulla memoria minima configurata.
- Quando il database viene sospeso, il costo di calcolo è pari a zero e vengono sostenuti solo i costi di archiviazione.
- Il costo di archiviazione viene determinato in modo analogo al livello di calcolo di cui è stato effettuato il provisioning.

Per informazioni più dettagliate sui costi, vedere [fatturazione](serverless-tier-overview.md#billing).

## <a name="scenarios"></a>Scenari

Il modello serverless è caratterizzato da un rapporto qualità-prezzo ottimizzato per database singoli con modelli di utilizzo intermittenti e imprevedibili che possono permettersi qualche ritardo all'avvio del calcolo dopo periodi di inattività. Al contrario, il livello di calcolo con provisioning è caratterizzato da un rapporto prezzo-prestazioni ottimizzato per database singoli o per più database in pool elastici con un utilizzo medio più elevato che non possono tollerare ritardi nella fase di avvio del calcolo.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenari particolarmente adatti per il calcolo serverless

- Database singoli con modelli di utilizzo intermittenti e non prevedibili intercalati con periodi di inattività e un utilizzo inferiore medio del calcolo nel tempo.
- Singoli database nel livello di calcolo di cui è stato effettuato il provisioning che vengono spesso ridimensionati e i clienti che preferiscono delegare il ridimensionamento del calcolo al servizio.
- Nuovi database singoli senza cronologia di utilizzo in cui il dimensionamento del calcolo è difficile o impossibile da stimare prima della distribuzione nel database SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenari particolarmente adatti per il calcolo con provisioning

- Database singoli con modelli di utilizzo più regolari e prevedibili e un utilizzo di calcolo medio superiore nel tempo.
- Database che non possono tollerare compromessi delle prestazioni dovuti a blocchi di memoria più frequenti o ritardi nella ripresa automatica da uno stato di sospensione.
- Più database con modelli di utilizzo intermittenti e imprevedibili che possono essere consolidati in pool elastici per migliorare l'ottimizzazione delle prestazioni.

## <a name="comparison-with-provisioned-compute-tier"></a>Confronto con il livello di calcolo con provisioning

La tabella seguente riepiloga le differenze tra il livello di calcolo serverless e il livello di calcolo con provisioning:

| | **Calcolo serverless** | **Calcolo con provisioning** |
|:---|:---|:---|
|**Modello di utilizzo del database**| Utilizzo intermittente e non prevedibile con un utilizzo di calcolo medio inferiore nel tempo. | Modelli di utilizzo più regolari con utilizzo di calcolo medio superiore nel tempo o più database con pool elastici.|
| **Impegno per la gestione delle prestazioni** |Minore|Maggiore|
|**Ridimensionamento delle risorse di calcolo**|Automatico|Manuale|
|**Tempo di risposta per le risorse di calcolo**|Inferiore dopo periodi di inattività|Immediato|
|**Granularità della fatturazione**|Al secondo|All'ora|

## <a name="purchasing-model-and-service-tier"></a>Modello di acquisto e livello di servizio

Il database SQL serverless è attualmente supportato solo nel livello per utilizzo generico su hardware di quinta generazione nel modello di acquisto vCore.

## <a name="autoscaling"></a>Scalabilità automatica

### <a name="scaling-responsiveness"></a>Tempo di risposta per il ridimensionamento

In generale, i database senza server vengono eseguiti in un computer dotato di capacità sufficiente per soddisfare la richiesta di risorse senza interruzioni per qualsiasi quantità di calcolo richiesta entro i limiti impostati dal valore massimo di vcore. Di tanto in tanto, viene eseguito automaticamente il bilanciamento del carico se il computer non è in grado di soddisfare la richiesta di risorse entro pochi minuti. Ad esempio, se la richiesta di risorse è 4 Vcore, ma sono disponibili solo 2 Vcore, potrebbero essere necessari alcuni minuti per il bilanciamento del carico prima che vengano forniti 4 vcore. Durante il bilanciamento del carico il database rimane online ad eccezione di un breve periodo al termine dell'operazione, quando vengono rilasciate le connessioni.

### <a name="memory-management"></a>Gestione della memoria

La memoria per i database senza server viene recuperata più frequentemente rispetto ai database di calcolo di cui è stato effettuato il provisioning. Questo comportamento è importante per controllare i costi in senza server e può influisca sulle prestazioni.

#### <a name="cache-reclamation"></a>Recupero della cache

A differenza dei database di calcolo con provisioning, la memoria della cache SQL viene recuperata da un database senza server quando l'utilizzo della CPU o della cache attiva è basso.  Si noti che quando l'utilizzo della CPU è basso, l'utilizzo della cache attiva può rimanere elevato a seconda del modello di utilizzo e impedire il recupero di memoria.

- L'utilizzo della cache attiva è considerato basso quando la dimensione totale delle voci della cache utilizzate più di recente scende al di sotto di una soglia per un determinato periodo di tempo.
- Quando viene attivato il recupero della cache, le dimensioni della cache di destinazione vengono ridotte in modo incrementale a una frazione della dimensione precedente e il recupero continua solo se l'utilizzo rimane ridotto.
- Quando si verifica il recupero della cache, i criteri per la selezione delle voci della cache da rimuovere sono gli stessi criteri di selezione per i database di calcolo con provisioning quando il numero di richieste di memoria è elevato.
- Le dimensioni della cache non vengono mai ridotte al di sotto del limite di memoria minimo come definito da min Vcore, che può essere configurato.

Nei database di calcolo senza server e con provisioning, le voci della cache possono essere eliminate se viene utilizzata tutta la memoria disponibile.

#### <a name="cache-hydration"></a>Idratazione della cache

La cache SQL cresce man mano che i dati vengono recuperati dal disco nello stesso modo e con la stessa velocità dei database di cui è stato effettuato il provisioning. Quando il database è occupato, è possibile che la cache cresca senza vincoli fino al limite massimo di memoria.

## <a name="autopausing-and-autoresuming"></a>Sospensione e ripresa di autosospensione

### <a name="autopausing"></a>Sospensione dell'autosospensione

La sospensione automatica viene attivata se tutte le condizioni seguenti sono vere per la durata del ritardo di sospensione automatica:

- Numero di sessioni = 0
- CPU = 0 per il carico di lavoro dell'utente in esecuzione nel pool di utenti

Se lo si desidera, viene fornita un'opzione per disabilitare la sospensione.

Le funzionalità seguenti non supportano la sospensione automatica, ma supportano la scalabilità automatica.  Ovvero, se viene utilizzata una delle funzionalità seguenti, il database rimane online indipendentemente dalla durata dell'inattività del database:

- Replica geografica (gruppi di replica geografica attiva e failover automatico).
- Conservazione dei backup a lungo termine (LTR).
- Database di sincronizzazione utilizzato nella sincronizzazione dati SQL.  A differenza dei database di sincronizzazione, i database hub e membri supportano la sospensione automatica.
- Il database dei processi usato nei processi elastici.

La sospensione dell'autosospensione è temporaneamente bloccata durante la distribuzione di alcuni aggiornamenti dei servizi che richiedono che il database sia online.  In questi casi, la sospensione automatico diventa nuovamente consentita al termine dell'aggiornamento del servizio.

### <a name="autoresuming"></a>Riavvio

La ripresa automatica viene attivata se si verifica una delle condizioni seguenti in qualsiasi momento:

|Funzionalità|Trigger di ripresa automatica|
|---|---|
|Autenticazione e autorizzazione|Accesso|
|Rilevamento delle minacce|Abilitazione o disabilitazione delle impostazioni di rilevamento delle minacce a livello di database o di server.<br>Modifica delle impostazioni di rilevamento delle minacce a livello di database o di server.|
|Individuazione e classificazione dei dati|Aggiunta, modifica, eliminazione o visualizzazione delle etichette di riservatezza|
|Controllo|Visualizzazione dei record di controllo<br>Aggiornamento o visualizzazione dei criteri di controllo.|
|Maschera dati|Aggiunta, modifica, eliminazione o visualizzazione delle regole di maschera dati|
|Transparent Data Encryption|Visualizzazione dello stato di Transparent Data Encryption|
|Valutazione della vulnerabilità|Analisi ad hoc e analisi periodiche se abilitate|
|Esecuzione di query sulle prestazioni dell'archivio dati|Modifica o visualizzazione delle impostazioni dell'archivio query|
|Ottimizzazione automatica|Applicazione e verifica delle indicazioni di ottimizzazione automatica, ad esempio l'indicizzazione automatica|
|Copia del database|Crea database come copia.<br>Esportare in un file BACPAC.|
|Sincronizzazione dati SQL|Sincronizzazione tra database hub e membro che viene eseguita secondo un calendario configurabile o manualmente|
|Modifica di alcuni metadati del database|Aggiunta di nuovi tag del database.<br>Modifica del ritardo massimo Vcore, minimo Vcore o di sospensione.|
|SQL Server Management Studio (SSMS)|L'uso di versioni di SSMS precedenti alla 18,1 e l'apertura di una nuova finestra di query per qualsiasi database nel server riprenderà tutti i database sospesi automaticamente nello stesso server. Questo comportamento non si verifica se si usa SSMS versione 18,1 o successiva.|

Il monitoraggio, la gestione o altre soluzioni che eseguono una delle operazioni elencate in precedenza attiveranno il riavvio automatico.

La ripresa automatica viene attivata anche durante la distribuzione di alcuni aggiornamenti dei servizi che richiedono che il database sia online.

### <a name="connectivity"></a>Connettività

Se un database senza server viene sospeso, il primo account di accesso riprenderà il database e restituirà un errore indicante che il database non è disponibile con codice di errore 40613. Dopo la ripresa del database è necessario ripetere l'accesso per stabilire la connettività. I client del database con la logica di ripetizione dei tentativi di connessione non devono essere modificati.

### <a name="latency"></a>Latenza

La latenza per il riavvio e la sospensione di un database senza server è in genere un ordine di 1 minuto per il riavvio e 1-10 minuti di sospensione.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Transparent Data Encryption gestita dal cliente (BYOK)

Se l'uso di [Transparent Data Encryption](transparent-data-encryption-byok-overview.md) (BYOK) gestito dal cliente e il database senza server viene sospeso automaticamente quando si verifica l'eliminazione o la revoca della chiave, il database rimane nello stato di sospensione automatica.  In questo caso, dopo la ripresa del database, il database diventa inaccessibile entro circa 10 minuti.  Quando il database diventa inaccessibile, il processo di ripristino è identico a quello per i database di calcolo di cui è stato effettuato il provisioning.  Se il database senza server è online quando si verifica l'eliminazione o la revoca della chiave, anche il database diventa inaccessibile entro circa 10 minuti allo stesso modo dei database di calcolo con provisioning.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding nel livello di calcolo senza server

La creazione di un nuovo database o lo trasferimento di un database esistente in un livello di calcolo senza server segue lo stesso modello della creazione di un nuovo database nel livello di calcolo con provisioning e prevede i due passaggi seguenti.

1. Specificare l'obiettivo di servizio. L'obiettivo di servizio prevede il livello di servizio, la generazione hardware e il numero massimo di vcore. Per le opzioni degli obiettivi di servizio, vedere [limiti delle risorse senza server](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Facoltativamente, specificare il ritardo min Vcore e autopause per modificare i valori predefiniti. La tabella seguente illustra i valori disponibili per questi parametri.

   |Parametro|Valori disponibili|Valore predefinito|
   |---|---|---|---|
   |Min vcore|Dipende dal numero massimo di Vcore configurati. vedere [limiti delle risorse](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 vCore|
   |Ritardo di sospensione automatica|Minimo: 60 minuti (1 ora)<br>Massimo: 10080 minuti (7 giorni)<br>Incrementi: 10 minuti<br>Disabilita la sospensione automatica: -1|60 minuti|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Creare un nuovo database nel livello di calcolo senza server

Negli esempi seguenti viene creato un nuovo database nel livello di calcolo senza server.

#### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Vedere [Guida introduttiva: creare un database singolo nel database SQL di Azure usando il portale di Azure](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>Usare PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Usare Transact-SQL (T-SQL)

Quando si usa T-SQL, vengono applicati i valori predefiniti per il ritardo minimo di Vcore e di sospensione.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Per informazioni dettagliate, vedere [create database](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Spostare un database dal livello di calcolo di cui è stato effettuato il provisioning nel livello di calcolo senza server

Gli esempi seguenti spostano un database dal livello di calcolo di cui è stato effettuato il provisioning nel livello di calcolo senza server.

#### <a name="use-powershell"></a>Usare PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Usare Transact-SQL (T-SQL)

Quando si usa T-SQL, vengono applicati i valori predefiniti per il ritardo minimo di Vcore e di sospensione.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Per informazioni dettagliate, vedere [ALTER database](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Spostare un database dal livello di calcolo senza server al livello di calcolo di cui è stato effettuato il provisioning

La procedura per spostare un database serverless in un livello di calcolo con provisioning è analoga a quella per spostare un database di calcolo con provisioning in un livello di calcolo serverless.

## <a name="modifying-serverless-configuration"></a>Modifica della configurazione senza server

### <a name="use-powershell"></a>Usare PowerShell

Per modificare il valore massimo o minimo per Vcore e il ritardo di sospensione, viene eseguita usando il comando [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) in PowerShell usando gli `MaxVcore` `MinVcore` argomenti, e `AutoPauseDelayInMinutes` .

### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

Per modificare il vcore massimo o minimo e il ritardo di sospensione, è necessario usare il comando [AZ SQL DB Update](/cli/azure/sql/db#az-sql-db-update) nell'interfaccia della riga di comando di Azure usando gli `capacity` `min-capacity` argomenti, e `auto-pause-delay` .


## <a name="monitoring"></a>Monitoraggio

### <a name="resources-used-and-billed"></a>Risorse usate e fatturate

Le risorse di un database senza server sono incapsulate dal pacchetto dell'app, dall'istanza di SQL e dalle entità del pool di risorse utente.

#### <a name="app-package"></a>Pacchetto dell'app

Il pacchetto app è il limite più esterno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Il pacchetto dell'app contiene i servizi esterni e l'istanza di SQL che definiscono congiuntamente l'ambito di tutte le risorse utente e di sistema usate da un database nel database SQL. R e la ricerca full-text sono esempi di servizi esterni. L'istanza di SQL è in genere la voce più onerosa nell'utilizzo complessivo delle risorse nel pacchetto dell'app.

#### <a name="user-resource-pool"></a>Pool di risorse utente

Il pool di risorse utente è il limite più interno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Gli ambiti del pool di risorse utente CPU e i/o per il carico di lavoro dell'utente generato da query DDL, ad esempio le query CREATE e ALTER e DML, ad esempio SELECT, INSERT, UPDATE e DELETE. Queste domande rappresentano in genere la percentuale più consistente di utilizzo all'interno del pacchetto dell'app.

### <a name="metrics"></a>Metriche

Nella tabella seguente sono elencate le metriche per il monitoraggio dell'utilizzo delle risorse del pacchetto dell'app e del pool di utenti di un database senza server.

|Entità|Metrica|Descrizione|Units|
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

Usare i comandi seguenti per eseguire una query sullo stato di sospensione e ripresa di un database:

#### <a name="use-powershell"></a>Usare PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Limiti delle risorse

Per i limiti delle risorse, vedere [livello di calcolo senza server](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Fatturazione

La quantità di risorse di calcolo fatturata corrisponde alla quantità massima di CPU e di memoria usata ogni secondo. Se la quantità di CPU e di memoria usata è inferiore alla quantità minima del provisioning per ognuna, viene fatturata la quantità del provisioning. Per confrontare la quantità di CPU e di memoria ai fini della fatturazione, la memoria viene normalizzata in unità di vCore ridimensionando la quantità di memoria in GB in base a 3 GB per vCore.

- **Risorsa fatturata**: CPU e memoria
- **Importo fatturato**: prezzo unitario vCore * Max (min Vcore, Vcore usato, min memory gb * 1/3, GB di memoria usati * 1/3) 
- **Frequenza di fatturazione**: al secondo

Il prezzo unitario vCore è il costo per ogni vCore al secondo. Per i prezzi unitari in una determinata area, vedere la [pagina dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

La quantità di risorse di calcolo fatturata è esposta dalla metrica seguente:

- **Metrica**: app_cpu_billed (secondi per vCore)
- **Definizione**: massimo (numero minimo di vCore, numero di vCore usati, quantità minima di memoria in GB * 1/3, quantità di memoria in GB usata * 1/3)
- **Frequenza report**: al minuto

Questa quantità viene calcolata ogni secondo e aggregata in un minuto.

### <a name="minimum-compute-bill"></a>Fattura minima di calcolo

Se un database senza server è sospeso, la fattura di calcolo è pari a zero.  Se un database senza server non viene sospeso, la fattura di calcolo minima non è inferiore alla quantità di Vcore in base al valore Max (min Vcore, min memory GB * 1/3).

Esempi:

- Si supponga che un database senza server non sia sospeso e configurato con 8 Max Vcore e 1 min vCore corrispondente a 3,0 GB di memoria min.  La fattura di calcolo minima è quindi basata su Max (1 vCore, 3,0 GB * 1 vCore/3 GB) = 1 vCore.
- Si supponga che un database senza server non sia sospeso e configurato con 4 Vcore Max e 0,5 min Vcore corrispondenti a 2,1 GB min di memoria.  La fattura di calcolo minima è basata su Max (0,5 Vcore, 2,1 GB * 1 vCore/3 GB) = 0,7 vcore.

Il [calcolatore dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/calculator/?service=sql-database) per senza server può essere usato per determinare la memoria minima configurabile in base al numero massimo e minimo di Vcore configurati.  Come regola, se il valore min Vcore configurato è maggiore di 0,5 Vcore, la fattura minima di calcolo è indipendente dalla memoria minima configurata e basata solo sul numero di min Vcore configurati.

### <a name="example-scenario"></a>Scenario di esempio

Si consideri un database senza server configurato con 1 min vCore e 4 Vcore max.  Corrisponde a circa 3 GB di memoria minima e a 12 GB di memoria max.  Si supponga che il ritardo di sospensione automatica sia impostato su 6 ore e che il carico di lavoro del database sia attivo nelle prime 2 ore di un periodo di 24 ore e in caso contrario inattivo.    

In questo caso, il database viene fatturato per le risorse di calcolo e di archiviazione durante le prime 8 ore.  Anche se il database è inattivo a partire dal secondo orario, viene comunque fatturato per il calcolo nelle 6 ore successive in base al calcolo minimo con provisioning mentre il database è online.  L'archiviazione viene fatturata solo durante il resto del periodo di 24 ore mentre il database è sospeso.

Più precisamente, la fattura di calcolo in questo esempio viene calcolata come segue:

|Intervallo di tempo|VCore usati ogni secondo|GB utilizzati ogni secondo|Dimensione di calcolo fatturata|vCore secondi fatturati nell'intervallo di tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|VCore usato|4 Vcore * 3600 secondi = 14400 vCore secondi|
|1:00-2:00|1|12|Memoria usata|12 GB * 1/3 * 3600 secondi = 14400 vCore secondi|
|2:00-8:00|0|0|Memoria minima sottoposta a provisioning|3 GB * 1/3 * 21600 secondi = 21600 vCore secondi|
|8:00-24:00|0|0|Nessun calcolo fatturato mentre è sospeso|0 vCore secondi|
|Totale vCore secondi fatturati per 24 ore||||50400 vCore secondi|

Si supponga che il prezzo dell'unità di calcolo sia $0.000145/vCore/Second.  Il calcolo fatturato per questo periodo di 24 ore è il prodotto del prezzo unitario di calcolo e vCore secondi fatturati: $0.000145/vCore/secondo * 50400 vCore secondi ~ $7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Vantaggio Azure Hybrid e capacità riservata

Vantaggio Azure Hybrid (vantaggio Azure Hybrid) e gli sconti di capacità riservata non si applicano al livello di calcolo senza server.

## <a name="available-regions"></a>Aree disponibili

Il livello di calcolo senza server è disponibile in tutto il mondo, ad eccezione delle aree seguenti: Cina orientale, Cina settentrionale, Germania centrale, Germania nord-orientale e US Gov centrale (Iowa).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Guida introduttiva: creare un database singolo nel database SQL di Azure usando il portale di Azure](single-database-create-quickstart.md).
- Per i limiti delle risorse, vedere [Limiti delle risorse del livello di calcolo serverless](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
