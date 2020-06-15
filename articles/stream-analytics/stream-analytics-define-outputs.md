---
title: Informazioni sugli output di Analisi di flusso di Azure
description: Questo articolo descrive opzioni di output di dati disponibili in Analisi di flusso di Azure, tra cui Power BI per i risultati dell'analisi.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/8/2020
ms.openlocfilehash: c4790585d089ab287260f74001a8aa3f1cb7e5f7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647512"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Informazioni sugli output di Analisi di flusso di Azure

Questo articolo descrive i tipi di output disponibili per un processo di Analisi di flusso di Azure. Gli output consentono di archiviare e salvare i risultati del processo di Analisi di flusso di Azure. Usando i dati di output, è possibile eseguire altre analisi di business e il data warehousing dei dati.

Quando si progetta la query di Analisi di flusso, fare riferimento al nome dell'output usando la [clausola INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). È possibile usare un singolo output per ogni processo o più output per ogni processo di streaming, se necessario, specificando più clausole INTO nella query.

Per creare, modificare e testare gli output dei processi di Analisi di flusso, è possibile usare il [portale di Azure](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), l'[API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), l'[API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output) e [Visual Studio](stream-analytics-quick-create-vs.md).

Alcuni tipi di output supportano il [partizionamento](#partitioning). [Le dimensioni dei batch di output](#output-batch-size) variano per ottimizzare la velocità effettiva.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Analisi di flusso supporta [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage è un repository con iperscalabilità a livello aziendale per carichi di lavoro di analisi di Big Data. È possibile usare Data Lake Storage per archiviare dati di qualsiasi dimensione, tipo e velocità di inserimento per le analisi esplorative e operative. Analisi di flusso deve essere autorizzato ad accedere a Data Lake Storage.

L'output di Azure Data Lake Storage da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina 21Vianet e Germania (T-Systems International).

La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Data Lake Storage Gen1.   

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a Data Lake Store. |
| Subscription | Sottoscrizione contenente l'account di Azure Data Lake Storage. |
| Nome account | Nome dell'account di Data Lake Store a cui si sta inviando l'output. Viene visualizzato un elenco a discesa degli account di Data Lake Store disponibili nella sottoscrizione. |
| Schema prefisso percorso | Percorso del file usato per scrivere i file nell'account di Data Lake Store specificato. È possibile specificare una o più istanze delle variabili {date} e {time}:<br /><ul><li>Esempio 1: folder1/logs/{date}/{time}</li><li>Esempio 2: folder1/logs/{date}</li></ul><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />Se il modello di percorso del file non contiene una barra (/) finale, l'ultimo modello nel percorso del file viene considerato come prefisso del nome file. <br /><br />Vengono creati nuovi file nei casi seguenti:<ul><li>Modifica dello schema di output</li><li>Riavvio interno o esterno di un processo</li></ul> |
| Formato data | Facoltativa. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
|Formato ora | Facoltativa. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro.|
| Codifica | Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato.|
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.|
| Format | Applicabile solo per la serializzazione JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Se si seleziona **Separato da righe**, JSON viene letto un oggetto alla volta. L'intero contenuto non è di per sé un oggetto JSON valido.  **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna particolare gestione durante la scrittura del file di output.|
| Modalità di autenticazione | È possibile autorizzare l'accesso all'account di Data Lake Storage usando l'[identità gestita](stream-analytics-managed-identities-adls.md) o il token utente. Quando si concede l'accesso, è possibile revocarlo modificando la password dell'account utente, eliminando l'output di Data Lake Storage per questo processo o eliminando il processo di Analisi di flusso. |

## <a name="sql-database"></a>Database SQL

È possibile usare [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) come output per i dati di natura relazionale o per applicazioni che dipendono dal contenuto ospitato in un database relazionale. I processi di Analisi di flusso eseguono la scrittura in una tabella esistente di un database SQL. Lo schema della tabella deve corrispondere esattamente ai campi e ai relativi tipi nell'output del processo. È inoltre possibile specificare [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) come output tramite l'opzione di output del database SQL. Per informazioni sui modi che consentono di migliorare la velocità effettiva di scrittura, vedere l'articolo [Analisi di flusso con database SQL di Azure come output](stream-analytics-sql-output-perf.md).

È anche possibile usare [Istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) come output. È necessario [configurare un endpoint pubblico in Istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) e quindi configurare manualmente le impostazioni seguenti in Analisi di flusso di Azure. La macchina virtuale di Azure che esegue SQL Server con un database collegato è supportata anche dalla configurazione manuale delle impostazioni riportate di seguito.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di database SQL.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
| Database | Nome del database a cui si sta inviando l'output. |
| Nome server | Nome del server di database SQL. Per Istanza gestita di database SQL di Azure, è necessario specificare la porta 3342. Ad esempio, *sampleserver.public.database.windows.net,3342* |
| Username | Nome utente con accesso in scrittura al database. Analisi di flusso supporta solo l'autenticazione SQL. |
| Password | Password per la connessione al database. |
| Tabella | Nome della tabella in cui viene scritto l'output. Il nome della tabella fa distinzione tra maiuscole e minuscole. Lo schema di questa tabella deve corrispondere esattamente al numero di campi e ai relativi tipi generati dall'output del processo. |
|Eredita schema di partizione| Opzione che consente di ereditare lo schema di partizionamento del passaggio di query precedente, per abilitare la topologia perfettamente parallela con più writer nella tabella. Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md).|
|Numero massimo di batch| Limite massimo consigliato rispetto al numero di record inviati con ogni transazione di inserimento bulk.|

