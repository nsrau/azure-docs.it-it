---
title: Database SQL di Azure serverless (anteprima) | Microsoft Docs
description: Questo articolo descrive il nuovo livello di calcolo serverless e lo confronta con il livello di calcolo con provisioning esistente
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: moslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 08/26/2019
ms.openlocfilehash: 418ca6f8d6258b826bb126252d7cf7b1c5fee299
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035714"
---
# <a name="azure-sql-database-serverless-preview"></a>Database SQL di Azure senza server (anteprima)

Il database SQL di Azure senza server (anteprima) è un livello di calcolo per i database singoli che scala automaticamente le risorse di calcolo in base alla domanda del carico di lavoro e alle fatture per la quantità di calcolo usata al secondo. Il livello di calcolo senza server inoltre sospende automaticamente i database durante i periodi di inattività quando viene addebitata solo l'archiviazione e riprende automaticamente i database quando l'attività restituisce.

## <a name="serverless-compute-tier"></a>Livello di elaborazione serverless

Il livello di calcolo senza server per un singolo database è parametrizzato da un intervallo di scalabilità automatica di calcolo e da un ritardo di sospensione automatica.  La configurazione di questi parametri forma l'esperienza di prestazioni del database e i costi di calcolo.

![Fatturazione serverless](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configurazione delle prestazioni

- Il valore **minimo di Vcore** e il **numero massimo di Vcore** sono parametri configurabili che definiscono l'intervallo di capacità di calcolo disponibile per il database. I limiti di memoria e I/O sono proporzionali all'intervallo vCore specificato.  
- Il **ritardo di sospensione** automatica è un parametro configurabile che definisce il periodo di tempo in cui il database deve rimanere inattivo prima che venga sospeso automaticamente. Il database viene ripreso automaticamente quando si verifica il successivo accesso o un'altra attività.  In alternativa, è possibile disabilitare l'autosospensione.

### <a name="cost"></a>Costi

- Il costo di un database senza server è la somma del costo di calcolo e dei costi di archiviazione.
- Quando l'utilizzo delle risorse di calcolo è compreso tra i limiti minimo e massimo configurati, il costo di calcolo è basato su vCore e sulla memoria usata.
- Quando l'utilizzo delle risorse di calcolo è inferiore ai limiti minimi configurati, il costo di calcolo è basato sul numero minimo di Vcore e sulla memoria minima configurata.
- Quando il database viene sospeso, il costo di calcolo è pari a zero e vengono sostenuti solo i costi di archiviazione.
- Il costo di archiviazione viene determinato in modo analogo al livello di calcolo di cui è stato effettuato il provisioning.

Per informazioni più dettagliate sui costi, vedere [fatturazione](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scenari

Il modello serverless è caratterizzato da un rapporto qualità-prezzo ottimizzato per database singoli con modelli di utilizzo intermittenti e imprevedibili che possono permettersi qualche ritardo all'avvio del calcolo dopo periodi di inattività. Al contrario, il livello di calcolo di cui è stato effettuato il provisioning è ottimizzato a livello di prestazioni per database singoli o più database in pool elastici con un utilizzo medio superiore che non può permettersi un ritardo nel riscaldamento di calcolo.

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
|**Modello di utilizzo del database**| Utilizzo intermittente e non prevedibile con un utilizzo di calcolo medio inferiore nel tempo. |  Modelli di utilizzo più regolari con utilizzo di calcolo medio superiore nel tempo o più database con pool elastici.|
| **Impegno per la gestione delle prestazioni** |Minore|Maggiore|
|**Ridimensionamento delle risorse di calcolo**|Automatico|Manuale|
|**Tempo di risposta per le risorse di calcolo**|Inferiore dopo periodi di inattività|Immediato|
|**Granularità della fatturazione**|Al secondo|All'ora|

## <a name="purchasing-model-and-service-tier"></a>Modello di acquisto e livello di servizio

Il database SQL serverless è attualmente supportato solo nel livello per utilizzo generico su hardware di quinta generazione nel modello di acquisto vCore.

## <a name="autoscaling"></a>Ridimensionamento automatico

### <a name="scaling-responsiveness"></a>Tempo di risposta per il ridimensionamento

In generale, i database senza server vengono eseguiti in un computer dotato di capacità sufficiente per soddisfare la richiesta di risorse senza interruzioni per qualsiasi quantità di calcolo richiesta entro i limiti impostati dal valore massimo di vcore. Di tanto in tanto, viene eseguito automaticamente il bilanciamento del carico se il computer non è in grado di soddisfare la richiesta di risorse entro pochi minuti. Ad esempio, se la richiesta di risorse è 4 Vcore, ma sono disponibili solo 2 Vcore, potrebbero essere necessari alcuni minuti per il bilanciamento del carico prima che vengano forniti 4 vcore. Durante il bilanciamento del carico il database rimane online ad eccezione di un breve periodo al termine dell'operazione, quando vengono rilasciate le connessioni.

### <a name="memory-management"></a>Gestione della memoria

La memoria per i database senza server viene recuperata più frequentemente rispetto ai database di calcolo di cui è stato effettuato il provisioning. Questo comportamento è importante per controllare i costi in senza server e può influisca sulle prestazioni.

#### <a name="cache-reclamation"></a>Recupero della cache

A differenza dei database di calcolo di cui è stato effettuato il provisioning, la memoria della cache SQL viene recuperata da un database senza server quando l'utilizzo della CPU o della cache è basso.

- L'utilizzo della cache è considerato basso quando la dimensione totale delle voci della cache utilizzate più di recente scende al di sotto di una soglia per un determinato periodo di tempo.
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

Le funzionalità seguenti non supportano la sospensione dell'autosospensione.  Ovvero, se viene utilizzata una delle funzionalità seguenti, il database rimane online indipendentemente dalla durata dell'inattività del database:

- Replica geografica (gruppi di replica geografica attiva e failover automatico).
- Conservazione dei backup a lungo termine (LTR).
- Database di sincronizzazione usato nella sincronizzazione dati SQL.

La sospensione dell'autosospensione è temporaneamente bloccata durante la distribuzione di alcuni aggiornamenti dei servizi che richiedono che il database sia online.  In questi casi, la sospensione automatico diventa nuovamente consentita al termine dell'aggiornamento del servizio.

### <a name="autoresuming"></a>Riavvio

La ripresa automatica viene attivata se si verifica una delle condizioni seguenti in qualsiasi momento:

|Funzionalità|Trigger di ripresa automatica|
|---|---|
|Autenticazione e autorizzazione|Accedi|
|Rilevamento delle minacce|Abilitazione o disabilitazione delle impostazioni di rilevamento delle minacce a livello di database o di server.<br>Modifica delle impostazioni di rilevamento delle minacce a livello di database o di server.|
|Individuazione e classificazione dei dati|Aggiunta, modifica, eliminazione o visualizzazione delle etichette di riservatezza|
|Controllo|Visualizzazione dei record di controllo<br>Aggiornamento o visualizzazione dei criteri di controllo.|
|Maschera dati|Aggiunta, modifica, eliminazione o visualizzazione delle regole di maschera dati|
|Transparent Data Encryption|Visualizzazione dello stato di Transparent Data Encryption|
|Esecuzione di query sulle prestazioni dell'archivio dati|Modifica o visualizzazione delle impostazioni dell'archivio dati; ottimizzazione automatica|
|Ottimizzazione automatica|Applicazione e verifica delle indicazioni di ottimizzazione automatica, ad esempio l'indicizzazione automatica|
|Copia del database|Crea database come copia.<br>Esportare in un file BACPAC.|
|Sincronizzazione dati SQL|Sincronizzazione tra database hub e membro che viene eseguita secondo un calendario configurabile o manualmente|
|Modifica di alcuni metadati del database|Aggiunta di nuovi tag del database.<br>Modifica del ritardo massimo Vcore, minimo Vcore o di sospensione.|
|SQL Server Management Studio (SSMS)|L'uso di versioni di SSMS precedenti alla 18,1 e l'apertura di una nuova finestra di query per qualsiasi database nel server riprenderà tutti i database sospesi automaticamente nello stesso server. Questo comportamento non si verifica se si usa SSMS versione 18,1 o successiva.|

La ripresa automatica viene attivata anche durante la distribuzione di alcuni aggiornamenti dei servizi che richiedono che il database sia online.

### <a name="connectivity"></a>Connettività

Se un database senza server viene sospeso, il primo account di accesso riprenderà il database e restituirà un errore indicante che il database non è disponibile con codice di errore 40613. Dopo la ripresa del database è necessario ripetere l'accesso per stabilire la connettività. I client del database con la logica di ripetizione dei tentativi di connessione non devono essere modificati.

### <a name="latency"></a>Latency

La latenza per il riavvio e la sospensione di un database senza server è in genere un ordine di 1 minuto per il riavvio e 1-10 minuti di sospensione.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding nel livello di calcolo senza server

La creazione di un nuovo database o lo trasferimento di un database esistente in un livello di calcolo senza server segue lo stesso modello della creazione di un nuovo database nel livello di calcolo con provisioning e prevede i due passaggi seguenti.

1. Specificare il nome dell'obiettivo di servizio. L'obiettivo di servizio prevede il livello di servizio, la generazione hardware e il numero massimo di vcore. La tabella seguente mostra le opzioni relative all'obiettivo di servizio:

   |Nome dell'obiettivo di servizio|Livello di servizio|Generazione dell'hardware|Numero massimo di vCore|
   |---|---|---|---|
   |GP_S_Gen5_1|Utilizzo generico|Quinta generazione|1|
   |GP_S_Gen5_2|Utilizzo generico|Quinta generazione|2|
   |GP_S_Gen5_4|Utilizzo generico|Quinta generazione|4|

2. Facoltativamente, specificare il ritardo min Vcore e autopause per modificare i valori predefiniti. La tabella seguente illustra i valori disponibili per questi parametri.

   |Parametro|Valori disponibili|Valore predefinito|
   |---|---|---|---|
   |Numero minimo vCore|Uno qualsiasi tra {0,5, 1, 2, 4} purché non superi il numero massimo di vCore|0,5 vCore|
   |Ritardo di sospensione automatica|Minimo: 60 minuti (1 ora)<br>Massimo 10080 minuti (7 giorni)<br>Incrementi: 60 minuti<br>Disabilita la sospensione automatica: -1|60 minuti|

> [!NOTE]
> L'uso di T-SQL per spostare un database esistente in un database serverless o modificarne le dimensioni di calcolo non è attualmente supportato, ma è possibile effettuare queste operazioni tramite il portale di Azure o PowerShell.

### <a name="create-new-database-in-serverless-compute-tier"></a>Crea nuovo database nel livello di calcolo senza server 

#### <a name="use-azure-portal"></a>Usare il portale di Azure

Vedere [Avvio rapido: Creare un database singolo nel database SQL di Azure usando il portale di Azure](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Usare PowerShell

Nell'esempio seguente viene creato un nuovo database nel livello di calcolo senza server.  In questo esempio vengono specificati in modo esplicito il numero minimo e massimo di vCore e il ritardo di sospensione automatica.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Spostare il database dal livello di calcolo di cui è stato effettuato il provisioning nel livello di calcolo senza server

#### <a name="use-powershell"></a>Usare PowerShell

L'esempio seguente sposta un database dal livello di calcolo di cui è stato effettuato il provisioning nel livello di calcolo senza server. In questo esempio vengono specificati in modo esplicito il numero minimo e massimo di vCore e il ritardo di sospensione automatica.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Spostare il database dal livello di calcolo senza server al livello di calcolo con provisioning

La procedura per spostare un database serverless in un livello di calcolo con provisioning è analoga a quella per spostare un database di calcolo con provisioning in un livello di calcolo serverless.

## <a name="modifying-serverless-configuration"></a>Modifica della configurazione senza server

### <a name="maximum-vcores"></a>Numero massimo di vCore

#### <a name="use-powershell"></a>Usare PowerShell

La modifica del numero massimo di Vcore viene eseguita usando il comando [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) in PowerShell `MaxVcore` usando l'argomento.

### <a name="minimum-vcores"></a>Numero minimo di vCore

#### <a name="use-powershell"></a>Usare PowerShell

La modifica del Vcore minimo viene eseguita usando il comando [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) in PowerShell usando l' `MinVcore` argomento.

### <a name="autopause-delay"></a>Ritardo di sospensione automatica

#### <a name="use-powershell"></a>Usare PowerShell

Per modificare il ritardo di sospensione, è necessario usare il comando [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) in PowerShell usando `AutoPauseDelayInMinutes` l'argomento.

## <a name="monitoring"></a>Monitoraggio

### <a name="resources-used-and-billed"></a>Risorse usate e fatturate

Le risorse di un database senza server sono incapsulate dal pacchetto dell'app, dall'istanza di SQL e dalle entità del pool di risorse utente.

#### <a name="app-package"></a>Pacchetto dell'app

Il pacchetto app è il limite più esterno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Il pacchetto dell'app contiene i servizi esterni e l'istanza di SQL che definiscono congiuntamente l'ambito di tutte le risorse utente e di sistema usate da un database nel database SQL. R e la ricerca full-text sono esempi di servizi esterni. L'istanza di SQL è in genere la voce più onerosa nell'utilizzo complessivo delle risorse nel pacchetto dell'app.

#### <a name="user-resource-pool"></a>Pool di risorse utente

Il pool di risorse utente è il limite più interno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Gli ambiti del pool di risorse utente CPU e i/o per il carico di lavoro dell'utente generato da query DDL, ad esempio le query CREATE e ALTER e DML, ad esempio SELECT, INSERT, UPDATE e DELETE. Queste domande rappresentano in genere la percentuale più consistente di utilizzo all'interno del pacchetto dell'app.

### <a name="metrics"></a>metrics

Nella tabella seguente sono elencate le metriche per il monitoraggio dell'utilizzo delle risorse del pacchetto dell'app e del pool di utenti di un database senza server.

|Entità|Metrica|Descrizione|Unità|
|---|---|---|---|
|Pacchetto dell'app|app_cpu_percent|Percentuale del numero di vCore usati dall'app rispetto al numero massimo di vCore consentito per l'app.|Percentuale|
|Pacchetto dell'app|app_cpu_billed|Quantità di risorse di calcolo fatturata per l'app durante il periodo di riferimento. L'importo pagato durante questo periodo è dato dal prodotto di questa metrica per il prezzo unitario dei vCore. <br><br>I valori di questa metrica sono determinati dall'aggregazione nel tempo del numero massimo di CPU usate e dalla memoria usata al secondo. Se la quantità usata è inferiore a quella minima di cui è stato effettuato il provisioning in base al valore impostato per il numero minimo di vCore e la quantità minima di memoria, viene fatturata la quantità minima di cui è stato effettuato il provisioning. Per confrontare la quantità di CPU e di memoria ai fini della fatturazione, la memoria viene normalizzata in unità di vCore ridimensionando la quantità di memoria in GB in base a 3 GB per vCore.|Secondi per vCore|
|Pacchetto dell'app|app_memory_percent|Percentuale di memoria usata dall'app rispetto alla memoria massima consentita per l'app.|Percentuale|
|Pool di utenti|cpu_percent|Percentuale di vCore usati dal carico di lavoro utente rispetto al numero massimo di vCore consentiti per il carico di lavoro utente.|Percentuale|
|Pool di utenti|data_IO_percent|Percentuale di operazioni di I/O al secondo sui dati usate dal carico di lavoro utente rispetto al numero massimo di operazioni di I/O al secondo sui dati consentite per il carico di lavoro utente.|Percentuale|
|Pool di utenti|log_IO_percent|Percentuale di MB/s di log usati dal carico di lavoro utente rispetto al numero massimo di MB/s di log consentiti per il carico di lavoro utente.|Percentuale|
|Pool di utenti|workers_percent|Percentuale di ruoli di lavoro usati dal carico di lavoro utente rispetto al numero massimo di ruoli di lavoro consentiti per il carico di lavoro utente.|Percentuale|
|Pool di utenti|sessions_percent|Percentuale di sessioni usate dal carico di lavoro utente rispetto al numero massimo di sessioni consentite per il carico di lavoro utente.|Percentuale|

### <a name="pause-and-resume-status"></a>Stato di sospensione e ripresa

Nel portale di Azure lo stato del database è visualizzato nel riquadro della panoramica del server in cui sono elencati i database in esso contenuti. Lo stato del database è anche visualizzato nel riquadro della panoramica relativo al database.

Usare usa il comando PowerShell seguente per eseguire una query sullo stato di sospensione e ripresa di un database:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limiti delle risorse

Per i limiti delle risorse, vedere [livello di calcolo senza server](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).

## <a name="billing"></a>Fatturazione

La quantità di risorse di calcolo fatturata corrisponde alla quantità massima di CPU e di memoria usata ogni secondo. Se la quantità di CPU e di memoria usata è inferiore alla quantità minima del provisioning per ognuna, viene fatturata la quantità del provisioning. Per confrontare la quantità di CPU e di memoria ai fini della fatturazione, la memoria viene normalizzata in unità di vCore ridimensionando la quantità di memoria in GB in base a 3 GB per vCore.

- **Risorsa fatturata**: CPU e memoria
- **Importo fatturato**: prezzo unitario vCore * Max (min Vcore, Vcore usato, min memory gb * 1/3, GB di memoria usati * 1/3) 
- **Frequenza di fatturazione**: Al secondo

Prezzo unitario vCore nel costo per ogni vCore al secondo. Per i prezzi unitari in una determinata area, vedere la [pagina dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

La quantità di risorse di calcolo fatturata è esposta dalla metrica seguente:

- **Metrica**: app_cpu_billed (secondi per vCore)
- **Definizione**: massimo (numero minimo di vCore, numero di vCore usati, quantità minima di memoria in GB * 1/3, quantità di memoria in GB usata * 1/3)
- **Frequenza di creazione dei report**: Al minuto

Questa quantità viene calcolata ogni secondo e aggregata in un minuto.

Si consideri un database senza server configurato con 1 min vCore e 4 Vcore max.  Corrisponde a circa 3 GB di memoria minima e a 12 GB di memoria max.  Si supponga che il ritardo di sospensione automatica sia impostato su 6 ore e che il carico di lavoro del database sia attivo nelle prime 2 ore di un periodo di 24 ore e in caso contrario inattivo.    

In questo caso, il database viene fatturato per le risorse di calcolo e di archiviazione durante le prime 8 ore.  Anche se il database è inattivo a partire dal secondo orario, viene comunque fatturato per il calcolo nelle 6 ore successive in base al calcolo minimo con provisioning mentre il database è online.  L'archiviazione viene fatturata solo durante il resto del periodo di 24 ore mentre il database è sospeso.

Più precisamente, la fattura di calcolo in questo esempio viene calcolata come segue:

|Intervallo di tempo|VCore usati ogni secondo|GB utilizzati ogni secondo|Dimensione di calcolo fatturata|vCore secondi fatturati nell'intervallo di tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|VCore usato|4 Vcore * 3600 secondi = 14400 vCore secondi|
|1:00-2:00|1|12|Memoria utilizzata|12 GB * 1/3 * 3600 secondi = 14400 vCore secondi|
|2:00-8:00|0|0|Memoria minima sottoposta a provisioning|3 GB * 1/3 * 21600 secondi = 21600 vCore secondi|
|8:00-24:00|0|0|Nessun calcolo fatturato mentre è sospeso|0 vCore secondi|
|Totale vCore secondi fatturati per 24 ore||||50400 vCore secondi|

Si supponga che il prezzo delle unità di calcolo sia $0,000073/vCore/secondo.  Il calcolo fatturato per questo periodo di 24 ore è il prodotto del prezzo unitario di calcolo e vCore secondi fatturati: $0.000073/vCore/secondo * 50400 vCore secondi = $3,68

## <a name="available-regions"></a>Aree disponibili

Il livello di calcolo senza server è disponibile in tutto il mondo, ad eccezione delle aree seguenti: Australia centrale, Cina orientale, Cina settentrionale, Francia meridionale, Germania centrale, Germania nord-orientale, India occidentale, Corea meridionale, Sudafrica occidentale, Regno Unito settentrionale, Regno Unito meridionale, Regno Unito occidentale e Stati Uniti centro-occidentali.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Avvio rapido: Creare un database singolo nel database SQL di Azure usando il portale di Azure](sql-database-single-database-get-started.md).
- Per i limiti delle risorse, vedere [Limiti delle risorse del livello di calcolo serverless](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).
