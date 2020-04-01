---
title: Informazioni sugli output di Analisi di flusso di Azure
description: Questo articolo descrive opzioni di output di dati disponibili in Analisi di flusso di Azure, tra cui Power BI per i risultati dell'analisi.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 4517f85fae278bd8bc15a9586d9dc0202e7dfe56
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475236"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Informazioni sugli output di Analisi di flusso di Azure

Questo articolo descrive i tipi di output disponibili per un processo di Analisi di flusso di Azure.This article describes the types of outputs available for an Azure Stream Analytics job. Gli output consentono di archiviare e salvare i risultati del processo di Analisi di flusso di Azure. Utilizzando i dati di output, è possibile eseguire ulteriori analisi aziendali e il data warehousing dei dati.

Quando si progetta la query di Analisi di flusso, fare riferimento al nome dell'output utilizzando la [clausola INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). È possibile usare un singolo output per processo o più output per processo di streaming (se necessario) fornendo più clausole INTO nella query.

Per creare, modificare e testare gli output dei processi di Analisi di flusso, è possibile usare il [portale](stream-analytics-quick-create-portal.md#configure-job-output)di Azure , [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [l'API .NET,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet)l'API [REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)e [Visual Studio](stream-analytics-quick-create-vs.md).

Alcuni tipi di output supportano il [partizionamento.](#partitioning) [Le dimensioni dei batch](#output-batch-size) di output variano per ottimizzare la velocità effettiva.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Analisi di flusso supporta [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage è un repository iperscalabile a livello aziendale per carichi di lavoro analitici basati su Big Data.Azure Data Lake Storage is an enterprise-wide, hyperscale repository for big data analytic workloads. È possibile utilizzare Data Lake Storage per archiviare dati di qualsiasi dimensione, tipo e velocità di inserimento per l'analisi operativa ed esplorativa. Analisi di flusso deve essere autorizzato ad accedere a Data Lake Storage.

L'output di Archiviazione di Azure Data Lake da Analisi di flusso non è attualmente disponibile nelle aree di Azure China 21Vianet e Azure Germania (T-Systems International).

Nella tabella seguente sono elencati i nomi delle proprietà e le relative descrizioni per configurare l'output di Data Lake Storage Gen 1.   

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output | Nome descrittivo utilizzato nelle query per indirizzare l'output della query a Data Lake Store. |
| Subscription | Sottoscrizione che contiene l'account di archiviazione di Azure Data Lake. |
| Nome account | Nome dell'account Data Lake Store a cui stai inviando l'output. Viene visualizzato un elenco a discesa di account Data Lake Store disponibili nell'abbonamento. |
| Schema prefisso percorso | Percorso del file utilizzato per scrivere i file all'interno dell'account Data Lake Store specificato. È possibile specificare una o più istanze delle variabili "date" e "time":<br /><ul><li>Esempio 1: folder1/logs/{date}/{time}</li><li>Esempio 2: folder1/logs/{date}</li></ul><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />Se il modello di percorso del file non contiene una barra finale (/), l'ultimo modello nel percorso del file viene considerato come un prefisso del nome file. <br /><br />Vengono creati nuovi file nei casi seguenti:<ul><li>Modifica dello schema di output</li><li>Riavvio esterno o interno di un processo</li></ul> |
| Formato data | Facoltativa. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
|Formato ora | Facoltativa. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro.|
| Codifica | Se si usa il formato CSV o JSON, è necessario specificare una codifica. Al momento UTF-8 è l'unico formato di codifica supportato.|
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.|
| Format | Applicabile solo per la serializzazione JSON. **Linea separata** specifica che l'output viene formattato facendo separate ogni oggetto JSON da una nuova riga. Se si seleziona **Riga separata,** il codice JSON viene letto un oggetto alla volta. L'intero contenuto da solo non sarebbe un JSON valido.  **Array** specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da riga, perché non richiede alcuna gestione speciale mentre il file di output è ancora in fase di scrittura.|
| Modalità di autenticazione | È possibile autorizzare l'accesso all'account di archiviazione data lake usando [l'identità gestita](stream-analytics-managed-identities-adls.md) o il token utente. Dopo aver concesso l'accesso, è possibile revocare l'accesso modificando la password dell'account utente, eliminando l'output di Data Lake Storage per questo processo o eliminando il processo di Analisi di flusso. |

## <a name="sql-database"></a>Database SQL

È possibile usare il [database SQL](https://azure.microsoft.com/services/sql-database/) di Azure come output per i dati relazionali in natura o per le applicazioni che dipendono dal contenuto ospitato in un database relazionale. I processi di Analisi di flusso scrivono in una tabella esistente nel database SQL. Lo schema della tabella deve corrispondere esattamente ai campi e ai relativi tipi nell'output del processo. È anche possibile specificare [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) come output tramite l'opzione Output database SQL. Per altre informazioni sui modi per migliorare la velocità effettiva di scrittura, vedere l'articolo Analisi di flusso con il database SQL di [Azure come output.](stream-analytics-sql-output-perf.md)

È anche possibile usare [l'istanza gestita del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) di Azure come output. È necessario [configurare l'endpoint pubblico nell'istanza gestita del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) di Azure e quindi configurare manualmente le impostazioni seguenti in Analisi di flusso di Azure.You have to configure public endpoint in Azure SQL Database Managed Instance and then manually configure the following settings in Azure Stream Analytics. La macchina virtuale di Azure che esegue SQL Server con un database collegato è supportata anche configurando manualmente le impostazioni riportate di seguito.

Nella tabella seguente sono elencati i nomi delle proprietà e la relativa descrizione per la creazione di un output del database SQL.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
| Database | Nome del database a cui si sta inviando l'output. |
| Nome server | Nome del server di database SQL. Per l'istanza gestita del database SQL di Azure è necessario specificare la porta 3342.For Azure SQL Database Managed Instance, it is required to specify the port 3342. Ad esempio, *sampleserver.public.database.windows.net,3342* |
| Username | Nome utente con accesso in scrittura al database. Analisi di flusso supporta solo l'autenticazione SQL. |
| Password | Password per la connessione al database. |
| Tabella | Nome della tabella in cui viene scritto l'output. Per il nome della tabella viene fatta distinzione tra maiuscole e minuscole. Lo schema di questa tabella deve corrispondere esattamente al numero di campi e ai relativi tipi generati dall'output del processo. |
|Eredita schema di partizione| Un'opzione per ereditare lo schema di partizionamento del passaggio di query precedente, per abilitare la topologia completamente parallela con più writer alla tabella. Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md).|
|Numero massimo di batch| Limite superiore consigliato per il numero di record inviati con ogni transazione di inserimento bulk.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Archiviazione BLOB e Azure Data Lake Gen2

Data Lake Storage Gen2 usa Archiviazione di Azure come base per la compilazione di Enterprise Data Lake (EDL) in Azure. Progettato fin dall'inizio per servire più petabyte di informazioni, mentre supportando centinaia di gigabit di velocità effettiva, Data Lake Storage Gen2 consente di gestire facilmente enormi quantità di dati. Una parte fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno spazio dei nomi gerarchico all'archiviazione BLOB.

L'archiviazione BLOB di Azure offre una soluzione conveniente e scalabile per l'archiviazione di grandi quantità di dati non strutturati nel cloud. Per un'introduzione sull'archiviazione BLOB e il relativo utilizzo, vedere Caricare, scaricare ed elencare BLOB con il portale di Azure.For an introduction on Blob storage and its usage, see [Upload, download, and list blobs with the Azure portal.](../storage/blobs/storage-quickstart-blobs-portal.md)

Nella tabella seguente sono elencati i nomi delle proprietà e le relative descrizioni per la creazione di un BLOB o di un output ADLS Gen2.

| Nome proprietà       | Descrizione                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias di output        | Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione BLOB. |
| Account di archiviazione     | Nome dell'account di archiviazione a cui si sta inviando l'output.               |
| Chiave dell'account di archiviazione | Chiave privata associata all'account di archiviazione.                              |
| Contenitore di archiviazione   | Raggruppamento logico per i BLOB archiviati nel servizio BLOB di Azure.A logical grouping for blobs stored in the Azure Blob service. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB. |
| Modello di percorso | Facoltativa. Modello di percorso del file usato per scrivere i BLOB all'interno del contenitore specificato. <br /><br /> Nel modello di percorso è possibile scegliere di usare una o più istanze delle variabili di data e ora per specificare la frequenza di scrittura dei BLOB:In the path pattern, you can choose to use one or more instances of the date and time variables to specify the frequency that blobs are written: <br /> {date}, {time} <br /><br />È possibile usare un partizionamento BLOB personalizzato per specificare un nome personalizzato {field} dai dati di evento ai BLOB di partizione. Il nome del campo è un valore alfanumerico e può includere spazi, trattini e caratteri di sottolineatura. Le restrizioni sui campi personalizzati includono le seguenti: <ul><li>I nomi dei campi non fanno distinzione tra maiuscole e minuscole. Ad esempio, il servizio non è in grado di distinguere tra la colonna "ID" e la colonna "id".</li><li>I campi nidificati non sono consentiti. Utilizzare invece un alias nella query di processo per "appiattire" il campo.</li><li>Le espressioni non possono essere utilizzate come nome di campo.</li></ul> <br />Questa funzionalità consente di usare anche configurazioni dell'identificatore di formato data/ora personalizzate nel percorso. I formati di data e ora personalizzati devono essere specificati uno alla volta, racchiusi tra la parola chiave {datetime:\<identificatore >}. Gli input consentiti per \<gli> dell'identificatore sono yyyy, MM, M, dd, d, HH, H, mm, m, ss o s. È possibile utilizzare\<più volte la parola chiave> identificatore data/ora nel percorso per formare configurazioni di data/ora personalizzate. <br /><br />Esempi: <ul><li>Esempio 1: cluster1/logs/{date}/{time}</li><li>Esempio 2: cluster1/logs/{date}</li><li>Esempio 3: cluster1/{client_id}/{date}/{time}</li><li>Esempio 4: cluster1 / , datetime:ss , o myField, dove la query è: SELECT data.myField AS myField FROM Input;</li><li>Esempio 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />La denominazione dei file utilizza la convenzione seguente: <br /><br />{Schema prefisso percorso}/schemaHashcode_Guid_Number.extension<br /><br />File di output di esempio:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Per altre informazioni su questa funzionalità, vedere Partizionamento dell'output BLOB personalizzato di Azure Stream Analytics.For more information about this feature, see [Azure Stream Analytics custom blob output partitioning](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato data | Facoltativa. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
| Formato ora | Facoltativa. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. JSON, CSV, Avro e Parquet sono supportati. |
|Righe minime (solo parità)|Numero minimo di righe per batch. Per Parquet, ogni lotto creerà un nuovo file. Il valore predefinito corrente è 2.000 righe e il valore massimo consentito è 10.000 righe.|
|Tempo massimo (solo parquet)|Tempo di attesa massimo per batch. Dopo questo periodo, il batch verrà scritto nell'output anche se il requisito minimo delle righe non viene soddisfatto. Il valore predefinito corrente è 1 minuto e il valore massimo consentito è 2 ore. Se l'output del BLOB ha una frequenza di ripetizione del percorso, il tempo di attesa non può essere superiore all'intervallo di tempo della partizione.|
| Codifica    | Se si usa il formato CSV o JSON, è necessario specificare una codifica. Al momento UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore   | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format      | Applicabile solo per la serializzazione JSON. **Linea separata** specifica che l'output viene formattato facendo separate ogni oggetto JSON da una nuova riga. Se si seleziona **Riga separata,** il codice JSON viene letto un oggetto alla volta. L'intero contenuto da solo non sarebbe un JSON valido. **Array** specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da riga, perché non richiede alcuna gestione speciale mentre il file di output è ancora in fase di scrittura. |

Quando si usa l'archiviazione BLOB come output, viene creato un nuovo file nel BLOB nei casi seguenti:When you're using Blob storage as output, a new file is created in the blob in the following cases:

* Se il file supera il numero massimo di blocchi consentiti (attualmente 50.000). È possibile raggiungere il numero massimo consentito di blocchi senza raggiungere la dimensione massima consentita del BLOB. Ad esempio, se la frequenza di output è elevata, ogni blocco può contenere più byte e le dimensioni del file sono superiori. Se la frequenza di output è bassa, ogni blocco contiene meno dati e le dimensioni del file sono inferiori.
* Se è presente una modifica dello schema nell'output e il formato di output richiede lo schema fisso (CSV e Avro).
* Se un processo viene riavviato, esternamente con l'arresto e l'avvio da parte di un utente oppure internamente per la manutenzione del sistema o il recupero da errori.
* Se la query è completamente partizionata e viene creato un nuovo file per ogni partizione di output.
* Se l'utente elimina un file o un contenitore dell'account di archiviazione.
* Se l'output è partizionato in base al tempo usando il modello di prefisso del percorso e viene usato un nuovo BLOB quando la query passa all'ora successiva.
* Se l'output è partizionato da un campo personalizzato e viene creato un nuovo BLOB per ogni chiave di partizione se non esiste.
* Se l'output è partizionato da un campo personalizzato in cui la cardinalità della chiave di partizione supera 8.000 e viene creato un nuovo BLOB per ogni chiave di partizione.

## <a name="event-hubs"></a>Hub eventi

Il servizio [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) è un inseritore eventi di pubblicazione-sottoscrizione altamente scalabile. Può raccogliere milioni di eventi al secondo. Un utilizzo di un hub eventi come output è quando l'output di un processo di Analisi di flusso diventa l'input di un altro processo di streaming. Per informazioni sulla dimensione massima dei messaggi e sull'ottimizzazione della dimensione del batch, vedere la sezione relativa alle dimensioni del batch di [output.](#output-batch-size)

Sono necessari alcuni parametri per configurare i flussi di dati dagli hub eventi come output.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output | Nome descrittivo utilizzato nelle query per indirizzare l'output della query a questo hub eventi. |
| Spazio dei nomi dell'hub eventi | Contenitore per un set di entità di messaggistica. Quando è stato creato un nuovo hub eventi, è stato creato anche uno spazio dei nomi dell'hub eventi. |
| Nome hub eventi | Nome dell'output dell'hub eventi. |
| Nome criteri hub eventi | Criteri di accesso condiviso, che è possibile creare nella scheda **Configura** dell'hub eventi. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e chiavi di accesso. |
| Chiave criteri hub eventi | Chiave di accesso condiviso utilizzata per autenticare l'accesso allo spazio dei nomi dell'hub eventi. |
| Colonna chiave di partizionePartition key column | Facoltativa. Colonna che contiene la chiave di partizione per l'output dell'hub eventi. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica | Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format | Applicabile solo per la serializzazione JSON. **Linea separata** specifica che l'output viene formattato facendo separate ogni oggetto JSON da una nuova riga. Se si seleziona **Riga separata,** il codice JSON viene letto un oggetto alla volta. L'intero contenuto da solo non sarebbe un JSON valido. **Array** specifica che l'output viene formattato come matrice di oggetti JSON.  |
| Colonne delle proprietà | Facoltativa. Colonne separate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché come payload. Ulteriori informazioni su questa funzionalità sono disponibili nella sezione [Proprietà personalizzate dei metadati per l'output.](#custom-metadata-properties-for-output) |

## <a name="power-bi"></a>Power BI

È possibile usare [Power BI](https://powerbi.microsoft.com/) come output per un processo di Analisi di flusso per fornire un'esperienza di visualizzazione avanzata dei risultati dell'analisi. È possibile utilizzare questa funzionalità per i dashboard operativi, la generazione di report e la creazione di report basati sulle metriche.

L'output di Power BI di Analisi di flusso non è attualmente disponibile nelle aree di Azure China 21Vianet e Azure Germania (T-Systems International).

Nella tabella seguente sono elencati i nomi delle proprietà e le relative descrizioni per configurare l'output di Power BI.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Specificare un nome descrittivo utilizzato nelle query per indirizzare l'output della query a questo output di Power BI. |
| Area di lavoro del gruppo |Per abilitare la condivisione dei dati con altri utenti di Power BI, è possibile selezionare i gruppi all'interno dell'account Power BI o scegliere **Area di lavoro personale** se non si vuole scrivere in un gruppo. L'aggiornamento di un gruppo esistente richiede il rinnovo dell'autenticazione di Power BI. |
| Nome del set di dati |Specificare un nome di set di dati che si vuole usare per l'output di Power BI. |
| Nome tabella |Immettere un nome per la tabella nel set di dati dell'output di Power BI. L'output di Power BI da processi di Analisi di flusso può avere al momento solo una tabella in un set di dati. |
| Autorizzare la connessione | È necessario autorizzare con Power BI per configurare le impostazioni di output. Dopo aver concesso l'accesso a questo output al dashboard di Power BI, è possibile revocare l'accesso modificando la password dell'account utente, eliminando l'output del processo o il processo di Analisi di flusso. | 

Per una procedura dettagliata sulla configurazione di un output e di un dashboard di Power BI, vedere l'esercitazione [Analisi di flusso di Azure e Power BI.](stream-analytics-power-bi-dashboard.md)

> [!NOTE]
> Non creare in modo esplicito il set di dati e la tabella nel dashboard di Power BI. Il set di dati e la tabella vengono popolati automaticamente all'avvio del processo e il processo inizia a pompare l'output in Power BI. Se la query del processo non genera alcun risultato, il set di dati e la tabella non vengono creati. Se Power BI dispone già di un set di dati e di una tabella con lo stesso nome di quello fornito in questo processo di Analisi di flusso, i dati esistenti vengono sovrascritti.
>

### <a name="create-a-schema"></a>Creare uno schema
Azure Stream Analytics crea un set di dati di Power BI e uno schema di tabella per l'utente, se non esistono già. In tutti gli altri casi, la tabella viene aggiornata con nuovi valori. Attualmente, all'interno di un set di dati può esistere una sola tabella. 

Power BI usa i criteri di conservazione FIFO (first-in, first-out). I dati verranno raccolti in una tabella fino a quando non raggiungeranno 200.000 righe.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Convertire un tipo di dati da Analisi di flusso a Power BIConvert a data type from Stream Analytics to Power BI
Analisi di flusso di Azure consente di aggiornare il modello di dati in modo dinamico in fase di esecuzione se viene modificato lo schema di output. Vengono rilevate tutte le modifiche al nome e al tipo di colonna e l'aggiunta o la rimozione di colonne.

Questa tabella illustra le conversioni dei tipi di dati dai tipi di dati di [Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) ai tipi [EDM (Power](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)BI Entity Data Model), se non esistono un set di dati e una tabella di Power BI.

Dall'analisi di flusso | A Power BI
-----|-----
bigint | Int64
nvarchar(max) | string
Datetime | Datetime
float | Double
Matrice di record | Tipo stringa, valore costante "IRecord" o "IArray"

### <a name="update-the-schema"></a>Aggiornare lo schema
L'analisi di flusso deduce lo schema del modello di dati in base al primo set di eventi dell'output. Successivamente, se necessario, lo schema del modello di dati viene aggiornato per contenere gli eventi in ingresso che potrebbero non rientrare nello schema originale.

Evitare `SELECT *` la query per impedire l'aggiornamento dinamico dello schema tra le righe. Oltre alle potenziali implicazioni in termini di prestazioni, potrebbe tradursi in un'incertezza del tempo impiegato per i risultati. Selezionare i campi esatti che devono essere visualizzati nel dashboard di Power BI. È anche necessario che i valori dei dati siano conformi al tipo di dati scelto.


Precedente/corrente | Int64 | string | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | string | string | Double
Double | Double | string | string | Double
string | string | string | string | string 
Datetime | string | string |  Datetime | string

## <a name="table-storage"></a>Archiviazione tabelle

[archiviazione tabelle di Azure](../storage/common/storage-introduction.md) offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica di un'applicazione in base alle richieste degli utenti. L'archiviazione tabelle è l'archivio chiavi/attributi NoSQL di Microsoft, che è possibile usare per i dati strutturati con meno vincoli sullo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente.

Nella tabella seguente sono elencati i nomi delle proprietà e le relative descrizioni per la creazione di un output di tabella.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione tabelle. |
| Account di archiviazione |Nome dell'account di archiviazione a cui si sta inviando l'output. |
| Chiave dell'account di archiviazione |Chiave di accesso associata all'account di archiviazione. |
| Nome tabella |Nome della tabella. La tabella viene creata se non esiste. |
| Chiave di partizione |Nome della colonna di output che contiene la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione all'interno di una tabella che costituisce la prima parte della chiave primaria di un'entità. Si tratta di un valore stringa che può avere una dimensione massima di 1 KB. |
| Chiave di riga |Nome della colonna di output che contiene la chiave di riga. La chiave di riga è un identificatore univoco per un'entità all'interno di una partizione. Forma la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa che può avere una dimensione massima di 1 KB. |
| Dimensioni dei batch |Numero di record per un'operazione batch. Il valore predefinito (100) è sufficiente per la maggior parte dei processi. Vedere la [specifica Operazione batch tabella](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) per ulteriori dettagli sulla modifica di questa impostazione. |

## <a name="service-bus-queues"></a>Code del bus di servizio

Le code del bus di servizio offrono il recapito di un messaggio FIFO a uno o più consumer [concorrenti.](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) In genere, i messaggi vengono ricevuti ed elaborati dai ricevitori nell'ordine temporale in cui sono stati aggiunti alla coda. Ogni messaggio viene ricevuto ed elaborato da un solo consumer di messaggi.

Nel [livello di compatibilità 1.2,](stream-analytics-compatibility-level.md)Azure Stream Analytics usa il protocollo di messaggistica [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) per scrivere nelle code e negli argomenti del bus di servizio. AMQP consente di creare applicazioni ibride multipiattaforma usando un protocollo aperto standard.

Nella tabella seguente sono elencati i nomi delle proprietà e le relative descrizioni per la creazione di un output della coda.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo utilizzato nelle query per indirizzare l'output della query a questa coda del bus di servizio. |
| Spazio dei nomi del bus di servizio |Contenitore per un set di entità di messaggistica. |
| Nome della coda |Nome della coda del bus di servizio. |
| Nome criteri coda |Quando si crea una coda, è anche possibile creare criteri di accesso condiviso nella scheda **Configura** della coda. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e chiavi di accesso. |
| Chiave criteri coda |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format |Applicabile solo per il tipo JSON. **Linea separata** specifica che l'output viene formattato facendo separate ogni oggetto JSON da una nuova riga. Se si seleziona **Riga separata,** il codice JSON viene letto un oggetto alla volta. L'intero contenuto da solo non sarebbe un JSON valido. **Array** specifica che l'output viene formattato come matrice di oggetti JSON. |
| Colonne delle proprietà | Facoltativa. Colonne separate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché come payload. Ulteriori informazioni su questa funzionalità sono disponibili nella sezione [Proprietà personalizzate dei metadati per l'output.](#custom-metadata-properties-for-output) |
| Colonne delle proprietà di sistema | Facoltativa. Coppie di valori chiave di Proprietà di sistema e nomi di colonna corrispondenti che devono essere associati al messaggio in uscita anziché al payload. Ulteriori informazioni su questa funzionalità sono disponibili nella sezione Proprietà del sistema [per gli output della coda del bus](#system-properties-for-service-bus-queue-and-topic-outputs) di servizio e degli argomenti  |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="service-bus-topics"></a>Argomenti del bus di servizio
Le code del bus di servizio forniscono un metodo di comunicazione uno-a-uno dal mittente al destinatario. [Gli argomenti](https://msdn.microsoft.com/library/azure/hh367516.aspx) del bus di servizio forniscono una forma di comunicazione uno-a-molti.

Nella tabella seguente sono elencati i nomi delle proprietà e le relative descrizioni per la creazione di un output dell'argomento Bus di servizio.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo utilizzato nelle query per indirizzare l'output della query a questo argomento del bus di servizio. |
| Spazio dei nomi del bus di servizio |Contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio. |
| Nome argomento |Gli argomenti sono entità di messaggistica, simili agli Hub eventi e alle code. Sono progettati per raccogliere flussi di eventi da dispositivi e servizi. Quando viene creato un argomento, viene assegnato anche un nome specifico. I messaggi inviati a un argomento non sono disponibili a meno che non venga creata una sottoscrizione, pertanto verificare che nell'argomento siano presenti una o più sottoscrizioni. |
| Nome criteri argomento |Quando si crea un argomento del bus di servizio, è anche possibile creare criteri di accesso condiviso nella scheda **Configura** dell'argomento. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e chiavi di accesso. |
| Chiave criteri argomento |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Se si usa il formato CSV o JSON, è necessario specificare una codifica. Al momento UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Colonne delle proprietà | Facoltativa. Colonne separate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché come payload. Ulteriori informazioni su questa funzionalità sono disponibili nella sezione [Proprietà personalizzate dei metadati per l'output.](#custom-metadata-properties-for-output) |
| Colonne delle proprietà di sistema | Facoltativa. Coppie di valori chiave di Proprietà di sistema e nomi di colonna corrispondenti che devono essere associati al messaggio in uscita anziché al payload. Ulteriori informazioni su questa funzionalità sono disponibili nella sezione Proprietà del sistema [per gli output della coda del bus](#system-properties-for-service-bus-queue-and-topic-outputs) di servizio e degli argomenti |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) è un servizio di database distribuito a livello globale che offre una scala elastica illimitata in tutto il mondo, query avanzate e indicizzazione automatica su modelli di dati indipendenti dallo schema. Per altre informazioni sulle opzioni del contenitore database Cosmos di Azure per Analisi di flusso, vedere l'articolo Analisi di flusso con database Cosmos di [Azure come output.](stream-analytics-documentdb-output.md)

L'output del database Cosmos di Azure da Analisi di flusso non è attualmente disponibile nelle aree di Azure China 21Vianet e Azure Germania (T-Systems International).

> [!Note]
> Al momento, Analisi di flusso di Azure supporta solo la connessione al database Cosmos di Azure usando l'API SQL.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente.

Nella tabella seguente sono descritte le proprietà per la creazione di un output di Azure Cosmos DB.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output | Alias per fare riferimento a questo output nella query di Analisi di flusso di Azure. |
| Sink | Database Cosmos di Azure.Azure Cosmos DB. |
| Opzione di importazione | Scegliere **Seleziona Cosmos DB dall'abbonamento** o **Fornisci manualmente le impostazioni di Cosmos DB.**
| Account ID | Nome o URI endpoint dell'account Azure Cosmos DB. |
| Chiave account | Chiave di accesso condiviso per l'account Azure Cosmos DB. |
| Database | Nome del database Azure Cosmos DB. |
| Nome contenitore | Il nome del contenitore da utilizzare, che deve esistere in Cosmos DB. Esempio:  <br /><ul><li> _MyContainer:_ deve esistere un contenitore denominato "MyContainer".</li>|
| Document ID |Facoltativa. Nome del campo negli eventi di output utilizzato per specificare la chiave primaria su cui si basano le operazioni di inserimento o aggiornamento.

## <a name="azure-functions"></a>Funzioni di Azure
Funzioni di Azure è un servizio di calcolo senza server che è possibile usare per eseguire il codice su richiesta senza dover eseguire il provisioning o gestire in modo esplicito l'infrastruttura. Consente di implementare il codice attivato da eventi che si verificano in Azure o nei servizi partner. Questa capacità di Funzioni di Azure per rispondere ai trigger lo rende un output naturale per Analisi di flusso di Azure.This ability of Azure Functions to respond to triggers makes it a natural output for Azure Stream Analytics. Questo adattatore di output consente agli utenti di connettere Analisi di flusso a Funzioni di Azure ed eseguire uno script o una parte di codice in risposta a una varietà di eventi.

L'output di Funzioni di Azure da Analisi di flusso non è attualmente disponibile nelle aree di Azure China 21Vianet e Azure Germania (T-Systems International).

Analisi di flusso di Azure richiama Funzioni di Azure tramite trigger HTTP. L'adattatore di output funzioni di Azure è disponibile con le proprietà configurabili seguenti:The Azure Functions output adapter is available with the following configurable properties:

| Nome proprietà | Descrizione |
| --- | --- |
| App per le funzioni |Nome dell'app Funzioni di Azure. |
| Funzione |Nome della funzione nell'app Funzioni di Azure. |
| Chiave |Se si vuole usare una funzione di Azure da un'altra sottoscrizione, è possibile farlo fornendo la chiave per accedere alla funzione. |
| Dimensioni massime batch |Proprietà che consente di impostare la dimensione massima per ogni batch di output inviato alla funzione di Azure.A property that lets you set the maximum size for each output batch that's sent to your Azure function. L'unità di input è espressa in byte. Per impostazione predefinita, questo valore è 262.144 byte (256 KB). |
| Numero massimo di batch  |A property that lets you specify the maximum number of events in each batch that's sent to Azure Functions. Il valore predefinito è 100. |

Azure Stream Analytics prevede lo stato HTTP 200 dall'app Funzioni per i batch elaborati correttamente.

Quando Analisi di flusso di Azure riceve un'eccezione 413 ("http Request Entity Too Large") da una funzione di Azure, riduce le dimensioni dei batch inviati a Funzioni di Azure.When Azure Stream Analytics receives a 413 ("http Request Entity Too Large") exception from an Azure function, it reduces the size of the batches that it sends to Azure Functions. Nel codice della funzione di Azure usare questa eccezione per assicurarsi che Azure Stream Analytics non invii batch di grandi dimensioni. Assicurarsi inoltre che i valori massimi di conteggio e dimensione dei batch utilizzati nella funzione siano coerenti con i valori immessi nel portale di Analisi di flusso.

> [!NOTE]
> Durante la connessione di prova, Analisi di flusso invia un batch vuoto a Funzioni di Azure per verificare se la connessione tra i due funziona. Assicurati che l'app Funzioni gestisca le richieste batch vuote per assicurarti che la connessione di prova venga superata.

Inoltre, in una situazione in cui non vi è alcun evento di atterraggio in un intervallo di tempo, non viene generato alcun output. Di conseguenza, la funzione **computeResult** non viene chiamata. Questo comportamento è coerente con le funzioni di aggregazione finestra predefinite.

## <a name="custom-metadata-properties-for-output"></a>Proprietà dei metadati personalizzate per l'output 

È possibile collegare le colonne della query come proprietà utente ai messaggi in uscita. Queste colonne non entrano nel payload. Le proprietà sono presenti sotto forma di dizionario nel messaggio di output. *Key* è il nome e il *valore* della colonna è il valore della colonna nel dizionario delle proprietà. Tutti i tipi di dati di Analisi di flusso sono supportati, ad eccezione di Record e Array.  

Uscite supportate: 
* Coda del bus di servizio 
* Argomento del bus di servizio 
* Hub eventi 

Nell'esempio seguente vengono aggiunti `DeviceId` i `DeviceStatus` due campi e ai metadati. 
* Query: `select *, DeviceId, DeviceStatus from iotHubInput`
* Configurazione dell'uscita:`DeviceId,DeviceStatus`

![Colonne delle proprietà](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Nella schermata seguente vengono illustrate le proprietà dei messaggi di output controllati in EventHub tramite [Esplora bus di servizio](https://github.com/paolosalvatori/ServiceBusExplorer).

![Proprietà personalizzate evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Proprietà del sistema per gli output della coda del bus di servizio e degli argomenti 
È possibile collegare le colonne di query come [proprietà di sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) alla coda del bus di servizio in uscita o ai messaggi Dell'argomento. Queste colonne non vengono inserite nel payload invece che la proprietà di [sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage corrispondente viene popolata con i valori della colonna della query.
Queste proprietà di `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`sistema sono supportate - .
I valori stringa di queste colonne vengono analizzati come tipo di valore della proprietà di sistema corrispondente e gli eventuali errori di analisi vengono considerati come errori di dati.
Questo campo viene fornito come formato di oggetto JSON. I dettagli su questo formato sono i seguenti -
* Circondato da parentesi graffe {}.
* Scritto in coppie chiave/valore.
* Le chiavi e i valori devono essere stringhe.
* Chiave è il nome e il valore della proprietà di sistema è il nome della colonna della query.
* Le chiavi e i valori sono separati da due punti.
* Ogni coppia chiave/valore è separata da una virgola.

Questo mostra come utilizzare questa proprietà –

* Query: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colonne delle proprietà di sistema:`{ "MessageId": "column1", "PartitionKey": "column2"}`

In questo `MessageId` modo vengono impostati i messaggi della coda del bus di servizio con `column1`i valori di 's e PartitionKey è impostato con `column2`i valori di 's.

## <a name="partitioning"></a>Partizionamento

Nella tabella seguente viene riepilogato il supporto della partizione e il numero di writer di output per ogni tipo di output:

| Tipo di output | Supporto del partizionamento | Chiave di partizione  | Numero di writer di output |
| --- | --- | --- | --- |
| Archivio Azure Data Lake | Sì | Nel modello di prefisso del percorso, utilizzare i token di data e ora. Scegliere il formato della data, ad esempio AAAA/MM/GG, GG/MM/AAAA o MM-GG-AAAA. HH viene utilizzato per il formato dell'ora. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| database SQL di Azure | Sì, deve essere abilitato. | In base alla clausola PARTITION BY nella query. | Quando l'opzione Eredita partizionamento è abilitata, segue il partizionamento di input per [query completamente parallelizzabili.](stream-analytics-scale-jobs.md) Per altre informazioni su come ottenere prestazioni migliori in termini di velocità effettiva di scrittura durante il caricamento dei dati nel database SQL di Azure, vedere Output di Analisi di flusso di Azure nel database SQL di Azure.To learn more about achieving better write throughput performance when you're loading data into Azure SQL Database, see [Azure Stream Analytics output to Azure SQL Database.](stream-analytics-sql-output-perf.md) |
| Archiviazione BLOB di Azure | Sì | Usa i token di data e ora dai campi dell'evento nel modello di percorso. Scegliere il formato della data, ad esempio AAAA/MM/GG, GG/MM/AAAA o MM-GG-AAAA. HH viene utilizzato per il formato dell'ora. L'output del BLOB può essere partizionato in base a un singolo attributo dell'evento personalizzato {fieldname} o {datetime:\<specifier>}. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Hub eventi di Azure | Sì | Sì | Varia a seconda dell'allineamento della partizione.<br /> Quando la chiave di partizione per l'output dell'hub eventi è allineata allo stesso modo con il passaggio di query upstream (precedente), il numero di writer è lo stesso del numero di partizioni nell'output dell'hub eventi. Ogni writer utilizza la [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) per inviare eventi alla partizione specifica. <br /> Quando la chiave di partizione per l'output dell'hub eventi non è allineata con il passaggio di query upstream (precedente), il numero di writer è uguale al numero di partizioni nel passaggio precedente. Ogni writer usa la [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) in **EventHubClient** per inviare eventi a tutte le partizioni di output. |
| Power BI | No | nessuno | Non applicabile. |
| Archiviazione tabelle di Azure | Sì | Qualsiasi colonna di output.  | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Argomento del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è basato sullo [SKU del bus](../service-bus-messaging/service-bus-partitioning.md)di servizio e sulle dimensioni . La chiave di partizione è un valore intero univoco per ogni partizione.| Corrisponde al numero di partizioni nell'argomento di output.  |
| Coda del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è basato sullo [SKU del bus](../service-bus-messaging/service-bus-partitioning.md)di servizio e sulle dimensioni . La chiave di partizione è un valore intero univoco per ogni partizione.| Corrisponde al numero di partizioni nella coda di output. |
| Azure Cosmos DB | Sì | In base alla clausola PARTITION BY nella query. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Funzioni di Azure | Sì | In base alla clausola PARTITION BY nella query. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |

Il numero di writer di `INTO <partition count>` output può essere controllato anche utilizzando la clausola [(vedere INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) nella query, che può essere utile per ottenere una topologia di processo desiderata. Se l'adattatore di output non è partizionato, la mancanza di dati in una partizione di input causerà un ritardo fino alla quantità di tempo di arrivo in ritardo. In questi casi, l'output viene unito a un singolo writer, che potrebbe causare colli di bottiglia nella pipeline. Per altre informazioni sui criteri di arrivo in ritardo, vedere [Considerazioni sull'ordine degli eventi](stream-analytics-out-of-order-and-late-events.md)di Analisi di flusso di Azure.To learn more about late arrival policy, see Azure Stream Analytics event order considerations .

## <a name="output-batch-size"></a>Dimensione del batch di output
Analisi di flusso di Azure usa batch di dimensioni variabili per elaborare eventi e scrivere negli output. In genere il motore di Analisi di flusso non scrive un messaggio alla volta e usa i batch per l'efficienza. Quando la frequenza degli eventi in ingresso e in uscita è elevata, Analisi di flusso utilizza batch di dimensioni maggiori. Quando la frequenza in uscita è bassa, usa batch di dimensioni minori per mantenere bassa la latenza.

Nella tabella seguente vengono illustrate alcune considerazioni per l'invio in batch di output:

| Tipo di output |    Dimensioni massime messaggio | Ottimizzazione delle dimensioni batch |
| :--- | :--- | :--- |
| Archivio Azure Data Lake | Vedere [Limiti di archiviazione di Data Lake](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Utilizzare fino a 4 MB per operazione di scrittura. |
| database SQL di Azure | Configurabile utilizzando il numero massimo di batch. 10.000 massimo e 100 righe minime per singola inserimento bulk per impostazione predefinita.<br />Vedere [Limiti di SQL](../sql-database/sql-database-resource-limits.md)di Azure . |  Ogni batch viene inizialmente inserito in blocco con il numero massimo di batch. Il batch viene diviso a metà (fino al numero minimo di batch) in base agli errori ritentabili da SQL. |
| Archiviazione BLOB di Azure | Vedere [Limiti di Archiviazione di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | La dimensione massima del blocco BLOB è 4 MB.<br />Il numero massimo di bock blob è 50.000.The maximum blob bock count is 50,000. |
| Hub eventi di Azure    | 256 KB o 1 MB per messaggio. <br />Consultate [Limiti degli hub eventi.](../event-hubs/event-hubs-quotas.md) |    Quando il partizionamento di input/output non è allineato, ogni evento viene compresso singolarmente `EventData` e inviato in un batch fino alla dimensione massima dei messaggi. Ciò si verifica anche se vengono utilizzate [proprietà dei metadati personalizzate.](#custom-metadata-properties-for-output) <br /><br />  Quando il partizionamento di input/output è allineato, più eventi vengono compressi in un'unica `EventData` istanza, fino alla dimensione massima dei messaggi e inviati.    |
| Power BI | Vedere [Limiti dell'API Rest di Power BI.](https://msdn.microsoft.com/library/dn950053.aspx) |
| Archiviazione tabelle di Azure | Vedere [Limiti di Archiviazione di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Il valore predefinito è 100 entità per singola transazione. È possibile configurarlo su un valore inferiore in base alle esigenze. |
| Coda del bus di servizio di Azure    | 256 KB per messaggio per il livello Standard, 1 MB per il livello Premium.<br /> Vedere [Limiti del bus di servizio](../service-bus-messaging/service-bus-quotas.md). | Utilizzare un singolo evento per messaggio. |
| Argomento del bus di servizio di Azure | 256 KB per messaggio per il livello Standard, 1 MB per il livello Premium.<br /> Vedere [Limiti del bus di servizio](../service-bus-messaging/service-bus-quotas.md). | Utilizzare un singolo evento per messaggio. |
| Azure Cosmos DB    | Vedere [Limiti di Azure Cosmos DB](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | Le dimensioni del batch e la frequenza di scrittura vengono regolate dinamicamente in base alle risposte del database Cosmos di Azure.Batch size and write frequency are adjusted dynamically based on Azure Cosmos DB responses. <br /> Non ci sono limitazioni predeterminate da Analisi di flusso. |
| Funzioni di Azure    | | La dimensione predefinita del batch è 262.144 byte (256 KB). <br /> Il numero di eventi predefinito per batch è 100.The default event count per batch is 100. <br /> Le dimensioni batch sono configurabili e possono essere aumentate o ridotte nelle [opzioni di output](#azure-functions) di Analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> 
> [Avvio rapido: creare un processo di Analisi di flusso di Azure tramite il portale di Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