Sono disponibili due adattatori che consentono l'output da Analisi di flusso di Azure ad Azure Synapse Analytics (in precedenza SQL Data Warehouse): Database SQL e Azure Synapse. Si consiglia di scegliere l'adattatore Azure Synapse Analytics anziché l'adattatore del database SQL, se si verifica una delle condizioni seguenti:

* **Velocità effettiva**: se la velocità effettiva prevista ora o in futuro è superiore a 10 MB/sec, usare l'opzione di output di Azure Synapse per ottenere prestazioni migliori.

* **Partizioni di input**: se si dispone di otto o più partizioni di input, usare l'opzione di output di Azure Synapse per aumentare il numero di istanze.

## <a name="azure-synapse-analytics-preview"></a>Azure Synapse Analytics (anteprima)

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (in precedenza SQL Data Warehouse) è un servizio di analisi senza limiti che riunisce funzionalità aziendali di data warehousing e analisi di Big Data. 

I processi di Analisi di flusso di Azure possono inviare l'output a una tabella del pool SQL in Azure Synapse Analytics e possono elaborare velocità effettive fino a 200 MB/sec. In questo modo, vengono soddisfatte le più elevate esigenze di analisi in tempo reale e di elaborazione dei dati del percorso critico tipiche di carichi di lavoro quali la creazione di report e dashboard.  

La tabella del pool SQL deve esistere prima che sia possibile aggiungerla come output al processo di Analisi di flusso. Lo schema della tabella deve corrispondere ai campi e ai relativi tipi nell'output del processo. 

Per usare Azure Synapse come output, è necessario assicurarsi di aver configurato l'account di archiviazione. Per configurare l'account di archiviazione, passare alle impostazioni dell'account di archiviazione. Sono consentiti solo i tipi di account di archiviazione che supportano le tabelle: Utilizzo generico v2 e utilizzo generico v1. Abilitare solo il livello Standard. Il livello Premium non è supportato.   

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di Azure Synapse Analytics.

|Nome proprietà|Descrizione|
|-|-|
|Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
|Database |Nome del pool SQL in cui si sta inviando l'output. |
|Nome server |Nome del server di Azure Synapse.  |
|Username |Nome utente con accesso in scrittura al database. Analisi di flusso supporta solo l'autenticazione SQL. |
|Password |Password per la connessione al database. |
|Tabella  | Nome della tabella in cui viene scritto l'output. Il nome della tabella fa distinzione tra maiuscole e minuscole. Lo schema di questa tabella deve corrispondere esattamente al numero di campi e ai relativi tipi generati dall'output del processo.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Archiviazione BLOB e Azure Data Lake Gen2

Data Lake Storage Gen2 usa Archiviazione di Azure come base per la compilazione di Enterprise Data Lake (EDL) in Azure. Progettato dall'inizio per servire più petabyte di informazioni supportando al contempo centinaia di Gigabit di velocità effettiva, Data Lake Storage Gen2 consente di gestire facilmente grandi quantità di dati. Una parte fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno spazio dei nomi gerarchico all'archiviazione BLOB.

L'archiviazione BLOB offre una soluzione conveniente e scalabile per archiviare grandi quantità di dati non strutturati nel cloud. Per un'introduzione all'archiviazione BLOB e al relativo utilizzo, vedere [Caricare, scaricare ed elencare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un BLOB o di output di ADLS Gen2.

