---
title: Azure SQL Database senza server (anteprima) | Microsoft Docs
description: Questo articolo descrive il nuovo livello di calcolo senza server e lo confronta con il livello di calcolo sottoposte a provisioning esistenti
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
ms.date: 05/07/2019
ms.openlocfilehash: 7f850f309034d128efef89ea842db41d35b8491e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235747"
---
# <a name="sql-database-serverless-preview"></a>Senza server Database SQL (anteprima)

## <a name="what-is-the-serverless-compute-tier"></a>Che cos'è il livello di calcolo senza server

Database SQL senza server (anteprima) è un livello di calcolo che distinte per la quantità di calcolo usata da un database singolo in base al secondo. Un'infrastruttura senza server è ottimizzata per database singoli con i modelli di utilizzo bursty in grado di affrontare un ritardo nella fase di riscaldamento di calcolo dopo i periodi di inattività utilizzo prezzo-prestazioni.
Al contrario, le offerte disponibili pubblicamente nella fattura di oggi, Database SQL per la quantità di calcolano sottoposte a provisioning su base oraria. Questo livello di calcolo sottoposte a provisioning è ottimizzata per i database singoli o elastici prezzo-prestazioni pool con un utilizzo medio più elevato che non può permettersi eventuali ritardi nella fase di riscaldamento di calcolo.

Un database nel livello di computer senza server con i parametri di un ritardo autopause e l'intervallo di calcolo che può usare.

