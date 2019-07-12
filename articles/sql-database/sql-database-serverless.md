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
ms.date: 07/05/2019
ms.openlocfilehash: 5a1a5ea39c9c0ed8973e1ecfa46977d2d06f83e7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603616"
---
# <a name="azure-sql-database-serverless-preview"></a>Database SQL di Azure senza server (anteprima)

Database SQL di Azure senza server (anteprima) è un livello di calcolo per i database singoli che offre scalabilità automatica di calcolo basata su richiesta di carico di lavoro e le fatture per la quantità di calcolo utilizzate al secondo. Il livello di calcolo senza server sospende automaticamente anche i database durante i periodi di inattività quando solo l'archiviazione viene fatturato e riprende automaticamente i database al termine dell'attività.

## <a name="serverless-compute-tier"></a>Livello di elaborazione serverless

Il livello di calcolo senza server per un database singolo con i parametri di un intervallo di scalabilità automatica di calcolo e un ritardo autopause.  La configurazione di questi parametri forma l'esperienza con prestazioni del database e dei costi di calcolo.

![Fatturazione serverless](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configurazione delle prestazioni

- Il **Vcore minimi** e **Vcore** configurabile di parametri che definiscono l'intervallo di capacità di calcolo disponibili per il database. I limiti di memoria e I/O sono proporzionali all'intervallo vCore specificato.  
- Il **ritardo autopause** è un parametro configurabile che definisce il periodo di tempo il database deve essere inattivo prima che verrà automaticamente messo in pausa. Il database viene ripresa automaticamente quando si verifica all'accesso successivo o un'altra attività.  In alternativa, è possibile disabilitare autopausing.

### <a name="cost"></a>Costi

- Il costo per un database senza server è la somma del costo di calcolo e costi di archiviazione.
- Quando l'utilizzo di calcolo è compreso tra min e max limiti configurati, il costo di calcolo è basato su vCore e memoria usata.
- Quando l'utilizzo di calcolo è di sotto di limiti di min configurati, il costo di calcolo è basato sul numero minimo di Vcore e memoria minima configurata.
- Quando il database è sospeso, il costo di calcolo è uguale a zero e vengono addebitati solo i costi di archiviazione.
- Il costo di archiviazione è determinato esattamente come il livello di calcolo sottoposte a provisioning.

Per altre informazioni dettagliate dei costi, vedere [fatturazione](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scenari

Il modello serverless è caratterizzato da un rapporto qualità-prezzo ottimizzato per database singoli con modelli di utilizzo intermittenti e imprevedibili che possono permettersi qualche ritardo all'avvio del calcolo dopo periodi di inattività. Al contrario, il livello di calcolo sottoposte a provisioning è rapporto prezzo-prestazioni ottimizzata per i database singoli o più database in pool elastici con un utilizzo medio più elevato che non può permettersi eventuali ritardi nella fase di riscaldamento di calcolo.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenari particolarmente adatti per il calcolo serverless

- I database singoli con modelli di utilizzo imprevedibili, intermittente frammisto con periodi di inattività e a basso utilizzo di calcolo Media nel tempo.
- Ridimensionamento per il servizio di calcolo singoli database nel livello di calcolo sottoposte a provisioning che spesso vengono ridimensionate e i clienti che preferiscono per delegare.
- Database singoli nuovo senza la cronologia di utilizzo in cui il ridimensionamento di calcolo è difficile o non è possibile stimare prima della distribuzione nel Database SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenari particolarmente adatti per il calcolo con provisioning

- I database singoli con modelli di utilizzo più regolare e prevedibile e Media superiore utilizzo nel corso del tempo di calcolo.
- Database che non possono tollerare compromessi delle prestazioni dovuti a blocchi di memoria più frequenti o ritardi nella ripresa automatica da uno stato di sospensione.
- Più database con modelli di utilizzo di errori intermittenti o imprevedibili che possono essere consolidati in pool elastici per una migliore ottimizzazione di un rapporto prezzo-prestazioni.

## <a name="comparison-with-provisioned-compute-tier"></a>Confronto con il livello di calcolo con provisioning

La tabella seguente riepiloga le differenze tra il livello di calcolo serverless e il livello di calcolo con provisioning:

| | **Calcolo serverless** | **Calcolo con provisioning** |
|:---|:---|:---|
|**Modello di utilizzo del database**| Utilizzo di errori intermittente o imprevedibile con basso utilizzo di calcolo Media nel tempo. |  I modelli di utilizzo più regolari con Media superiore utilizzo nel tempo, o tra più database con i pool elastici del calcolo.|
| **Impegno per la gestione delle prestazioni** |Minore|Maggiore|
|**Ridimensionamento delle risorse di calcolo**|Automatico|Manuale|
|**Tempo di risposta per le risorse di calcolo**|Inferiore dopo periodi di inattività|Immediato|
|**Granularità della fatturazione**|Al secondo|All'ora|

## <a name="purchasing-model-and-service-tier"></a>Modello di acquisto e livello di servizio

Il database SQL serverless è attualmente supportato solo nel livello per utilizzo generico su hardware di quinta generazione nel modello di acquisto vCore.

## <a name="autoscaling"></a>Scalabilità automatica

### <a name="scaling-responsiveness"></a>Tempo di risposta per il ridimensionamento

In generale, i database senza server vengono eseguiti in un computer con una capacità sufficiente per soddisfare la domanda di risorse senza interruzioni di qualsiasi quantità di calcolo richiesta entro i limiti impostati dal valore di numero massimo di Vcore. Di tanto in tanto, viene eseguito automaticamente il bilanciamento del carico se il computer non è in grado di soddisfare la richiesta di risorse entro pochi minuti. Ad esempio, se la richiesta di risorse è 4 Vcore, ma sono disponibili solo 2 Vcore, quindi potrebbe richiedere fino a qualche minuto per bilanciare il carico prima vengono forniti 4 Vcore per utilizzo. Durante il bilanciamento del carico il database rimane online ad eccezione di un breve periodo al termine dell'operazione, quando vengono rilasciate le connessioni.

### <a name="memory-management"></a>Gestione della memoria

Memoria per i database senza server viene recuperata più frequente per i database di calcolo sottoposte a provisioning. Questo comportamento è importante per controllare i costi in senza server e può influire sulle prestazioni.

#### <a name="cache-reclamation"></a>Recupero della cache

A differenza dei database di calcolo sottoposte a provisioning, della memoria dalla cache del SQL viene recuperata da un database senza server quando l'utilizzo della CPU o della cache è bassa.

- L'uso della cache viene considerata bassa quando la dimensione totale più usati di recente sono di voci della cache sotto una soglia per un periodo di tempo.
- Quando viene attivato il recupero della cache, le dimensioni della cache di destinazione viene ridotta in modo incrementale a una frazione della dimensione precedente e recuperarne continua solo se l'utilizzo rimane basso.
- Quando si verifica il recupero della cache, i criteri per la selezione di voci della cache per la rimozione sono gli stessi criteri di selezione a quelli di calcolo sottoposte a provisioning database quando l'utilizzo della memoria è elevato.
- Le dimensioni della cache non viene ridotto mai inferiore al limite di memoria minimo in base al numero minimo di Vcore che possono essere configurate.

Senza server sia stato eseguito il provisioning nel calcolo i database, le voci possono essere rimosse se viene utilizzata la memoria disponibile tutte le cache.

#### <a name="cache-hydration"></a>Attivazione della cache

La cache SQL aumenta man mano che i dati vengono recuperati dal disco nello stesso modo e con la stessa velocità a quelli di database con provisioning. Quando il database è occupato, la cache è consentita a crescere senza vincoli fino al limite di memoria massima.

## <a name="autopausing-and-autoresuming"></a>Autopausing e autoresuming

### <a name="autopausing"></a>Autopausing

Autopausing viene attivato se tutte le condizioni seguenti sono true per la durata del ritardo autopause:

- Numero di sessioni = 0
- CPU = 0 per carico di lavoro utente in esecuzione nel pool di utente

È disponibile un'opzione per disabilitare autopausing se lo si desidera.

Le funzionalità seguenti non supportano autopausing.  Vale a dire, se si usano le funzionalità seguenti, il database rimane online indipendentemente dal fatto il periodo di inattività del database:

- Replica geografica (gruppi di active geo-replica e failover automatico).
- Conservazione backup a lungo termine (conservazione a lungo termine).
- Database di sincronizzazione usato nella sincronizzazione dati SQL.

Autopausing temporaneamente non è consentita durante la distribuzione di alcuni aggiornamenti dei servizi che richiedono che il database sia online.  In questi casi, autopausing diventa consentita nuovamente dopo aver completato l'aggiornamento del servizio.

### <a name="autoresuming"></a>Autoresuming

Autoresuming viene attivato se una delle condizioni seguenti è vera in qualsiasi momento:

|Funzionalità|Trigger di ripresa automatica|
|---|---|
|Autenticazione e autorizzazione|Login|
|Introduzione al rilevamento delle minacce|Abilitazione/disabilitazione impostazioni di rilevamento delle minacce a livello di database o server.<br>Modifica le impostazioni di rilevamento delle minacce a livello di database o server.|
|Individuazione e classificazione dei dati|Aggiunta, modifica, eliminazione o visualizzazione delle etichette di riservatezza|
|Controllo|Visualizzazione dei record di controllo<br>L'aggiornamento o la visualizzazione di criteri di controllo.|
|Maschera dati|Aggiunta, modifica, eliminazione o visualizzazione delle regole di maschera dati|
|Transparent Data Encryption|Visualizzazione dello stato di Transparent Data Encryption|
|Esecuzione di query sulle prestazioni dell'archivio dati|Modifica o visualizzazione delle impostazioni dell'archivio dati; ottimizzazione automatica|
|Ottimizzazione automatica|Applicazione e verifica delle indicazioni di ottimizzazione automatica, ad esempio l'indicizzazione automatica|
|Copia del database|Creare database come copia.<br>Esportare in un file BACPAC.|
|Sincronizzazione dati SQL|Sincronizzazione tra database hub e membro che viene eseguita secondo un calendario configurabile o manualmente|
|Modifica di alcuni metadati del database|Aggiunta di nuovi tag di database.<br>Modifica numero massimo di Vcore, numero minimo di Vcore o autopause ritardo.|
|SQL Server Management Studio (SSMS)|Se si usa SSMS versione 18 e si apre una nuova finestra di query per un qualsiasi database nel server verranno ripresi tutti i database sospesi automaticamente nello stesso server. Questo comportamento non si verifica se si usa SSMS versione 17.9.1 con la funzionalità IntelliSense disattivata.|

Autoresuming viene anche attivato durante la distribuzione di alcuni aggiornamenti dei servizi che richiedono che il database sia online.

### <a name="connectivity"></a>Connettività

Se un database senza server è in pausa, il primo accesso riprendere il database e restituire un errore indicante che il database non è disponibile con il codice di errore 40613. Dopo la ripresa del database è necessario ripetere l'accesso per stabilire la connettività. I client del database con la logica di ripetizione dei tentativi di connessione non devono essere modificati.

### <a name="latency"></a>Latenza

La latenza autoresume e autopause un database senza server è in genere ordine pari a 1 minuto a autoresume e 1 a 10 minuti per autopause.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding nel livello di calcolo senza server

Crea un nuovo database o lo spostamento di che un database esistente a un livello di calcolo senza server segue lo stesso modello come creare un nuovo database in stato effettuato il provisioning a livello di calcolo e prevede i due passaggi seguenti.

1. Specificare il nome dell'obiettivo di servizio. L'obiettivo di servizio prevede il livello di servizio, la generazione di hardware e numero massimo di Vcore. La tabella seguente mostra le opzioni relative all'obiettivo di servizio:

   |Nome dell'obiettivo di servizio|Livello di servizio|Generazione dell'hardware|Numero massimo di vCore|
   |---|---|---|---|
   |GP_S_Gen5_1|Utilizzo generico|Quinta generazione|1|
   |GP_S_Gen5_2|Utilizzo generico|Quinta generazione|2|
   |GP_S_Gen5_4|Utilizzo generico|Quinta generazione|4|

2. Facoltativamente, specificare l'intervallo minimo di Vcore e autopause per modificare i valori predefiniti. La tabella seguente illustra i valori disponibili per questi parametri.

   |Parametro|Valori disponibili|Valore predefinito|
   |---|---|---|---|
   |Numero minimo di Vcore|Uno qualsiasi tra {0,5, 1, 2, 4} purché non superi il numero massimo di vCore|0,5 vCore|
   |Ritardo di sospensione automatica|Minimo: 60 minuti (1 ora)<br>Massima: 10080 minuti (7 giorni)<br>Incrementi: 60 minuti<br>Disabilita la sospensione automatica: -1|60 minuti|

> [!NOTE]
> L'uso di T-SQL per spostare un database esistente in un database serverless o modificarne le dimensioni di calcolo non è attualmente supportato, ma è possibile effettuare queste operazioni tramite il portale di Azure o PowerShell.

### <a name="create-new-database-in-serverless-compute-tier"></a>Crea nuovo database nel livello di calcolo senza server 

#### <a name="use-azure-portal"></a>Usare il portale di Azure

Vedere [Avvio rapido: Creare un database singolo nel database SQL di Azure usando il portale di Azure](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Usare PowerShell

L'esempio seguente crea un nuovo database nel livello di calcolo senza server.  In questo esempio vengono specificati in modo esplicito il numero minimo e massimo di vCore e il ritardo di sospensione automatica.

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

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Spostare i database dal livello di calcolo sottoposte a provisioning a livello di calcolo senza server

#### <a name="use-powershell"></a>Usare PowerShell

L'esempio seguente sposta un database dal livello di calcolo sottoposte a provisioning nel livello di calcolo senza server. In questo esempio vengono specificati in modo esplicito il numero minimo e massimo di vCore e il ritardo di sospensione automatica.

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

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Spostare i database dal livello di calcolo senza server a livello di calcolo sottoposte a provisioning

La procedura per spostare un database serverless in un livello di calcolo con provisioning è analoga a quella per spostare un database di calcolo con provisioning in un livello di calcolo serverless.

## <a name="modifying-serverless-configuration"></a>Modifica della configurazione senza server

### <a name="maximum-vcores"></a>Numero massimo di vCore

#### <a name="use-powershell"></a>Usare PowerShell

Modifica il numero massimo di Vcore viene eseguita usando il [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando in PowerShell usando il `MaxVcore` argomento.

### <a name="minimum-vcores"></a>Numero minimo di vCore

#### <a name="use-powershell"></a>Usare PowerShell

Modifica il numero minimo di Vcore viene eseguita usando il [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando in PowerShell usando il `MinVcore` argomento.

### <a name="autopause-delay"></a>Ritardo di sospensione automatica

#### <a name="use-powershell"></a>Usare PowerShell

Modifica del ritardo autopause viene eseguita usando il [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando in PowerShell usando il `AutoPauseDelayInMinutes` argomento.

## <a name="monitoring"></a>Monitoraggio

### <a name="resources-used-and-billed"></a>Risorse usate e fatturate

Pacchetto dell'app, istanza di SQL Server e le entità del pool di risorse utente sono incapsulate le risorse di un database senza server.

#### <a name="app-package"></a>Pacchetto dell'app

Il pacchetto app è il limite più esterno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Il pacchetto dell'app contiene i servizi esterni e l'istanza di SQL che definiscono congiuntamente l'ambito di tutte le risorse utente e di sistema usate da un database nel database SQL. R e la ricerca full-text sono esempi di servizi esterni. L'istanza di SQL è in genere la voce più onerosa nell'utilizzo complessivo delle risorse nel pacchetto dell'app.

#### <a name="user-resource-pool"></a>Pool di risorse utente

Il pool di risorse utente è il limite più interno di gestione delle risorse per un database, indipendentemente dal fatto che il database si trovi in un livello di calcolo serverless o con provisioning. Utente del pool gli ambiti di risorse della CPU e IO per carico di lavoro utente generato dalle query DDL, ad esempio le query CREATE e ALTER e DML come selezionare, inserire, aggiornare ed eliminare. Queste domande rappresentano in genere la percentuale più consistente di utilizzo all'interno del pacchetto dell'app.

### <a name="metrics"></a>metrics

Nella tabella seguente sono elencate le metriche per monitorare l'utilizzo delle risorse del pool di utente e del pacchetto di app di un database senza server:

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

Per i limiti delle risorse, vedere [Livello di calcolo serverless](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).

## <a name="billing"></a>Fatturazione

La quantità di risorse di calcolo fatturata corrisponde alla quantità massima di CPU e di memoria usata ogni secondo. Se la quantità di CPU e di memoria usata è inferiore alla quantità minima del provisioning per ognuna, viene fatturata la quantità del provisioning. Per confrontare la quantità di CPU e di memoria ai fini della fatturazione, la memoria viene normalizzata in unità di vCore ridimensionando la quantità di memoria in GB in base a 3 GB per vCore.

- **Risorsa fatturata**: CPU e memoria
- **Importo fatturato**: prezzo unitario vCore * max (numero minimo di Vcore, Vcore usato, la memoria minima GB * memoria 1/3 GB usato * 1 o 3) 
- **Frequenza di fatturazione**: Al secondo

Il prezzo unitario di vCore del costo per ogni vCore al secondo. Per i prezzi unitari in una determinata area, vedere la [pagina dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

La quantità di risorse di calcolo fatturata è esposta dalla metrica seguente:

- **Metrica**: app_cpu_billed (secondi per vCore)
- **Definizione**: massimo (numero minimo di vCore, numero di vCore usati, quantità minima di memoria in GB * 1/3, quantità di memoria in GB usata * 1/3)
- **Frequenza di creazione dei report**: Al minuto

Questa quantità viene calcolata ogni secondo e aggregata in un minuto.

Si consideri un database senza server configurati con 1 min vCore e 4 Vcore per utilizzo massimi.  Ciò corrisponde a circa 3 GB di memoria minimo e massimo 12 GB di memoria.  Si supponga che il ritardo di sospensione automatica è impostato su 6 ore e il carico di lavoro di database sia attivo durante le ore 2 prima di un periodo di 24 ore e inattive in caso contrario.    

In questo caso, il database viene fatturato per l'archiviazione e calcolo durante le prime 8 ore.  Anche se il database è inattivo a partire da dopo dalla seconda ora, viene comunque fatturata per il calcolo nelle ore successive 6 basato su attività di calcolo minimo effettuato il provisioning quando il database è online.  Solo archiviazione sarà addebitata fino alla fine del periodo di 24 ore mentre il database è sospeso.

Più precisamente, la fattura di calcolo in questo esempio viene calcolata come segue:

|Intervallo di tempo|numero di Vcore utilizzati ogni secondo|GB utilizzati ogni secondo|Calcolo di dimensione fatturato|secondi di vCore fatturati intervallo di tempo|
|---|---|---|---|---|
|0:00-1:00|4|9|numero di Vcore utilizzati|4 Vcore * 3600 secondi = 14.400 secondi di vCore|
|1:00-2:00|1|12|Memoria utilizzata|12 GB * 1 o 3 * 3.600 secondi = 14400 vCore secondi|
|2:00-8:00|0|0|Memoria minima effettuato il provisioning|3 GB * 1 o 3 * 21600 secondi = 21600 vCore secondi|
|8:00-24:00|0|0|Nessun calcolo fatturato mentre in pausa|vCore 0 secondi|
|Totale vCore secondi fatturati più di 24 ore||||vCore 50400 secondi|

Si supponga che il prezzo delle unità di calcolo sia $0,000073/vCore/secondo.  Le risorse di calcolo addebitate questo periodo di 24 ore è il prodotto dei calcolo unit price vCore secondi e fatturata: $0.000073/vCore/second * 50400 vCore secondi = $3.68

## <a name="available-regions"></a>Aree disponibili

Il livello di calcolo senza server è disponibile in tutto il mondo, ad eccezione delle aree geografiche seguenti: Australia centrale, Cina orientale, Cina settentrionale, Francia meridionale, Germania centrale, Germania nord-orientale, India occidentale, Corea meridionale, Sudafrica occidentale, Regno Unito settentrionale, Regno Unito meridionale, Regno Unito occidentale e Stati Uniti centro occidentali.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Avvio rapido: Creare un database singolo nel database SQL di Azure usando il portale di Azure](sql-database-single-database-get-started.md).
- Per i limiti delle risorse, vedere [Limiti delle risorse del livello di calcolo serverless](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