| Nome proprietà       | Descrizione                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias di output        | Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione BLOB. |
| Account di archiviazione     | Nome dell'account di archiviazione a cui si sta inviando l'output.               |
| Chiave dell'account di archiviazione | Chiave privata associata all'account di archiviazione.                              |
| Contenitore di archiviazione   | Raggruppamento logico dei BLOB archiviati nel servizio BLOB di Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB. |
| Modello di percorso | Facoltativa. Modello di percorso del file usato per scrivere i BLOB nel contenitore specificato. <br /><br /> Nel modello del percorso è possibile scegliere di usare una o più istanze delle variabili di data e ora per specificare la frequenza di scrittura dei BLOB: <br /> {date}, {time} <br /><br />È possibile usare un partizionamento BLOB personalizzato per specificare un nome personalizzato {field} dai dati di evento ai BLOB di partizione. Il nome del campo è un valore alfanumerico e può includere spazi, trattini e caratteri di sottolineatura. Le restrizioni sui campi personalizzati includono le seguenti: <ul><li>Per i nomi dei campi non viene fatta distinzione tra maiuscole e minuscole. Ad esempio, il servizio non distingue tra le colonne "ID" e "id".</li><li>I campi annidati non sono consentiti. Usare, in alternativa, un alias della query di processo per rendere flat il campo.</li><li>Le espressioni non possono essere usate come nome di campo.</li></ul> <br />Questa funzionalità consente di usare anche configurazioni dell'identificatore di formato data/ora personalizzate nel percorso. I formati di data e ora personalizzati devono essere specificati uno alla volta, racchiusi tra la parola chiave {datetime:\<identificatore >}. Gli input consentiti per \<identificatore> sono aaaa, MM, M, gg, g, HH, H, mm, m, ss o s. La parola chiave {datetime:\<identificatore>} può essere usata più volte nel percorso, in modo da formare delle configurazioni data/ora personalizzate. <br /><br />Esempi: <ul><li>Esempio 1: cluster1/logs/{date}/{time}</li><li>Esempio 2: cluster1/logs/{date}</li><li>Esempio 3: cluster1/{client_id}/{date}/{time}</li><li>Esempio 4: cluster1/{datetime:ss}/{myField} in cui la query è: SELECT data.myField AS myField FROM Input;</li><li>Esempio 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />La denominazione dei file usa questa convenzione: <br /><br />{Schema prefisso percorso}/schemaHashcode_Guid_Number.extension<br /><br />File di output di esempio:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Per altre informazioni su questa funzionalità, visitare [Partizionamento dell'output dei BLOB personalizzato in Analisi di flusso di Azure](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato data | Facoltativa. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
| Formato ora | Facoltativa. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati JSON, CSV, Avro e Parquet. |
|Numero minimo di righe (solo Parquet)|Numero minimo di righe per batch. Per Parquet, ogni batch creerà un nuovo file. Il valore predefinito corrente è 2000 righe e il valore massimo consentito è 10.000 righe.|
|Tempo massimo (solo Parquet)|Tempo massimo di attesa per batch. Dopo questo periodo di tempo il batch verrà scritto nell'output anche se il requisito relativo al numero minimo di righe non è stato soddisfatto. Il valore predefinito corrente è 1 minuto e il valore massimo consentito è 2 ore. Se l'output del BLOB ha la frequenza del modello di percorso, il tempo di attesa non può essere superiore all'intervallo di tempo della partizione.|
| Codifica    | Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore   | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format      | Applicabile solo per la serializzazione JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Se si seleziona **Separato da righe**, JSON viene letto un oggetto alla volta. L'intero contenuto non è di per sé un oggetto JSON valido. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna particolare gestione durante la scrittura del file di output. |

Quando si usa l'archiviazione BLOB come output, nei casi seguenti viene creato un nuovo file nel BLOB:

* Se il file supera il numero massimo di blocchi consentiti (attualmente 50.000). È possibile raggiungere il numero massimo di blocchi consentiti senza raggiungere le dimensioni massime consentite per il BLOB. Ad esempio, se la frequenza di output è elevata, ogni blocco può contenere più byte e le dimensioni del file sono superiori. Se la frequenza di output è bassa, ogni blocco contiene meno dati e le dimensioni del file sono inferiori.
* Se l'output contiene una modifica dello schema e il formato di output richiede uno schema fisso (CSV e Avro).
* Se un processo viene riavviato, esternamente con l'arresto e l'avvio da parte di un utente oppure internamente per la manutenzione del sistema o il recupero da errori.
* Se la query è completamente partizionata e, per ogni partizione di output, viene creato un nuovo file.
* Se l'utente elimina un file o un contenitore dell'account di archiviazione.
* Se l'output è partizionato in base al tempo usando lo schema prefisso percorso, quando la query passa all'ora successiva viene usato un nuovo BLOB.
* Se l'output è partizionato da un campo personalizzato, viene creato un nuovo BLOB per ogni chiave di partizione, se inesistente.
* Se l'output è partizionato da un campo personalizzato in cui la cardinalità della chiave di partizione supera 8000 e viene creato un nuovo BLOB per ogni chiave di partizione.

## <a name="event-hubs"></a>Hub eventi

Il servizio [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) è un inseritore eventi di pubblicazione-sottoscrizione altamente scalabile. Può raccogliere milioni di eventi al secondo. L'uso di un hub eventi come output si verifica quando l'output di un processo di Analisi di flusso di Azure diventa l'input di un altro processo di streaming. Per informazioni sulle dimensioni massime dei messaggi e sull'ottimizzazione delle dimensioni del batch, vedere la sezione [Dimensione del batch di output](#output-batch-size).

Per configurare i flussi di dati dagli hub eventi come output, sono necessari alcuni parametri.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a questo hub eventi. |
| Spazio dei nomi dell'hub eventi | Contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi dell'hub eventi. |
| Nome hub eventi | Nome dell'output dell'hub eventi. |
| Nome criteri hub eventi | Criteri di accesso condivisi che è possibile creare nella scheda **Configura** dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri hub eventi | Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi dell'hub eventi. |
| Colonna chiave di partizione | Facoltativa. Colonna che contiene la chiave di partizione per l'output dell'hub eventi. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica | Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format | Applicabile solo per la serializzazione JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Se si seleziona **Separato da righe**, JSON viene letto un oggetto alla volta. L'intero contenuto non è di per sé un oggetto JSON valido. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON.  |
| Colonne delle proprietà | Facoltativa. Colonne delimitate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché del payload. Altre informazioni su questa funzionalità sono illustrate nella sezione [Proprietà dei metadati personalizzati per l'output](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

È possibile usare [Power BI](https://powerbi.microsoft.com/) come output per un processo di Analisi di flusso per offrire un'esperienza di visualizzazione avanzata dei risultati di analisi. È possibile usare questa funzionalità per i dashboard operativi, la generazione di report e la creazione di report basati sulle metriche.

L'output di Power BI da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina 21Vianet e Germania (T-Systems International).

La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Power BI.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Consente di specificare un nome descrittivo usato nelle query per indirizzare l'output delle query a questo output di Power BI. |
| Area di lavoro del gruppo |Per abilitare la condivisione dei dati con altri utenti Power BI, è possibile selezionare i gruppi all'interno dell'account Power BI o scegliere **Area di lavoro personale** se non si vuole eseguire la scrittura in un gruppo. L'aggiornamento di un gruppo esistente richiede il rinnovo dell'autenticazione di Power BI. |
| Nome del set di dati |Consente di specificare un nome per il set di dati che dovrà essere usato dall'output di Power BI. |
| Nome tabella |Immettere un nome per la tabella nel set di dati dell'output di Power BI. L'output di Power BI da processi di Analisi di flusso può avere al momento solo una tabella in un set di dati. |
| Autorizzare connessione | Per configurare le impostazioni di output, è necessario eseguire l'autorizzazione con Power BI. Quando si concede l'accesso dell'output al dashboard di Power BI, è possibile revocarlo modificando la password dell'account utente, eliminando l'output del processo o eliminando il processo di Analisi di flusso. | 

Per una procedura dettagliata della configurazione di un output di Power BI e del dashboard, vedere l'esercitazione [Analisi di flusso di Azure e Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Non creare in modo esplicito il set di dati e la tabella nel dashboard di Power BI. Il set di dati e la tabella vengono compilati automaticamente quando il processo viene avviato e inizia a generare output in Power BI. Se la query di processo non genera alcun risultato, il set di dati e la tabella non vengono creati. Se Power BI ha già un set di dati e una tabella con lo stesso nome fornito dall'utente nel processo di Analisi di flusso, i dati esistenti vengono sovrascritti.
>

### <a name="create-a-schema"></a>Creare uno schema
Analisi di flusso di Azure crea un set di dati e uno schema di tabella di Power BI per conto dell'utente, se non esistono già. In tutti gli altri casi, la tabella viene aggiornata con nuovi valori. Attualmente, all'interno di un set di dati può esistere una sola tabella. 

Power BI usa i criteri di conservazione FIFO. I dati verranno raccolti in una tabella fino al raggiungimento di 200.000 righe.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Convertire tipi di dati da Analisi di flusso di Azure a Power BI
Analisi di flusso di Azure consente di aggiornare il modello di dati in modo dinamico in fase di esecuzione se viene modificato lo schema di output. Vengono rilevate tutte le modifiche al nome e al tipo di colonna e l'aggiunta o la rimozione di colonne.

Questa tabella contiene le conversioni dei tipi di dati dai [tipi di dati di Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) ai [tipi Entity Data Model (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) di Power BI, se non esistono né un set di dati né una tabella di Power BI.

Dall'analisi di flusso | A Power BI
-----|-----
bigint | Int64
nvarchar(max) | string
Datetime | Datetime
float | Double
Matrice di record | Tipo String, valore di tipo Constant "IRecord" o "IArray"

### <a name="update-the-schema"></a>Aggiornare lo schema
L'analisi di flusso deduce lo schema del modello di dati in base al primo set di eventi dell'output. In un secondo momento, se necessario, lo schema del modello di dati viene aggiornato per gestire gli eventi in ingresso che potrebbero non rientrare nello schema originale.

Non usare la query `SELECT *` per evitare l'aggiornamento dinamico dello schema nelle righe. Oltre a implicazioni potenziali sulle prestazioni, potrebbe anche verificarsi un problema di incertezza rispetto al tempo necessario per ottenere i risultati. Selezionare i campi esatti che devono essere visualizzati nel dashboard di Power BI. È anche necessario che i valori dei dati siano conformi al tipo di dati scelto.


Precedente/Corrente | Int64 | string | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | string | string | Double
Double | Double | string | string | Double
string | string | string | string | string 
Datetime | string | string |  Datetime | string

## <a name="table-storage"></a>Archiviazione tabelle

[archiviazione tabelle di Azure](../storage/common/storage-introduction.md) offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica di un'applicazione in base alle richieste degli utenti. Archiviazione tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft che è possibile usare per i dati strutturati con meno vincoli allo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output della tabella.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione tabelle. |
| Account di archiviazione |Nome dell'account di archiviazione a cui si sta inviando l'output. |
| Chiave dell'account di archiviazione |Chiave di accesso associata all'account di archiviazione. |
| Nome tabella |Nome della tabella. Se non esiste, viene creata una nuova tabella. |
| Chiave di partizione |Nome della colonna di output contenente la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione di una tabella che costituisce la prima parte della chiave primaria di un'entità. Si tratta di un valore stringa le cui dimensioni massime sono di 1 KB. |
| Chiave di riga |Nome della colonna di output che contiene la chiave di riga. La chiave di riga è un identificatore univoco per un'entità all'interno di una partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa le cui dimensioni massime sono di 1 KB. |
| Dimensioni dei batch |Numero di record per un'operazione batch. Il valore predefinito (100) è sufficiente per la maggior parte dei processi. Per altre informazioni sulla modifica di questa impostazione, vedere la [specifica relativa alle operazioni batch su tabella](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation). |

## <a name="service-bus-queues"></a>Code del bus di servizio

Le [code del bus di servizio](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) consentono un recapito dei messaggi di tipo FIFO a uno o più consumer concorrenti. In genere, i messaggi vengono ricevuti ed elaborati dai destinatari nell'ordine temporale in cui sono stati aggiunti alla coda. Ogni messaggio viene ricevuto ed elaborato da un solo consumer di messaggi.

Nel [livello di compatibilità 1.2](stream-analytics-compatibility-level.md), Analisi di flusso di Azure usa il protocollo di messaggistica [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) per la scrittura in code e argomenti del bus di servizio. AMQP consente di creare applicazioni ibride multipiattaforma usando un protocollo aperto standard.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output della coda.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa coda del bus di servizio. |
| Spazio dei nomi del bus di servizio |Contenitore per un set di entità di messaggistica. |
| Nome della coda |Nome della coda del bus di servizio. |
| Nome criteri coda |Durante la creazione di una coda, nella scheda **Configura** della coda è anche possibile creare criteri di accesso condiviso. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri coda |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format |Applicabile solo per il tipo JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Se si seleziona **Separato da righe**, JSON viene letto un oggetto alla volta. L'intero contenuto non è di per sé un oggetto JSON valido. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. |
| Colonne delle proprietà | Facoltativa. Colonne delimitate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché del payload. Altre informazioni su questa funzionalità sono illustrate nella sezione [Proprietà dei metadati personalizzati per l'output](#custom-metadata-properties-for-output). |
| Colonne delle proprietà di sistema | Facoltativa. Coppie chiave-valore delle proprietà di sistema e nomi di colonna corrispondenti che devono essere allegati al messaggio in uscita anziché al payload. Altre informazioni su questa funzionalità sono riportate nella sezione [Proprietà di sistema per gli output di code e argomenti del bus di servizio](#system-properties-for-service-bus-queue-and-topic-outputs)  |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="service-bus-topics"></a>Argomenti del bus di servizio
Le code del bus di servizio forniscono un metodo di comunicazione uno-a-uno dal mittente al destinatario. Gli [argomenti del bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrono una forma di comunicazione uno-a-molti.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output dell'argomento del bus di servizio.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo argomento del bus di servizio. |
| Spazio dei nomi del bus di servizio |Contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio. |
| Nome argomento |Gli argomenti sono entità di messaggistica, simili agli Hub eventi e alle code. Sono progettati per raccogliere i flussi di eventi da dispositivi e servizi. Quando un argomento viene creato, gli viene anche assegnato un nome specifico. Dal momento che i messaggi inviati a un argomento non sono disponibili se non viene creata una sottoscrizione, assicurarsi che esistano una o più sottoscrizioni per l'argomento. |
| Nome criteri argomento |Durante la creazione di un argomento del bus di servizio, nella scheda **Configura** dell'argomento è anche possibile creare criteri di accesso condiviso. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri argomento |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Colonne delle proprietà | Facoltativa. Colonne delimitate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché del payload. Altre informazioni su questa funzionalità sono illustrate nella sezione [Proprietà dei metadati personalizzati per l'output](#custom-metadata-properties-for-output). |
| Colonne delle proprietà di sistema | Facoltativa. Coppie chiave-valore delle proprietà di sistema e nomi di colonna corrispondenti che devono essere allegati al messaggio in uscita anziché al payload. Altre informazioni su questa funzionalità sono riportate nella sezione [Proprietà di sistema per gli output di code e argomenti del bus di servizio](#system-properties-for-service-bus-queue-and-topic-outputs) |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) è un servizio di database distribuito a livello globale che offre scalabilità elastica illimitata in tutto il mondo, query avanzate e indicizzazione automatica su modelli di dati indipendenti dallo schema. Per informazioni sulle opzioni del contenitore di Cosmos DB per Analisi di flusso, vedere l'articolo [Analisi di flusso con Aure Cosmos DB come output](stream-analytics-documentdb-output.md).

L'output di Azure Cosmos DB da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina 21Vianet e Germania (T-Systems International).

> [!Note]
> Ad oggi, Analisi di flusso di Azure supporta solo la connessione ad Azure Cosmos DB tramite l'API SQL.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente.

Nella tabella seguente sono descritte le proprietà per la creazione di un output di Azure Cosmos DB.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output | Alias per fare riferimento a questo output nella query di Analisi di flusso di Azure. |
| Sink | Azure Cosmos DB. |
| Opzione di importazione | Scegliere **Selezionare Cosmos DB dalle sottoscrizioni correnti** o **Specificare le impostazioni di Cosmos DB manualmente**.
| Account ID | Nome o URI endpoint dell'account Azure Cosmos DB. |
| Chiave account | Chiave di accesso condiviso per l'account Azure Cosmos DB. |
| Database | Nome del database Azure Cosmos DB. |
| Nome contenitore | Nome del contenitore da usare, che deve esistere in Cosmos DB. Esempio:  <br /><ul><li> _ContenitorePersonale_: deve esistere un contenitore denominato "ContenitorePersonale".</li>|
| Document ID |Facoltativa. Il nome del campo negli eventi di output usato per specificare la chiave primaria su cui si basano le operazioni di inserimento o aggiornamento.

## <a name="azure-functions"></a>Funzioni di Azure
Funzioni di Azure è un servizio di calcolo senza server che è possibile usare per eseguire codice su richiesta senza dover gestire l'infrastruttura o effettuare il provisioning in modo esplicito. Consente di implementare il codice attivato da eventi generati nei servizi di Azure o di un partner. La possibilità di rispondere ai trigger rende Funzioni di Azure l'output naturale per Analisi di flusso di Azure. Questo adattatore di output consente agli utenti di collegare Analisi di flusso a Funzioni di Azure ed eseguire uno script o una porzione di codice in risposta a diversi eventi.

L'output di Funzioni di Azure da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina 21Vianet e Germania (T-Systems International).

Analisi di flusso di Azure richiama Funzioni di Azure tramite trigger HTTP. L'adattatore di output di Funzioni di Azure è disponibile con le proprietà configurabili seguenti:

| Nome proprietà | Descrizione |
| --- | --- |
| App per le funzioni |Nome dell'app Funzioni di Azure. |
| Funzione |Nome della funzione nell'app Funzioni di Azure. |
| Chiave |Per usare una funzione di Azure da un'altra sottoscrizione, è necessario fornire la chiave per accedere alla funzione. |
| Dimensioni massime batch |Proprietà che consente di impostare le dimensioni massime per ogni batch di output inviato a Funzioni di Azure. L'unità di input è espressa in byte. Per impostazione predefinita, questo valore è 262.144 byte (256 KB). |
| Numero massimo di batch  |Proprietà che consente di specificare il numero massimo di eventi in ogni batch che vengono inviati a Funzioni di Azure. Il valore predefinito è 100. |

Analisi di flusso di Azure prevede lo stato HTTP 200 dall'app Funzioni per i batch elaborati correttamente.

Quando Analisi di flusso di Azure riceve l'eccezione 413 ("Entità richiesta HTTP troppo grande") da una funzione di Azure, riduce la dimensione dei batch che invia a Funzioni di Azure. Usare questa eccezione nel codice della funzione di Azure per fare in modo che Analisi di flusso di Azure non invii batch troppo grandi. Assicurarsi anche che i valori relativi alle dimensioni e al numero massimo di batch usati nella funzione siano conformi ai valori inseriti nel portale di Analisi di flusso.

> [!NOTE]
> Durante il test della connessione, Analisi di flusso invia un batch vuoto a Funzioni di Azure per verificare se la connessione tra i due funziona. Assicurarsi che l'app Funzioni gestisca le richieste batch vuote per consentire l'esito positivo del test della connessione.

Quando non avviene alcun evento in un intervallo di tempo, non viene generato alcun output. Di conseguenza, non viene chiamata la funzione **computeResult**. Questo comportamento è coerente con le funzioni di aggregazione finestra predefinite.

## <a name="custom-metadata-properties-for-output"></a>Proprietà dei metadati personalizzati per l'output 

È possibile aggiungere colonne di query come proprietà utente ai messaggi in uscita. Queste colonne non vengono inserite nel payload. Le proprietà sono presenti sotto forma di dizionario nel messaggio di output. *Key* è il nome della colonna e *value* è il valore della colonna nel dizionario delle proprietà. Sono supportati tutti i tipi di dati di Analisi di flusso, ad eccezione di record e matrice.  

Output supportati: 
* Coda del bus di servizio 
* Argomento del bus di servizio 
* Hub eventi 

Nell'esempio seguente vengono aggiunti i due campi `DeviceId` e `DeviceStatus` ai metadati. 
* Query: `select *, DeviceId, DeviceStatus from iotHubInput`
* Configurazione di output: `DeviceId,DeviceStatus`

![Colonne delle proprietà](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Lo screenshot seguente mostra le proprietà dei messaggi di output ispezionate in EventHub tramite [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Proprietà personalizzate dell'evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Proprietà di sistema per gli output di code e argomenti del bus di servizio 
È possibile aggiungere colonne di query come [proprietà di sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) ai messaggi in uscita di code o argomenti del bus di servizio. Queste colonne non vengono inserite nel payload, bensì la [proprietà di sistema](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage corrispondente viene popolata con i valori della colonna di query.
Sono supportate le proprietà di sistema seguenti: `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
I valori stringa di queste colonne vengono analizzati come tipo di valore della proprietà di sistema corrispondente e gli eventuali errori di analisi vengono considerati come errori di dati.
Questo campo viene fornito come formato oggetto JSON. I dettagli su questo formato sono i seguenti:
* Racchiuso tra parentesi graffe {}.
* Scritto in coppie chiave/valore.
* Chiavi e valori devono essere stringhe.
* Key è il nome della proprietà di sistema e value è il nome della colonna della query.
* Chiavi e valori sono separati da due punti.
* Ogni coppia chiave/valore è separata da una virgola.

Di seguito, viene illustrato come usare questa proprietà:

* Query: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colonne delle proprietà di sistema: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Viene impostato `MessageId` nei messaggi della coda del bus di servizio con i valori di `column1` e PartitionKey viene impostato con i valori di `column2`.

## <a name="partitioning"></a>Partizionamento

Nella tabella seguente viene riepilogato il supporto della partizione e il numero di writer di output per ogni tipo di output:

| Tipo di output | Supporto del partizionamento | Chiave di partizione  | Numero di writer di output |
| --- | --- | --- | --- |
| Archivio Azure Data Lake | Sì | Usare i token {date} e {time} nello schema prefisso percorso. Scegliere il formato della data, ad esempio AAAA/MM/GG, GG/MM/AAAA o MM-GG-AAAA. HH viene usato per il formato dell'ora. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| database SQL di Azure | Sì, deve essere abilitato. | Basato sulla clausola PARTITION BY nella query. | Quando l'opzione per ereditare il partizionamento è abilitata, segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). Per altre informazioni su come ottenere migliori prestazioni per la velocità effettiva di scrittura quando si caricano dati nel database SQL di Azure, vedere [Output di Analisi di flusso di Azure nel database SQL di Azure](stream-analytics-sql-output-perf.md). |
| Archiviazione BLOB di Azure | Sì | Usare i token {date} e {time} dai campi dell'evento nel modello di percorso. Scegliere il formato della data, ad esempio AAAA/MM/GG, GG/MM/AAAA o MM-GG-AAAA. HH viene usato per il formato dell'ora. L'output del BLOB può essere partizionato in base a un singolo attributo dell'evento personalizzato {fieldname} o {datetime:\<specifier>}. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Hub eventi di Azure | Sì | Sì | Varia a seconda dell'allineamento della partizione.<br /> Quando la chiave di partizione per l'output dell'hub eventi è ugualmente allineata al passaggio di query upstream (precedente), il numero di writer è uguale al numero di partizioni dell'hub eventi. Ogni writer usa la [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet), per inviare eventi alla partizione specifica. <br /> Quando la chiave di partizione per l'output dell'hub eventi non è allineata al passaggio di query upstream (precedente), il numero di writer è uguale al numero di partizioni in tale passaggio precedente. Ogni writer usa la [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) in **EventHubClient**, per inviare eventi a tutte le partizioni di output. |
| Power BI | No | nessuno | Non applicabile. |
| Archiviazione tabelle di Azure | Sì | Qualsiasi colonna di output.  | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Argomento del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.| Corrisponde al numero di partizioni nell'argomento di output.  |
| Coda del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.| Corrisponde al numero di partizioni nella coda di output. |
| Azure Cosmos DB | Sì | Basato sulla clausola PARTITION BY nella query. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Funzioni di Azure | Sì | Basato sulla clausola PARTITION BY nella query. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |

Il numero di writer di output può essere controllato anche usando la clausola `INTO <partition count>` (vedere [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) nella query, che può essere utile per ottenere la topologia del processo desiderata. Se l'adattatore di output non è partizionato, la mancanza di dati in una partizione di input causerà un ritardo fino alla quantità di tempo di arrivo in ritardo. In questi casi, l'output viene unito a un unico writer che può causare colli di bottiglia nella pipeline. Per altre informazioni sui criteri di arrivo in ritardo, vedere [Considerazioni sull'ordine degli eventi con Analisi di flusso di Azure](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Dimensione del batch di output
Analisi di flusso di Azure usa batch di dimensioni variabili per l'elaborazione degli eventi e la scrittura negli output. Il motore di Analisi di flusso non scrive generalmente un messaggio alla volta e usa i batch per migliorare l'efficienza. Quando la frequenza degli eventi in ingresso e in uscita è elevata, Analisi di flusso usa batch più grandi. Quando la frequenza in uscita è bassa, usa batch di dimensioni minori per mantenere bassa la latenza.

La tabella seguente spiega alcune considerazioni per l'invio in batch dell'output:

| Tipo di output |    Dimensioni massime messaggio | Ottimizzazione delle dimensioni batch |
| :--- | :--- | :--- |
| Archivio Azure Data Lake | Vedere [Limiti di Data Lake Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Usare fino a 4 MB per ogni operazione di scrittura. |
| database SQL di Azure | Configurabile con numero massimo di batch. Per impostazione predefinita, il numero massimo di righe è 10.000 e il numero minimo è 100 per singolo inserimento bulk.<br />Vedere [Limiti di Azure SQL](../sql-database/sql-database-resource-limits.md). |  Per ogni batch viene inizialmente eseguito l'inserimento bulk con il numero massimo di batch. Il batch può essere diviso a metà (fino al numero minimo di batch) in base a errori SQL non irreversibili. |
| Archiviazione BLOB di Azure | Vedere [Limiti di Archiviazione di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Le dimensioni massime dei blocchi di BLOB sono pari a 4 MB.<br />Il numero massimo di blocchi di BLOB è 50.000. |
| Hub eventi di Azure    | 256 KB o 1 MB per ogni messaggio. <br />Vedere [Limiti di Hub eventi](../event-hubs/event-hubs-quotas.md). |    Quando il partizionamento di input/output non è allineato, ogni evento viene inserito individualmente in `EventData` e inviato in un batch che può raggiungere le dimensioni massime del messaggio. Ciò si verifica anche se vengono usate [proprietà dei metadati personalizzate](#custom-metadata-properties-for-output). <br /><br />  Quando il partizionamento di input/output è allineato, più eventi vengono inseriti in una singola istanza di `EventData`, fino alle dimensioni massime del messaggio, e inviati.    |
| Power BI | Vedere [Limiti dell'API REST di Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Archiviazione tabelle di Azure | Vedere [Limiti di Archiviazione di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Il valore predefinito è 100 entità per singola transazione. È possibile configurarlo su un valore inferiore in base alle esigenze. |
| Coda del bus di servizio di Azure    | 256 KB per messaggio per il livello Standard, 1 MB per il livello Premium.<br /> Vedere [Limiti del bus di servizio](../service-bus-messaging/service-bus-quotas.md). | Usare un evento singolo per ogni messaggio. |
| Argomento del bus di servizio di Azure | 256 KB per messaggio per il livello Standard, 1 MB per il livello Premium.<br /> Vedere [Limiti del bus di servizio](../service-bus-messaging/service-bus-quotas.md). | Usare un evento singolo per ogni messaggio. |
| Azure Cosmos DB    | Vedere [Limiti di Azure Cosmos DB](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | Le dimensioni batch e la frequenza di scrittura vengono modificate in modo dinamico in base alle risposte di Azure CosmosDB. <br /> Non sono presenti limiti predeterminati per Analisi di flusso. |
| Funzioni di Azure    | | La dimensione del batch predefinita è 262.144 byte (256 KB). <br /> Il numero di eventi predefinito per ogni batch è 100. <br /> Le dimensioni batch sono configurabili e possono essere aumentate o ridotte nelle [opzioni di output](#azure-functions) di Analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> 
> [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
