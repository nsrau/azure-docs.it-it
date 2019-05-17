---
title: Database SQL di Azure serverless (anteprima) | Microsoft Docs
description: Questo articolo descrive il nuovo livello di calcolo serverless e lo confronta con il livello di calcolo con provisioning esistente
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/11/2019
ms.openlocfilehash: 72552f6335f3ad6742679708a639634362c49c0b
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823307"
---
# <a name="sql-database-serverless-preview"></a>Database SQL serverless (anteprima)

## <a name="what-is-the-serverless-compute-tier"></a>Informazioni sul livello di calcolo serverless

Database SQL serverless (anteprima) è un livello di calcolo che addebita le risorse di calcolo usate da un database singolo in base al numero di secondi. Il modello serverless è caratterizzato da un rapporto qualità-prezzo ottimizzato per database singoli con modelli di utilizzo intermittenti e imprevedibili che possono permettersi qualche ritardo all'avvio del calcolo dopo periodi di inattività.

Per definire i parametri di un database nel livello di calcolo serverless, si considerano l'intervallo di calcolo utilizzabile e un ritardo di sospensione automatica.

![Fatturazione serverless](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Prestazioni

- `MinVcore` e `MaxVcore` sono i parametri configurabili che definiscono l'intervallo della capacità di calcolo disponibile per il database. I limiti di memoria e I/O sono proporzionali all'intervallo vCore specificato.  
- Il ritardo di sospensione automatica è un parametro configurabile che definisce il periodo di tempo in cui il database deve rimanere inattivo prima che venga messo in pausa automaticamente. Il database viene ripristinato automaticamente in seguito all'accesso successivo.

### <a name="pricing"></a>Prezzi

- Il totale della fattura relativa a un database serverless corrisponde alla somma della fattura per le risorse di calcolo e della fattura per le risorse di archiviazione.
La fatturazione per le risorse di calcolo si basa sulla quantità di vCore usati e sulla memoria usata al secondo.
- Il numero minimo di risorse di calcolo fatturate si basa sul numero minimo di vCore e sulla quantità minima di memoria.
- Durante la sospensione del database vengono addebitate solo le risorse di archiviazione.

## <a name="scenarios"></a>Scenari

Il modello serverless è caratterizzato da un rapporto qualità-prezzo ottimizzato per database singoli con modelli di utilizzo intermittenti e imprevedibili che possono permettersi qualche ritardo all'avvio del calcolo dopo periodi di inattività. Al contrario, il livello di calcolo con provisioning è caratterizzato da un rapporto prezzo-prestazioni ottimizzato per database singoli o in pool con un utilizzo medio più elevato che non possono permettersi eventuali ritardi nella fase di avvio del calcolo.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenari particolarmente adatti per il calcolo serverless

- Database singoli con modelli di utilizzo intermittente e imprevedibile, intervallati da periodi di inattività, che possono trarre vantaggio dalla fatturazione al secondo e basata sulla quantità di risorse di calcolo usate.
- Database singoli in cui la richiesta di risorse è difficile da prevedere e clienti che preferiscono delegare al servizio il ridimensionamento delle risorse di calcolo.
- Database singoli nel livello di elaborazione con provisioning che cambiano spesso livello di prestazioni.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenari particolarmente adatti per il calcolo con provisioning

- Database singoli con un utilizzo delle risorse di calcolo più regolare e costante nel tempo.
- Database che non possono tollerare compromessi delle prestazioni dovuti a blocchi di memoria più frequenti o ritardi nella ripresa automatica da uno stato di sospensione.
- Più database con modelli di utilizzo intermittenti e imprevedibili che possono essere consolidati in un unico server e usano pool elastici per una migliore ottimizzazione dei prezzi.

## <a name="comparison-with-provisioned-compute-tier"></a>Confronto con il livello di calcolo con provisioning

La tabella seguente riepiloga le differenze tra il livello di calcolo serverless e il livello di calcolo con provisioning:

| | **Calcolo serverless** | **Calcolo con provisioning** |
|:---|:---|:---|
|**Scenario di utilizzo tipico**| Database con utilizzo intermittente e imprevedibile, intervallato da periodo di inattività. | Database o pool elastici caratterizzati da un utilizzo più regolare.|
| **Impegno per la gestione delle prestazioni** |Minore|Maggiore|
|**Ridimensionamento delle risorse di calcolo**|Automatico|Manuale|
|**Tempo di risposta per le risorse di calcolo**|Inferiore dopo periodi di inattività|Immediato|
|**Granularità della fatturazione**|Al secondo|All'ora|

## <a name="purchasing-model-and-service-tier"></a>Modello di acquisto e livello di servizio

Il database SQL serverless è attualmente supportato solo nel livello per utilizzo generico su hardware di quinta generazione nel modello di acquisto vCore.

## <a name="autoscaling"></a>Ridimensionamento automatico

### <a name="scaling-responsiveness"></a>Tempo di risposta per il ridimensionamento

In generale, i database vengono eseguiti in un computer con capacità sufficiente a soddisfare la richiesta di risorse senza interruzioni per qualsiasi quantità di risorse di calcolo richiesta entro i limiti fissati dal valore `maxVcores`. Di tanto in tanto, viene eseguito automaticamente il bilanciamento del carico se il computer non è in grado di soddisfare la richiesta di risorse entro pochi minuti. Durante il bilanciamento del carico il database rimane online ad eccezione di un breve periodo al termine dell'operazione, quando vengono rilasciate le connessioni.

### <a name="memory-management"></a>Gestione della memoria

La memoria per i database serverless viene recuperata con maggiore frequenza rispetto ai database con provisioning. Questo comportamento è importante per controllare i costi nei database serverless. A differenza delle risorse di calcolo con provisioning, la memoria della cache SQL viene recuperata da un database serverless quando l'utilizzo della CPU o della cache è basso.

## <a name="autopause-and-autoresume"></a>Sospensione automatica e ripresa automatica

### <a name="autopause"></a>Sospensione automatica

La sospensione automatica viene attivata in presenza di tutte le condizioni seguenti per la durata del ritardo di sospensione automatica:

- Numero di sessioni = 0
- CPU = 0 (per il carico di lavoro utente in esecuzione nel pool di utenti)

Se si desidera, è disponibile un'opzione per disabilitare la sospensione automatica.

### <a name="autoresume"></a>Ripresa automatica

La ripresa automatica viene attivata in presenza di una qualsiasi delle condizioni seguenti in un qualsiasi momento:

|Funzionalità|Trigger di ripresa automatica|
|---|---|
|Autenticazione e autorizzazione|Accedi|
|Rilevamento delle minacce|Abilitazione/disabilitazione delle impostazioni di rilevamento delle minacce a livello di database o server<br>Modifica delle impostazioni di rilevamento delle minacce a livello di database o server|
|Individuazione e classificazione dei dati|Aggiunta, modifica, eliminazione o visualizzazione delle etichette di riservatezza|
|Controllo|Visualizzazione dei record di controllo<br>Aggiornamento o visualizzazione di criteri di controllo|
|Maschera dati|Aggiunta, modifica, eliminazione o visualizzazione delle regole di maschera dati|
|Transparent Data Encryption|Visualizzazione dello stato di Transparent Data Encryption|
|Esecuzione di query sulle prestazioni dell'archivio dati|Modifica o visualizzazione delle impostazioni dell'archivio dati; ottimizzazione automatica|
|Ottimizzazione automatica|Applicazione e verifica delle indicazioni di ottimizzazione automatica, ad esempio l'indicizzazione automatica|
|Copia del database|Creazione del database come copia<br>Esportazione in un file BACPAC|
|Sincronizzazione dati SQL|Sincronizzazione tra database hub e membro che viene eseguita secondo un calendario configurabile o manualmente|
|Modifica di alcuni metadati del database|Aggiunta di nuovi tag del database<br>Modifica del numero massimo e minimo di vCore e del ritardo di sospensione automatica|
|SQL Server Management Studio (SSMS)|Se si usa SSMS versione 18 e si apre una nuova finestra di query per un qualsiasi database nel server verranno ripresi tutti i database sospesi automaticamente nello stesso server. Questo comportamento non si verifica se si usa SSMS versione 17.9.1 con la funzionalità IntelliSense disattivata.|

### <a name="connectivity"></a>Connettività

Se un database serverless è in pausa, verrà ripreso al primo accesso. Verrà inoltre restituito un errore con codice 40613 che indica che il database non è disponibile. Dopo la ripresa del database è necessario ripetere l'accesso per stabilire la connettività. I client del database con la logica di ripetizione dei tentativi di connessione non devono essere modificati.

### <a name="latency"></a>Latenza

La latenza per la sospensione o la ripresa automatica di un database serverless è in genere nell'ordine di 1 minuto.

### <a name="feature-support"></a>Supporto delle funzionalità

Le funzionalità seguenti non supportano la sospensione e la ripresa automatica. Se quindi si usa una delle funzionalità seguenti, il database rimane online indipendentemente dall'intervallo di inattività:

- Replica geografica (replica geografica attiva e gruppi di failover automatico)
- Conservazione a lungo termine del backup
- Database di sincronizzazione usato nella sincronizzazione dati SQL.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>Onboarding nel livello di calcolo serverless

La procedura di creazione di un nuovo database o di spostamento di un database esistente in un livello di calcolo serverless è analoga a quella di creazione di un nuovo database nel livello di calcolo con provisioning e prevede i due passaggi seguenti:

1. Specificare il nome dell'obiettivo di servizio. L'obiettivo di servizio descrive il livello di servizio, la generazione dell'hardware e il numero massimo di vCore. La tabella seguente mostra le opzioni relative all'obiettivo di servizio:

   |Nome dell'obiettivo di servizio|Livello di servizio|Generazione dell'hardware|Numero massimo di vCore|
   |---|---|---|---|
   |GP_S_Gen5_1|Utilizzo generico|Quinta generazione|1|
   |GP_S_Gen5_2|Utilizzo generico|Quinta generazione|2|
   |GP_S_Gen5_4|Utilizzo generico|Quinta generazione|4|

2. Facoltativamente, specificare il numero minimo di vCore e il ritardo di sospensione automatica per modificarne i valori predefiniti. La tabella seguente illustra i valori disponibili per questi parametri.

   |Parametro|Valori disponibili|Valore predefinito|
   |---|---|---|---|
   |Numero minimo di vCore|Uno qualsiasi tra {0,5, 1, 2, 4} purché non superi il numero massimo di vCore|0,5 vCore|
   |Ritardo di sospensione automatica|Min: 360 minuti (6 ore)<br>Max: 10080 minuti (7 giorni)<br>Incrementi: 60 minuti<br>Disabilita la sospensione automatica: -1|360 minuti|

> [!NOTE]
> L'uso di T-SQL per spostare un database esistente in un database serverless o modificarne le dimensioni di calcolo non è attualmente supportato, ma è possibile effettuare queste operazioni tramite il portale di Azure o PowerShell.

### <a name="create-new-database-using-the-azure-portal"></a>Creare il nuovo database con il portale di Azure

Vedere [Avvio rapido: Creare un database singolo nel database SQL di Azure usando il portale di Azure](sql-database-single-database-get-started.md).

### <a name="create-new-database-using-powershell"></a>Creare il nuovo database con PowerShell

Nell'esempio seguente viene creato un nuovo database nel livello di calcolo serverless definito dall'obiettivo di servizio denominato GP_S_Gen5_4 con i valori predefiniti per il numero minimo di vCore e il ritardo di sospensione automatica.

Con il livello serverless è richiesta una versione di PowerShell più recente rispetto a quella attualmente presente nella raccolta, di conseguenza eseguire `Update-Module Az.Sql` per ottenere i cmdlet più recenti abilitati per il livello serverless.

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
  -AutoPauseDelay 720
```

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>Spostare il database esistente nel livello di calcolo serverless

Nell'esempio seguente un database singolo esistente viene spostato dal livello di calcolo con provisioning al livello di calcolo serverless. In questo esempio vengono specificati in modo esplicito il numero minimo e massimo di vCore e il ritardo di sospensione automatica.

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
  -AutoPauseDelay 1440
```

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>Spostare un database dal livello di calcolo serverless

La procedura per spostare un database serverless in un livello di calcolo con provisioning è analoga a quella per spostare un database di calcolo con provisioning in un livello di calcolo serverless.

## <a name="modify-serverless-configuration-parameters"></a>Modificare i parametri di configurazione serverless

### <a name="maximum-vcores"></a>Numero massimo di vCore

Per modificare il numero massimo di vCore, si usa il comando [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) in PowerShell con l'argomento `MaxVcore`.

### <a name="minimum-vcores"></a>Numero minimo di vCore

Modifica il numero minimo di Vcore viene eseguita usando il [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando in PowerShell usando il `MinVcore` argomento.

### <a name="autopause-delay"></a>Ritardo di sospensione automatica

Modifica del ritardo autopause viene eseguita usando il [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando in PowerShell usando il `AutoPauseDelay` argomento.

## <a name="monitor-serverless-database"></a>Monitorare il database serverless

### <a name="resources-used-and-billed"></a>Risorse usate e fatturate

Le risorse di un database serverless sono incapsulate dalle entità seguenti:

#### <a name="app-package"></a>Pacchetto dell'app

Il pacchetto app è il limite più esterno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Il pacchetto dell'app contiene i servizi esterni e l'istanza di SQL che definiscono congiuntamente l'ambito di tutte le risorse utente e di sistema usate da un database nel database SQL. R e la ricerca full-text sono esempi di servizi esterni. L'istanza di SQL è in genere la voce più onerosa nell'utilizzo complessivo delle risorse nel pacchetto dell'app.

#### <a name="user-resource-pool"></a>Pool di risorse utente

Il pool di risorse utente è il limite più interno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Il pool di risorse utente definisce l'ambito della CPU e dell'I/O per il carico di lavoro utente generato da query DDL (ad esempio CREATE, ALTER e così via) e query DML (ad esempio SELECT, INSERT, UPDATE, DELETE e così via). Queste domande rappresentano in genere la percentuale più consistente di utilizzo all'interno del pacchetto dell'app.

### <a name="metrics"></a>Metriche

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
____

> [!NOTE]
> Nel portale di Azure le metriche relative a un database singolo sono disponibili nel riquadro del database in **Monitoraggio**.

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

Per i limiti delle risorse, vedere [Livello di calcolo serverless](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).

## <a name="billing"></a>Fatturazione

La quantità di risorse di calcolo fatturata corrisponde alla quantità massima di CPU e di memoria usata ogni secondo. Se la quantità di CPU e di memoria usata è inferiore alla quantità minima del provisioning per ognuna, viene fatturata la quantità del provisioning. Per confrontare la quantità di CPU e di memoria ai fini della fatturazione, la memoria viene normalizzata in unità di vCore ridimensionando la quantità di memoria in GB in base a 3 GB per vCore.

- **Risorsa fatturata**: CPU e memoria
- **Importo fatturato ($)**: prezzo unitario vCore * massimo (numero minimo di vCore, numero di vCore usati, quantità minima di memoria in GB * 1/3, quantità di memoria in GB usata * 1/3) 
- **Frequenza di fatturazione**: Al secondo

Prezzo unitario del vCore nel costo per vCore al secondo. Per i prezzi unitari in una determinata area, vedere la [pagina dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

La quantità di risorse di calcolo fatturata è esposta dalla metrica seguente:

- **Metrica**: app_cpu_billed (secondi per vCore)
- **Definizione**: massimo (numero minimo di vCore, numero di vCore usati, quantità minima di memoria in GB * 1/3, quantità di memoria in GB usata * 1/3)
- **Frequenza di creazione dei report**: Al minuto

Questa quantità viene calcolata ogni secondo e aggregata in un minuto.

Si consideri un database senza server configurati con 1 min vcore e 4 Vcore per utilizzo massimi.  Ciò corrisponde a circa 3 GB di memoria minimo e massimo 12 GB di memoria.  Si supponga che il ritardo di sospensione automatica è impostato su 6 ore e il carico di lavoro di database sia attivo durante le ore 2 prima di un periodo di 24 ore su 24 e inattive in caso contrario.    

In questo caso, il database viene fatturato per l'archiviazione e calcolo durante le prime 8 ore.  Anche se il database è inattivo avvio dopo l'ora 2nd, viene comunque fatturata per il calcolo nelle ore successive 6 basato su attività di calcolo minimo effettuato il provisioning quando il database è online.  Solo archiviazione sarà addebitata fino alla fine del periodo di 24 ore mentre il database è sospeso.

Più precisamente, la fattura di calcolo in questo esempio viene calcolata come segue:

|Intervallo di tempo|numero di Vcore utilizzati ogni secondo|GB utilizzati ogni secondo|Calcolo di dimensione fatturato|secondi di vCore fatturati intervallo di tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|numero di Vcore utilizzati|4 Vcore * 3600 secondi = 14.400 secondi di vCore|
|1:00-2:00|1|12|Memoria utilizzata|12 Gb * 1 o 3 * 3.600 secondi = 14400 vCore secondi|
|2:00-8:00|0|0|Memoria minima effettuato il provisioning|3 Gb * 1 o 3 * 21600 secondi = 21600 vCore secondi|
|8:00-24:00|0|0|Nessun calcolo fatturato mentre in pausa|vCore 0 secondi|
|Totale vCore secondi fatturati più di 24 ore||||vCore 50400 secondi|

Si supponga che il prezzo delle unità di calcolo sia $0,000073/vCore/secondo.  Le risorse di calcolo addebitate questo periodo di 24 ore è il prodotto dei calcolo unit price vcore secondi e fatturata: $0.000073/vCore/second * 50400 vCore secondi = $3.68

## <a name="available-regions"></a>Aree disponibili

Il livello di calcolo serverless è disponibile in tutte le aree ad eccezione delle seguenti: Australia centrale, Cina orientale, Cina settentrionale, Francia meridionale, Germania centrale, Germania nord-orientale, India occidentale, Corea meridionale, Sudafrica occidentale, Regno Unito settentrionale, Regno Unito meridionale, Regno Unito occidentale e Stati Uniti centro-occidentali

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Avvio rapido: Creare un database singolo nel database SQL di Azure usando il portale di Azure](sql-database-single-database-get-started.md).
- Per i limiti delle risorse, vedere [Limiti delle risorse del livello di calcolo serverless](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
