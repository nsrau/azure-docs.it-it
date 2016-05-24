<properties
	pageTitle="Usare Analisi archiviazione per raccogliere dati di log e metriche | Microsoft Azure"
	description="Analisi archiviazione consente di tenere traccia dei dati delle metriche per tutti i servizi di archiviazione e di raccogliere i log per l'archiviazione di BLOB, code e tabelle."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="robinsh"/>

# Analisi archiviazione

## Panoramica

Analisi archiviazione di Azure esegue la registrazione e fornisce le metriche dei dati per un account di archiviazione. È possibile utilizzare questi dati per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione.

Per utilizzare Analisi archiviazione, è necessario abilitarla singolarmente per ciascun servizio che si desidera monitorare. È possibile abilitarlo dal [portale di Azure](https://portal.azure.com). Per informazioni dettagliate, vedere [Monitorare un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Usare le operazioni [Get Blob Service Properties](https://msdn.microsoft.com/library/hh452239.aspx), [Get Queue Service Properties](https://msdn.microsoft.com/library/hh452243.aspx), [Get Table Service Properties](https://msdn.microsoft.com/library/hh452238.aspx) e [Get File Service Properties](https://msdn.microsoft.com/library/mt427369.aspx) per abilitare Analisi archiviazione per ciascun servizio.

I dati aggregati vengono archiviati in un BLOB noto (per la registrazione) e in tabelle note (per le metriche), a cui è possibile accedere tramite le API del servizio BLOB e del servizio tabelle.

Analisi archiviazione può archiviare un massimo di 20 TB di dati. Tale limite è indipendente dal limite totale dell'account di archiviazione. Per ulteriori informazioni sulla fatturazione e sui criteri di conservazione dei dati, vedere [Analisi archiviazione e fatturazione](https://msdn.microsoft.com/library/hh360997.aspx). Per informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## Informazioni sulla registrazione di Analisi archiviazione

Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo.

Le voci di log vengono create solo in presenza di attività del servizio di archiviazione. Se, ad esempio, un account di archiviazione presenta un'attività nel servizio BLOB, ma non nei servizi di tabelle o di accodamento, saranno creati solo log relativi al servizio BLOB.

La registrazione di Analisi archiviazione non è disponibile per Servizio file di Azure.

### Registrazione delle richieste autenticate

Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite

- Richieste non riuscite, tra cui timeout, limitazione, rete, autorizzazione e altri errori

- Richieste tramite una firma di accesso condiviso, incluse le richieste riuscite e non riuscite

- Richieste ai dati di analisi

Le richieste eseguite dalla stessa Analisi archiviazione, ad esempio, la creazione oppure l'eliminazione di log, non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx) e [Formato log Analisi archiviazione](https://msdn.microsoft.com/library/hh343259.aspx).

### Registrazione di richieste anonime
Vengono registrati i seguenti tipi di richieste anonime:

- Richieste riuscite

- Errori server

- Errori di timeout per client e server

- Richieste GET non riuscite con codice di errore 304 (non modificate)

Tutte le altre richieste anonime non riuscite non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx) e [Formato log Analisi archiviazione](https://msdn.microsoft.com/library/hh343259.aspx).

### Come vengono archiviati i log
Tutti i log vengono archiviati in BLOB di blocchi in un contenitore denominato $logs, che viene creato automaticamente quando viene abilitata Analisi archiviazione per un account di archiviazione. Il contenitore $logs si trova nello spazio dei nomi BLOB dell'account di archiviazione, ad esempio: `http://<accountname>.blob.core.windows.net/$logs`. Questo contenitore non può essere eliminato una volta abilitata Analisi di archiviazione, sebbene sia possibile eliminarne il contenuto.

>[Azure.NOTE] Il contenitore $logs non viene visualizzato quando viene eseguita un'operazione di inclusione nell'elenco del contenitore, ad esempio, il metodo [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx). È necessario effettuare l'accesso diretto. Ad esempio, è possibile utilizzare il metodo [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) per accedere ai BLOB nel contenitore `$logs`. Nel momento in cui vengono registrate le richieste, Analisi archiviazione carica i risultati intermedi come blocchi. Analisi archiviazione invierà periodicamente questi blocchi e li renderà disponibili come BLOB.

Per i log creati nella stessa ora possono esistere record duplicati. È possibile determinare se un record è un duplicato controllandone il numero **RequestId** e **Operation**.

### Convenzioni di denominazione dei log
Ciascun log verrà scritto nel seguente formato:

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

Nella tabella seguente vengono descritti i singoli attributi del nome del log:

| Attributo | Descrizione |
|----------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| <service-name> | Nome del servizio di archiviazione. Ad esempio: BLOB, tabella o coda |
| AAAA | Quattro cifre dell'anno del log. Ad esempio: 2011 |
| MM | Due cifre del mese del log. Ad esempio: 07 |
| GG | Due cifre del mese del log. Ad esempio: 07 |
| hh | Due cifre dell'ora che indica l'ora di inizio dei log, nel formato UTC 24 ore. Ad esempio: 18 |
| mm | Numero di due cifre che indica il minuto di inizio per i log. Questo valore non è supportato nella versione corrente di Analisi archiviazione, e il relativo valore sarà sempre 00. |
| <counter> | Contatore base zero con sei cifre che indica il numero di BLOB di log generati per il servizio di archiviazione in un'ora. Questo contatore parte da 000000. Ad esempio: 000001 |

Di seguito viene riportato un nome di log di esempio completo che combina gli esempi precedenti:

    blob/2011/07/31/1800/000001.log

L'URI di esempio riportato di seguito può essere utilizzato per accedere al log precedente:

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Quando viene registrata una richiesta di archiviazione, il nome log risultante è correlato all'ora in cui è stata completata l'operazione richiesta. Ad esempio, se una richiesta GetBlob è stata completata alle 18.30 del 31/07/2011, il log verrà scritto con il seguente prefisso: `blob/2011/07/31/1800/`

### Metadati dei log
Tutti i BLOB dei log vengono archiviati con i metadati che possono essere utilizzati per identificare i dati di registrazione contenuti nei BLOB. Nella tabella seguente sono descritti i singoli attributi dei metadati:

| Attributo | Descrizione |
|------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| LogType | Descrive se il log contiene informazioni relative alle operazioni di lettura, scrittura o eliminazione. Questo valore può includere un solo tipo o una combinazione di tutti e tre, separati da virgola. Esempio 1: scrittura Esempio 2: lettura, scrittura Esempio 3: lettura, scrittura, eliminazione |
| StartTime | L'ora in cui è stata registrata la prima voce nel log, nel formato AAAA-MM-GGThh:mm:ssZ. Ad esempio: 31-07-2011T18:21:46Z |
| EndTime | L'ora in cui è stata registrata l'ultima voce nel log, nel formato AAAA-MM-GGThh:mm:ssZ. Ad esempio: 31-07-2011T18:22:09Z |
| LogVersion | Versione del formato del log. Al momento, l'unico valore supportato è: 1,0 |

Nell'elenco seguente sono visualizzati i metadati di esempio completi tramite gli esempi precedenti:

- LogType=write

- StartTime=2011-07-31T18:21:46Z

- EndTime=2011-07-31T18:22:09Z

- LogVersion=1.0

### Accesso ai dati di registrazione

Tutti i dati del contenitore `$logs` sono accessibili tramite le API del servizio BLOB, incluse le API .NET fornite dalla libreria gestita di Azure. L'amministratore dell'account di archiviazione può leggere ed eliminare i log, ma non può crearli o aggiornarli. Quando si esegue una query per un log è possibile utilizzare sia i metadati del log, sia il nome del log. È possibile che i log di una data ora siano visualizzati come fuori servizio, ma i metadati specificano sempre l'intervallo temporale delle voci di un log. Pertanto, nella ricerca di un particolare log, è possibile utilizzare una combinazione di nomi log e metadati.

## Informazioni sulle metriche di Analisi archiviazione

Analisi archiviazione è in grado di archiviare le metriche che includono le statistiche delle transazioni aggregate e i dati di capacità relativi alle richieste in un servizio di archiviazione. Le transazioni vengono segnalate sia a livello di operazione API, sia a livello di servizio di archiviazione, mentre la capacità viene segnalata a livello di servizio di archiviazione. I dati delle metriche possono essere utilizzati per analizzare l'uso del servizio di archiviazione, diagnosticare i problemi relativi alle richieste effettuate al servizio di archiviazione e per migliorare le prestazioni delle applicazioni che usano un servizio.

Per utilizzare Analisi archiviazione, è necessario abilitarla singolarmente per ciascun servizio che si desidera monitorare. È possibile abilitarlo dal [portale di Azure](https://portal.azure.com). Per informazioni dettagliate, vedere [Monitorare un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Per abilitare Analisi archiviazione per ogni servizio, usare le operazioni che consentono di **ottenere le proprietà dei servizi**.

### Metriche di transazione

A intervalli di ore o minuti viene registrato un set di dati consistente per ciascun servizio di archiviazione e operazione API richiesta, inclusi ingresso/uscita, disponibilità, errori e vengono suddivise in categorie le percentuali di richieste. È possibile vedere un elenco completo dei dettagli delle transazioni nell'argomento [Schema di tabella della metrica di Analisi di archiviazione](https://msdn.microsoft.com/library/hh343264.aspx).

I dati delle transazioni vengono registrati a due livelli: a livello di servizio e di operazione API. A livello di servizio, le statistiche che riepilogano tutte le operazioni API richieste vengono scritte in un'entità di tabella ogni ora, persino se non sono state eseguite richieste al servizio. A livello di operazione API, le statistiche vengono scritte in un'entità solo se l'operazione è stata richiesta entro l'ora.

Se, ad esempio, si esegue un'operazione **GetBlob** nel servizio BLOB, la metrica Analisi archiviazione registrerà la richiesta e la includerà nei dati aggregati sia per il servizio BLOB, sia per l'operazione **GetBlob**. Tuttavia, se nel corso di quell'ora non vengono richieste operazioni **GetBlob**, non verrà scritta un'entità in `$MetricsTransactionsBlob` per tale operazione.

Le metriche delle transazioni vengono registrate sia per le richieste utente, sia per quelle eseguite dalla stessa Analisi archiviazione. Ad esempio, le richieste di Analisi archiviazione di scrivere entità di log e di tabella vengono registrate. Per altre informazioni su come vengono fatturate queste richieste, vedere [Analisi archiviazione e fatturazione](https://msdn.microsoft.com/library/hh360997.aspx)

### Metriche della capacità

>[AZURE.NOTE] Al momento, le metriche per la capacità sono disponibili solo per il servizio BLOB. Le metriche della capacità per il servizio di tabelle e di accodamento saranno disponibili nelle versioni future di Analisi archiviazione.

I dati relativi alla capacità vengono registrati quotidianamente per il servizio BLOB di un account di archiviazione e vengono scritte due entità di tabella. Un'entità fornisce le statistiche per i dati utente e l'altra le statistiche sul contenitore BLOB `$logs` utilizzato da Analisi archiviazione. Nella tabella `$MetricsCapacityBlob` sono incluse le seguenti statistiche:

- **Capacity**: la quantità di archiviazione utilizzata dal servizio BLOB dell'account di archiviazione, in byte.

- **ContainerCount**: il numero di contenitori BLOB del servizio BLOB dell'account di archiviazione.

- **ObjectCount**: il numero di BLOB di pagine o blocchi inviati nel servizio BLOB dell'account di archiviazione.

Per altre informazioni sulle metriche della capacità, vedere [Schema di tabella della metrica di Analisi di archiviazione](https://msdn.microsoft.com/library/hh343264.aspx).

### Come vengono archiviate le metriche

Tutti i dati delle metriche per ciascun servizio di archiviazione vengono archiviati in tre tabelle riservate per tale servizio: una tabella per le informazioni sulle transazioni, una per le informazioni sulle transazioni al minuto e un'altra per le informazioni relative alla capacità. Le informazioni sulla transazione e sulle transazioni al minuto consistono nei dati di richiesta e di risposta, mentre le informazioni sulla capacità consistono nei dati d'uso dell'archiviazione. Metriche per ora, minuto e capacità del servizio BLOB di un account di archiviazione sono accessibili nelle tabelle denominate nel modo descritto nella tabella seguente.

| Livello metrica | Nomi tabella | Versioni supportate |
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| Metriche orarie, posizione principale | $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue | Solo le versioni precedenti al 15-08-2013. Sebbene tali nomi siano supportati, è consigliabile passare all'utilizzo delle tabelle elencate di seguito. |
| Metriche orarie, posizione principale | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue | Tutte le versioni, inclusa quella del 15-08-2013. |
| Metriche per minuto, posizione principale | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue | Tutte le versioni, inclusa quella del 15-08-2013. |
| Metriche orarie, posizione secondaria | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue | Tutte le versioni, inclusa quella del 15-08-2013. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica. |
| Metriche al minuto, posizione secondaria | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue | Tutte le versioni, inclusa quella del 15-08-2013. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica. |
| Capacità (solo servizio BLOB) | $MetricsCapacityBlob | Tutte le versioni, inclusa quella del 15-08-2013. |


Tali tabelle vengono create automaticamente quando viene abilitato Analisi archiviazione per un account di archiviazione. L'accesso alle tabelle viene eseguito tramite lo spazio dei nomi dell'account di archiviazione, ad esempio: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### Accesso ai dati delle metriche

Tutti i dati delle tabelle delle metriche sono accessibili mediante le API del servizio di tabelle, incluse le API .NET fornite dalla libreria gestita di Azure. L'amministratore dell'account di archiviazione può leggere ed eliminare le entità di tabella, ma non può crearle né aggiornarle.

## Fatturazione per Analisi archiviazione

Analisi archiviazione viene abilitata dal proprietario di un account di archiviazione; non viene abilitato per impostazione predefinita. Tutti i dati delle metriche vengono scritti dai servizi di un account di archiviazione. Di conseguenza, ogni operazione di scrittura eseguita da Analisi archiviazione è fatturabile. Inoltre, anche la quantità di archiviazione utilizzata dai dati delle metriche è fatturabile.

Le seguenti azioni eseguite da Analisi archiviazione sono fatturabili:

- Richieste di creazione di BLOB per la registrazione 

- Richieste di creazione di entità di tabella per le metriche

Se è stato configurato un criterio di conservazione dei dati, non verranno addebitati costi per le transazioni eliminate quando Analisi archiviazione elimina i vecchi dati di registrazione e delle metriche. Tuttavia, le transazioni eliminate da un client sono fatturabili. Per ulteriori informazioni sui criteri di conservazione, vedere [Impostazione di un criterio di conservazione dei dati di Analisi archiviazione](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### Informazioni sulle richieste fatturabili

Ogni richiesta effettuata al servizio di archiviazione di un account è fatturabile oppure non fatturabile. Analisi archiviazione registra ogni singola richiesta eseguita a un servizio, incluso un messaggio di stato che indica in che modo è stata gestita la richiesta. Allo stesso modo, Analisi archiviazione archivia le metriche per un servizio e per le operazioni API di tale servizio, incluse le percentuali e il numero di determinati messaggi di stato. Insieme, queste funzionalità possono aiutare l'utente ad analizzare le richieste fatturabili, apportare miglioramenti all'applicazione, e diagnosticare i problemi relativi alle richieste ai servizi. Per altre informazioni sulla fatturazione, vedere [Informazioni sulla fatturazione di Archiviazione di Azure - Larghezza di banda, transazioni e capacità](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Quando si osservano i dati di Analisi archiviazione, è possibile utilizzare le tabelle dell'argomento [Operazioni registrate di Analisi archiviazione e messaggi di stato](https://msdn.microsoft.com/library/azure/hh343260.aspx) per determinare quali sono le richieste fatturabili. Quindi è possibile confrontare i log e i dati delle metriche ai messaggi di stato per vedere se è stata addebitata una particolare richiesta. Le tabelle dell'argomento citato possono anche essere utilizzate per investigare la disponibilità di un servizio di archiviazione o di una singola operazione API.

## Passaggi successivi

### Configurazione di Analisi archiviazione
- [Monitorare un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md)
- [Abilitazione e configurazione di Analisi archiviazione](https://msdn.microsoft.com/library/hh360996.aspx)

### Registrazione di Analisi archiviazione  
- [Informazioni sulla registrazione di Analisi archiviazione](https://msdn.microsoft.com/library/hh343262.aspx)
- [Formato log Analisi archiviazione](https://msdn.microsoft.com/library/hh343259.aspx)
- [Operazioni registrate di Analisi archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx)

### Metriche di Analisi archiviazione
- [Informazioni sulle metriche di Analisi archiviazione](https://msdn.microsoft.com/library/hh343258.aspx)
- [Schema di tabella della metrica di Analisi di archiviazione](https://msdn.microsoft.com/library/hh343264.aspx)
- [Operazioni registrate di Analisi archiviazione e messaggi di stato](https://msdn.microsoft.com/library/hh343260.aspx)  

<!---HONumber=AcomDC_0511_2016-->