![fatturazione senza server](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Prestazioni

- `MinVcore` e `MaxVcore` configurabile di parametri che definiscono l'intervallo di capacità di calcolo disponibili per il database. Limiti di memoria e IO sono proporzionali all'intervallo vCore specificato.  
- Il ritardo autopause è un parametro configurabile che definisce il periodo di tempo che il database deve essere inattivo prima che verrà automaticamente messo in pausa. Il database viene ripresa automaticamente quando si verifica all'accesso successivo.

### <a name="pricing"></a>Prezzi

- Il costo totale addebitato per un database senza server è la somma della fattura per l'archiviazione e calcolo distinta.
La fatturazione per il calcolo è basata sulla quantità di Vcore usata e memoria utilizzate al secondo.
- Le risorse di calcolo minimo fatturata dipende dal numero minimo di Vcore e memoria minima.
- Mentre il database è sospeso, viene fatturato solo l'archiviazione.

## <a name="scenarios"></a>Scenari

Un'infrastruttura senza server è rapporto prezzo-prestazioni ottimizzata per database singoli con i modelli di utilizzo bursty in grado di affrontare un ritardo nella fase di riscaldamento di calcolo dopo i periodi di inattività di utilizzo. Il livello di calcolo sottoposte a provisioning è rapporto prezzo-prestazioni ottimizzata per un database singolo o in pool con un utilizzo medio più elevato che non può permettersi eventuali ritardi nella fase di riscaldamento di calcolo.

La tabella seguente confronta il livello di calcolo senza server con il livello di calcolo sottoposte a provisioning:

||Elaborazione serverless|Calcolo con provisioning|
|---|---|---|
|**Scenario di utilizzo tipico**|I database con utilizzo imprevedibile, bursty frammisto con periodi di inattività|Database o pool elastici con utilizzo più regolare|
|**Impegno di gestione delle prestazioni**|inferiore|Superiore|
|**Scala di calcolo**|Automatico|Manuale|
|**Calcolare la velocità di risposta**|Dopo periodi di inattività inferiore|Immediato|
|**Granularità di fatturazione**|al secondo|All'ora|
|

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenari particolarmente adatti per il calcolo senza server

- I database singoli con modelli di utilizzo bursty intercalati periodi di inattività possono trarre vantaggio da risparmi prezzo basati sulla fatturazione al secondo per la quantità di calcolo usato.
- Database singoli con richiesta di risorse che è difficile da prevedere e i clienti che preferiscono per delegare il ridimensionamento di calcolo per il servizio.
- Singoli database nel livello di calcolo sottoposte a provisioning che sono di frequente modificano i livelli di prestazioni.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenari particolarmente adatti per il calcolo sottoposte a provisioning

- I database singoli con altre regolari e più sostanziali utilizzo nel corso del tempo di calcolo.
- I database che non possono tollerare prestazioni svantaggi derivanti da più frequenti memoria trimming o ritardo in autoresuming dallo stato di sospensione.
- Più database con modelli di utilizzo bursty che possono essere consolidati in un singolo server e usano i pool elastici per una migliore ottimizzazione dei prezzi.


## <a name="purchasing-model-and-service-tier"></a>Livello di servizio e di modello di acquisto

Il Database SQL senza server è attualmente supportata solo nel livello su hardware di generazione 5 nel modello di acquisto di Vcore per utilizzo generico.

## <a name="autoscaling"></a>Ridimensionamento automatico

### <a name="scaling-responsiveness"></a>Ridimensionamento della velocità di risposta

In generale, i database vengono eseguiti in un computer con una capacità sufficiente per soddisfare la domanda di risorse senza interruzioni di qualsiasi quantità di calcolo richiesta entro i limiti impostati il `maxVcores` valore. In alcuni casi, il bilanciamento del carico automaticamente si verifica se la macchina non riesce a soddisfare la richiesta di risorse in pochi minuti. Il database rimane online durante il caricamento del bilanciamento del carico, ad eccezione di un breve periodo alla fine dell'operazione quando le connessioni vengono rimosse.

### <a name="memory-management"></a>Gestione della memoria

Memoria per i database senza server viene recuperata più spesso rispetto ad un database con provisioning. Questo comportamento è importante per controllare i costi in senza server. A differenza delle risorse di calcolo sottoposte a provisioning, della memoria dalla cache del SQL viene recuperata da un database senza server quando l'utilizzo della CPU o della cache è bassa.

## <a name="autopause-and-autoresume"></a>Autopause e autoresume

### <a name="autopause"></a>Autopause

Autopause viene attivato se tutte le condizioni seguenti sono true per la durata del ritardo autopause:

- Numero sessioni = 0
- CPU = 0 (per utente del carico di lavoro in esecuzione nel pool di utenti)

È disponibile un'opzione per disabilitare autopause se lo si desidera.

### <a name="autoresume"></a>Autoresume

Autoresume viene attivato se una delle condizioni seguenti è vera in qualsiasi momento:

|Funzionalità|Trigger Autoresume|
|---|---|
|Autenticazione e autorizzazione|Accedi|
|Rilevamento delle minacce|Abilitazione/disabilitazione delle impostazioni di rilevamento delle minacce a livello di database o server<br>Modifica le impostazioni di rilevamento delle minacce a livello di database o server|
|Individuazione e classificazione dei dati|Aggiunta, modifica, l'eliminazione o la visualizzazione delle etichette di riservatezza|
|Controllo|Visualizzazione di record di controllo.<br>L'aggiornamento o la visualizzazione di criteri di controllo|
|Maschera dati|Aggiunta, modifica, eliminazione o visualizzando le regole di maschera dati|
|Transparent Data Encryption|Vista stato o stato di transparent data encryption|
|Archivio dati query (prestazioni)|Modifica o la visualizzazione di impostazioni di archivio query. l'ottimizzazione automatica|
|AutoTuning|Verifica delle raccomandazioni autotuning, ad esempio l'indicizzazione automatica e applicazione|
|Copia del database|Creare database come copia<br>Esportare in un file BACPAC|
|Sincronizzazione dati SQL|Sincronizzazione tra database hub e member eseguiti su una pianificazione configurabile o vengono eseguiti manualmente|
|Modifica di alcuni metadati del database|Aggiunta di nuovi tag di database<br>Modifica numero massimo di Vcore, numero minimo di Vcore, ritardo autopause|
|SQL Server Management Studio (SSMS)|Usando SSMS versione 18 e aprendo una nuova finestra di query per tutti i database nel server verranno ripresi qualsiasi database sospesa automaticamente nello stesso server. Questo comportamento non viene eseguito se tramite SSMS versione 17.9.1 con IntelliSense disattivate.|

### <a name="connectivity"></a>Connettività

Se un database senza server è in pausa, il primo accesso riprendere il database e restituire un errore indicante che il database non è disponibile. Una volta che viene ripreso il database, l'account di accesso deve essere riprovata per stabilire la connettività. I client del database con la logica di ripetizione dei tentativi di connessione non è necessario essere modificato.

### <a name="latency"></a>Latenza

La latenza autopause o autoresume un database senza server è in genere nell'ordine 1 minuto.

### <a name="feature-support"></a>Supporto delle funzionalità

Le funzionalità seguenti non supportano autopausing e autoresuming. Vale a dire, se si usano le funzionalità seguenti, il database rimane online indipendentemente dal periodo di inattività del database:

- Replica geografica (attiva la replica geografica e automaticamente gruppi di failover)
- Conservazione backup a lungo termine (conservazione a lungo termine)
- Il database di sincronizzazione usato nella sincronizzazione dati SQL.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>Onboarding nel livello di calcolo senza server

Crea un nuovo database o lo spostamento di che un database esistente a un livello di calcolo senza server segue lo stesso modello come creare un nuovo database in stato effettuato il provisioning a livello di calcolo e prevede i due passaggi seguenti:

1. Specificare il nome obiettivo del servizio. Nella tabella seguente mostra il livello di servizio disponibile e le dimensioni di calcolo attualmente disponibili in anteprima pubblica.

   |Livello di servizio|Dimensioni di calcolo|
   |---|---|
   |Utilizzo generico|GP_S_Gen5_1|
   |Utilizzo generico|GP_S_Gen5_2|
   |Utilizzo generico|GP_S_Gen5_4|

2. Facoltativamente, specificare il ritardo minimo di Vcore e autopause per modificare i valori predefiniti. Nella tabella seguente mostra i valori disponibili per questi parametri.

   |Parametro|Scelte di valore|Valore predefinito|
   |---|---|---|---|
   |Numero minimo di vCore|Uno qualsiasi dei {0.5, 1, 2, 4} che non superano numero massimo di Vcore|0,5 Vcore|
   |Autopause ritardo|Min: 360 minuti (6 ore)<br>Max: 10080 minuti (7 giorni)<br>Incrementi: 60 minuti<br>Disabilitare autopause: -1|360 minuti|

> [!NOTE]
> Usando T-SQL per spostare un database esistente in senza server o modificarne le dimensioni di calcolo non è attualmente supportata, ma può essere eseguita tramite il portale di Azure o PowerShell.

### <a name="create-new-database-using-the-azure-portal"></a>Crea nuovo database usando il portale di Azure

Vedere [Avvio rapido: Creare un singolo database nel Database SQL di Azure usando il portale di Azure](sql-database-single-database-get-started.md).

### <a name="create-new-database-using-powershell"></a>Crea nuovo database con PowerShell

L'esempio seguente crea un nuovo database nel livello di calcolo senza server definito dall'obiettivo di servizio denominato GP_S_Gen5_4 con i valori predefiniti per l'intervallo minimo di Vcore e autopause.

Un'infrastruttura senza server richiede una versione più recente di PowerShell che è attualmente nella raccolta, quindi eseguire `Update-Module Az.Sql` per ottenere i cmdlet più recenti senza server abilitata.

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

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>Spostare database esistente al livello di calcolo senza server

L'esempio seguente sposta un database singolo esistente dal livello di calcolo sottoposte a provisioning nel livello di calcolo senza server. Questo esempio Usa i valori predefiniti per il numero minimo di Vcore, numero massimo di Vcore e autopause ritardo.

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

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>Spostare un database all'esterno del livello di calcolo senza server

Un database senza server può essere spostato in un livello di calcolo sottoposte a provisioning nello stesso modo lo spostamento di un database di calcolo sottoposte a provisioning a un livello di calcolo senza server.

## <a name="modify-serverless-configuration-parameters"></a>Modificare i parametri di configurazione senza server

### <a name="maximum-vcores"></a>Numero massimo di vCore

Modifica il numero massimo di Vcore viene eseguita usando il [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando in PowerShell usando il `MaxVcore` argomento.

### <a name="minimum-vcores"></a>Numero minimo di vCore

Modifica il numero massimo di Vcore viene eseguita usando il [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando in PowerShell usando il `MinVcore` argomento.

### <a name="autopause-delay"></a>Autopause ritardo

Modifica il numero massimo di Vcore viene eseguita usando il [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando in PowerShell usando il `AutoPauseDelay` argomento.

## <a name="monitor-serverless-database"></a>Database senza server di monitoraggio

### <a name="resources-used-and-billed"></a>Risorse utilizzate e fatturato

Le risorse di un database senza server sono incapsulate dalle entità seguenti:

#### <a name="app-package"></a>Pacchetto dell'App

Il pacchetto dell'app è il limite di gestione risorse la maggior parte delle esterno per un database, indipendentemente dal fatto che il database è in un livello di calcolo senza server o con provisioning. Il pacchetto dell'app contiene l'istanza SQL e servizi esterni quell'ambito insieme tutte le risorse di sistema e utente usate da un database nel Database SQL. R e ricerca full-text sono esempi di servizi esterni. L'istanza di SQL in genere domina l'utilizzo delle risorse complessiva tra il pacchetto dell'app.

#### <a name="user-resource-pool"></a>Pool di risorse utente

Il pool di risorse utente è interna la maggior parte dei limiti di gestione risorse per un database, indipendentemente dal fatto che il database è in un livello di calcolo senza server o con provisioning. Il pool di risorse utente definisce l'ambito della CPU e IO per carico di lavoro utente generato dalle query DDL (ad esempio, CREATE, ALTER, e così via) e le query DML (ad esempio, SELECT, INSERT, UPDATE, DELETE, ecc.). Queste query rappresentano in genere l'elemento più sostanziale dell'utilizzo all'interno del pacchetto dell'app.

### <a name="metrics"></a>Metriche

|Entità|Metrica|Descrizione|Unità|
|---|---|---|---|
|Pacchetto dell'App|app_cpu_percent|Percentuale del numero di Vcore usata dall'app rispetto al numero massimo di Vcore consentito per l'app.|Percentuale|
|Pacchetto dell'App|app_cpu_billed|La quantità di calcolo fatturato per l'app durante il periodo di report. L'importo pagato durante questo periodo è il prodotto di questa metrica e il prezzo unitario di vCore.<br>Valori della metrica sono determinati da aggregare nel tempo il numero massimo di CPU usato e memoria utilizzati ogni secondo.<br>Se la quantità utilizzata è inferiore alla quantità minima effettuato il provisioning come set per il numero minimo di Vcore e memoria minima, viene fatturata la quantità minima di provisioning.  Per poter confrontare CPU con la memoria per la fatturazione, memoria verrà normalizzata in unità di Vcore ridimensionando la quantità di memoria in GB da 3 GB per ogni vCore.|vCore secondi|
|Pacchetto dell'App|app_memory_percent|Percentuale di memoria usata dall'app rispetto alla quantità massima di memoria consentita per l'app.|Percentuale|
|Pool di utenti|cpu_percent|Percentuale di Vcore utilizzato dal carico di lavoro utente rispetto al numero massimo di Vcore consentito per il carico di lavoro utente.|Percentuale|
|Pool di utenti|data_IO_percent|Percentuale di dati utilizzato dal carico di lavoro utente rispetto ai dati max operazioni IOPS di IOPS consentito per il carico di lavoro utente.|Percentuale|
|Pool di utenti|log_IO_percent|Percentuale di log usati dal carico di lavoro utente rispetto al log massima MB/s MB/s consentiti per carico di lavoro utente.|Percentuale|
|Pool di utenti|workers_percent|Percentuale di lavori utilizzati dal carico di lavoro utente rispetto al thread di lavoro massimo consentito per il carico di lavoro utente.|Percentuale|
|Pool di utenti|sessions_percent|Percentuale di sessioni utilizzato dal carico di lavoro utente rispetto al numero massimo di sessioni consentite per carico di lavoro utente.|Percentuale|
____

> [!NOTE]
> Le metriche nel portale di Azure sono disponibili nel riquadro di database per un database singolo sotto **Monitoring**.

### <a name="pause-and-resume-status"></a>Stato di sospensione e ripresa

Nel portale di Azure, lo stato del database viene visualizzato nel riquadro della panoramica del server in cui sono elencati i database in esso contenuti. Lo stato del database viene anche visualizzato nel riquadro della panoramica per il database.

Usando il comando PowerShell seguente per eseguire una query la pausa e riprendere lo stato di un database:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limiti delle risorse

Per i limiti delle risorse, vedere [livello di calcolo senza server](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Fatturazione

La quantità di calcolo fatturato ogni secondo è il numero massimo di CPU usata e memoria usata ogni secondo. Se la quantità di CPU usata e memoria utilizzata è inferiore rispetto alla quantità minima di provisioning per ognuno, viene fatturata la quantità sottoposte a provisioning. Per poter confrontare CPU con la memoria per la fatturazione, memoria verrà normalizzata in unità di Vcore ridimensionando la quantità di memoria in GB da 3 GB per ogni vCore.

- **Risorse fatturate**: CPU e memoria
- **Importo fatturato ($)**: prezzo unitario vCore * max (numero minimo di Vcore, Vcore usato, la memoria minima GB * memoria 1/3 GB usato * 1 o 3) 
- **Frequenza di fatturazione**: al secondo

La quantità di calcolo fatturato verrà esposti dalle metriche seguenti:

- **Metrica**: app_cpu_billed (vCore secondi)
- **Definizione**: max (numero minimo di Vcore, Vcore usato, la memoria minima GB * memoria 1/3 GB usato * 1 o 3) *
- **Report frequenza**: Al minuto

> [!NOTE]
> \* Questa quantità viene calcolata ogni secondo e aggregata più di 1 minuto.

**Esempio**: Si consideri un database usando GP_S_Gen5_4 con la sintassi seguente in un periodo di un'ora:

|Tempo (ore: minuti)|app_cpu_billed (vCore secondi)|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||Totale: 1631|

Si supponga che il prezzo unitario calcolo 0.2609/vCore/hour $. Quindi le risorse di calcolo addebitate questo periodo di un'ora viene determinato usando la formula seguente: **$0.2609/vCore/hour * 1631 vCore secondi * 1 ora/3600 secondi = $0.1232**

## <a name="available-regions"></a>Aree disponibili

Il livello di calcolo senza server è disponibile in tutte le aree ad eccezione delle aree geografiche seguenti: Australia centrale, Cina orientale, Cina settentrionale, Francia meridionale, Germania centrale, Germania nord-orientale, India occidentale, Corea meridionale, Sudafrica occidentale, Regno Unito settentrionale, Regno Unito meridionale, Regno Unito occidentale e Stati Uniti centro occidentali

## <a name="next-steps"></a>Passaggi successivi

Per i limiti delle risorse, vedere [limiti delle risorse del livello di calcolo senza server](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).