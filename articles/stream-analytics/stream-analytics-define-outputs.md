---
title: Informazioni sugli output di Analisi di flusso di Azure
description: Questo articolo descrive opzioni di output di dati disponibili in Analisi di flusso di Azure, tra cui Power BI per i risultati dell'analisi.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: 3b242ff8ee3e635493cd501cf37ffc7c78a57d91
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563312"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Informazioni sugli output di Analisi di flusso di Azure

Questo articolo descrive i tipi di output disponibili per un processo di analisi di flusso di Azure. Gli output consentono di archiviare e salvare i risultati del processo di Analisi di flusso di Azure. Utilizzando i dati di output, è possibile eseguire ulteriori analisi aziendali e data warehousing dei dati.

Quando si progetta la query di analisi di flusso, fare riferimento al nome dell'output usando la [clausola into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). È possibile usare un singolo output per ogni processo o più output per ogni processo di streaming (se necessario) fornendo più clausole INTO nella query.

Per creare, modificare e testare gli output del processo di analisi di flusso, è possibile usare l' [portale di Azure](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), l' [API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), l' [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)e [Visual Studio](stream-analytics-quick-create-vs.md).

Alcuni tipi di output supportano il [partizionamento](#partitioning). Le [dimensioni dei batch di output](#output-batch-size) variano per ottimizzare la velocità effettiva.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Analisi di flusso supporta [Azure Data Lake storage generazione 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage è un repository con iperscalabilità a livello aziendale per Big Data carichi di lavoro analitici. È possibile usare Data Lake Storage per archiviare dati di qualsiasi dimensione, tipo e velocità di inserimento per le analisi operative ed esplorative. Analisi di flusso deve essere autorizzato ad accedere a Data Lake Storage.

Azure Data Lake Storage output di analisi di flusso non è attualmente disponibile nelle aree Azure China 21Vianet e Azure Germania (T-Systems International).

La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per configurare l'output di Data Lake Storage generazione 1.   

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output della query a Data Lake Store. |
| Sottoscrizione | Sottoscrizione che contiene l'account Azure Data Lake Storage. |
| Nome account | Nome dell'account Data Lake Store a cui si sta inviando l'output. Viene visualizzato un elenco a discesa di account Data Lake Store disponibili nella sottoscrizione. |
| Schema prefisso percorso | Percorso del file usato per scrivere i file nell'account di Data Lake Store specificato. È possibile specificare una o più istanze delle variabili {date} e {Time}:<br /><ul><li>Esempio 1: folder1/logs/{date}/{time}</li><li>Esempio 2: folder1/logs/{date}</li></ul><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />Se il modello di percorso del file non contiene una barra finale (/), l'ultimo modello nel percorso del file viene considerato come prefisso del nome file. <br /><br />Vengono creati nuovi file nei casi seguenti:<ul><li>Modifica dello schema di output</li><li>Riavvio interno o esterno di un processo</li></ul> |
| Formato data | facoltativo. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
|Formato ora | facoltativo. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro.|
| Codifica | Se si usa il formato CSV o JSON, è necessario specificare una codifica. Al momento, UTF-8 è l'unico formato di codifica supportato.|
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.|
| Formato | Applicabile solo per la serializzazione JSON. **Delimitato da righe** specifica che l'output viene formattato in base a ogni oggetto JSON separato da una nuova riga. **Array** specifica che l'output viene formattato come una matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON delimitati da righe, perché non richiede alcuna gestione speciale mentre è ancora in corso la scrittura del file di output.|
| Modalità di autenticazione | È possibile autorizzare l'accesso all'account di Data Lake Storage usando l' [identità gestita](stream-analytics-managed-identities-adls.md) o il token utente. Quando si concede l'accesso, è possibile revocare l'accesso modificando la password dell'account utente, eliminando l'output Data Lake Storage per questo processo o eliminando il processo di analisi di flusso. |

## <a name="sql-database"></a>Database SQL

È possibile usare il [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) come output per i dati di natura relazionale o per applicazioni che dipendono dal contenuto ospitato in un database relazionale. I processi di analisi di flusso scrivono in una tabella esistente del database SQL. Lo schema della tabella deve corrispondere esattamente ai campi e ai relativi tipi nell'output del processo. È anche possibile specificare [Azure SQL data warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) come output tramite l'opzione di output del database SQL. Per informazioni sui modi per migliorare la velocità effettiva di scrittura, vedere l'articolo [analisi di flusso con database SQL di Azure come output](stream-analytics-sql-output-perf.md) .

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output del database SQL.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
| Database | Nome del database in cui si sta inviando l'output. |
| Nome del server | Nome del server di database SQL. |
| Nome utente | Nome utente con accesso in scrittura al database. Analisi di flusso supporta solo l'autenticazione SQL. |
| Password | Password per la connessione al database. |
| Tabella | Nome della tabella in cui viene scritto l'output. Il nome della tabella fa distinzione tra maiuscole e minuscole. Lo schema di questa tabella deve corrispondere esattamente al numero di campi e ai relativi tipi generati dall'output del processo. |
|Eredita schema di partizione| Opzione per l'ereditarietà dello schema di partizionamento del passaggio precedente della query, per consentire la topologia completamente parallela con più writer alla tabella. Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md).|
|Numero massimo di batch| Limite superiore consigliato per il numero di record inviati con ogni transazione di inserimento bulk.|

> [!NOTE]
> L'offerta del database SQL di Azure è supportata per l'output di un processo in analisi di flusso, ma una macchina virtuale di Azure che esegue SQL Server con un database collegato o in una SQL Azure Istanza gestita non è ancora supportata. Questo comportamento sarà soggetto a modifiche nelle versioni future.

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Archiviazione BLOB e Azure Data Lake Gen2

L'uscita per Azure Data Lake Gen2 viene offerta come funzionalità di anteprima in aree limitate in tutto il mondo. Per richiedere l'accesso all'anteprima, è possibile fornire dettagli aggiuntivi nel [modulo di richiesta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).

Archiviazione BLOB di Azure offre una soluzione conveniente e scalabile per l'archiviazione di grandi quantità di dati non strutturati nel cloud. Per un'introduzione all'archiviazione BLOB e al relativo utilizzo, vedere [caricare, scaricare ed elencare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per la creazione di un output del BLOB.

| Nome proprietà       | DESCRIZIONE                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias di output        | Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione BLOB. |
| Account di archiviazione     | Nome dell'account di archiviazione in cui si sta inviando l'output.               |
| Chiave dell'account di archiviazione | Chiave privata associata all'account di archiviazione.                              |
| Contenitore di archiviazione   | Raggruppamento logico per i BLOB archiviati nel servizio BLOB di Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB. |
| Modello percorso | facoltativo. Modello di percorso del file usato per scrivere i BLOB nel contenitore specificato. <br /><br /> Nel modello di percorso è possibile scegliere di usare una o più istanze delle variabili di data e ora per specificare la frequenza di scrittura dei BLOB: <br /> {date}, {time} <br /><br />È possibile usare un partizionamento BLOB personalizzato per specificare un nome personalizzato {field} dai dati di evento ai BLOB di partizione. Il nome del campo è un valore alfanumerico e può includere spazi, trattini e caratteri di sottolineatura. Le restrizioni sui campi personalizzati includono le seguenti: <ul><li>Il nome dei campi non distingue tra maiuscole e minuscole Il servizio, ad esempio, non può distinguere tra la colonna "ID" e la colonna "ID".</li><li>I campi annidati non sono consentiti. Usare invece un alias nella query del processo per "appiattire" il campo.</li><li>Non è possibile usare le espressioni come nome di campo.</li></ul> <br />Questa funzionalità consente di usare anche configurazioni dell'identificatore di formato data/ora personalizzate nel percorso. I formati di data e ora personalizzati devono essere specificati uno alla volta, racchiusi tra la parola chiave {datetime:\<identificatore >}. Gli input consentiti per \<> identificatore sono aaaa, mm, m, GG, d, HH, H, mm, m, SS o s. La parola chiave {\<DateTime: specifier >} può essere usata più volte nel percorso per creare configurazioni di data/ora personalizzate. <br /><br />Esempi: <ul><li>Esempio 1: cluster1/logs/{date}/{time}</li><li>Esempio 2: cluster1/logs/{date}</li><li>Esempio 3: cluster1/{client_id}/{date}/{time}</li><li>Esempio 4: cluster1/{datetime:ss}/{myField} in cui la query è: SELECT data.myField AS myField FROM Input;</li><li>Esempio 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />La denominazione dei file usa la convenzione seguente: <br /><br />{Schema prefisso percorso}/schemaHashcode_Guid_Number.extension<br /><br />File di output di esempio:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Per altre informazioni su questa funzionalità, vedere [partizionamento di output BLOB personalizzato di analisi di flusso di Azure](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato data | facoltativo. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
| Formato ora | facoltativo. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati JSON, CSV, avro e parquet. |
|Numero minimo di righe (solo parquet)|Numero minimo di righe per batch. Per parquet, ogni batch creerà un nuovo file. Il valore predefinito corrente è 2.000 righe e il valore massimo consentito è 10.000 righe.|
|Tempo massimo (solo parquet)|Tempo massimo di attesa per batch. Al termine di questo periodo, il batch verrà scritto nell'output anche se non viene soddisfatto il requisito di righe minime. Il valore predefinito corrente è 1 minuto e il valore massimo consentito è 2 ore. Se l'output del BLOB ha la frequenza del modello di percorso, il tempo di attesa non può essere superiore all'intervallo di tempo della partizione.|
| Codifica    | Se si usa il formato CSV o JSON, è necessario specificare una codifica. Al momento, UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore   | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Formato      | Applicabile solo per la serializzazione JSON. **Delimitato da righe** specifica che l'output viene formattato in base a ogni oggetto JSON separato da una nuova riga. **Array** specifica che l'output viene formattato come una matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON delimitati da righe, perché non richiede alcuna gestione speciale mentre è ancora in corso la scrittura del file di output. |

Quando si usa l'archiviazione BLOB come output, viene creato un nuovo file nel BLOB nei casi seguenti:

* Se il file supera il numero massimo di blocchi consentiti (attualmente 50.000). È possibile raggiungere il numero massimo consentito di blocchi senza raggiungere la dimensione massima consentita per il BLOB. Ad esempio, se la frequenza di output è elevata, ogni blocco può contenere più byte e le dimensioni del file sono superiori. Se la frequenza di output è bassa, ogni blocco contiene meno dati e le dimensioni del file sono inferiori.
* Se è presente una modifica dello schema nell'output e il formato di output richiede uno schema fisso (CSV e Avro).
* Se un processo viene riavviato, esternamente con l'arresto e l'avvio da parte di un utente oppure internamente per la manutenzione del sistema o il recupero da errori.
* Se la query è completamente partizionata, viene creato un nuovo file per ogni partizione di output.
* Se l'utente elimina un file o un contenitore dell'account di archiviazione.
* Se l'output è partizionato a lungo usando il modello di prefisso del percorso e viene usato un nuovo BLOB quando la query viene spostata all'ora successiva.
* Se l'output è partizionato in base a un campo personalizzato e viene creato un nuovo BLOB per ogni chiave di partizione, se non esiste.
* Se l'output è partizionato da un campo personalizzato in cui la cardinalità della chiave di partizione supera 8.000 e viene creato un nuovo BLOB per ogni chiave di partizione.

## <a name="event-hubs"></a>Hub eventi

Il servizio [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) è un inseritore eventi di pubblicazione-sottoscrizione altamente scalabile. Può raccogliere milioni di eventi al secondo. Un uso di un hub eventi come output si verifica quando l'output di un processo di analisi di flusso diventa l'input di un altro processo di streaming.

Sono necessari alcuni parametri per configurare i flussi di dati dagli hub eventi come output.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a questo hub eventi. |
| Spazio dei nomi dell'hub eventi | Contenitore per un set di entità di messaggistica. Quando è stato creato un nuovo hub eventi, è stato creato anche uno spazio dei nomi dell'hub eventi. |
| Nome hub eventi | Nome dell'output dell'hub eventi. |
| Nome criteri hub eventi | Criteri di accesso condiviso, che è possibile creare nella scheda **Configura** dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri hub eventi | Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi dell'hub eventi. |
| Colonna chiave di partizione | facoltativo. Colonna contenente la chiave di partizione per l'output dell'hub eventi. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica | Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta vari delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Formato | Applicabile solo per la serializzazione JSON. **Delimitato da righe** specifica che l'output viene formattato in base a ogni oggetto JSON separato da una nuova riga. **Array** specifica che l'output viene formattato come una matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON delimitati da righe, perché non richiede alcuna gestione speciale mentre è ancora in corso la scrittura del file di output. |
| Colonne delle proprietà | facoltativo. Colonne delimitate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché del payload. Altre informazioni su questa funzionalità sono illustrate nella sezione [proprietà dei metadati personalizzati per l'output](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

È possibile usare [Power bi](https://powerbi.microsoft.com/) come output per un processo di analisi di flusso per offrire un'esperienza di visualizzazione avanzata dei risultati dell'analisi. È possibile usare questa funzionalità per i Dashboard operativi, la generazione di report e la creazione di report basati sulle metriche.

Power BI output di analisi di flusso non è attualmente disponibile nelle aree Azure China 21Vianet e Azure Germania (T-Systems International).

La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per configurare l'output del Power BI.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Fornire un nome descrittivo usato nelle query per indirizzare l'output delle query a questo output Power BI. |
| Area di lavoro del gruppo |Per abilitare la condivisione dei dati con altri utenti Power BI, è possibile selezionare i gruppi nell'account Power BI o scegliere **area di lavoro personale** se non si vuole scrivere in un gruppo. L'aggiornamento di un gruppo esistente richiede il rinnovo dell'autenticazione di Power BI. |
| Nome set di dati |Specificare il nome del set di dati che si desidera venga utilizzato dall'output del Power BI. |
| Nome tabella |Immettere un nome per la tabella nel set di dati dell'output di Power BI. L'output di Power BI da processi di Analisi di flusso può avere al momento solo una tabella in un set di dati. |
| Autorizza connessione | È necessario autorizzare con Power BI per configurare le impostazioni di output. Quando si concede a questo output l'accesso al dashboard di Power BI, è possibile revocare l'accesso modificando la password dell'account utente, eliminando l'output del processo o eliminando il processo di analisi di flusso. | 

Per una procedura dettagliata sulla configurazione di un Power BI output e un dashboard, vedere l'esercitazione [analisi di flusso e Power BI di Azure](stream-analytics-power-bi-dashboard.md) .

> [!NOTE]
> Non creare in modo esplicito il set di dati e la tabella nel dashboard Power BI. Il set di dati e la tabella vengono popolati automaticamente quando il processo viene avviato e il processo inizia a generare output in Power BI. Se la query del processo non genera alcun risultato, il set di dati e la tabella non vengono creati. Se Power BI già presente un set di dati e una tabella con lo stesso nome di quello fornito in questo processo di analisi di flusso, i dati esistenti vengono sovrascritti.
>

### <a name="create-a-schema"></a>Creare uno schema
Analisi di flusso di Azure crea un set di dati Power BI e uno schema di tabella per l'utente, se non esistono già. In tutti gli altri casi, la tabella viene aggiornata con nuovi valori. Attualmente, all'interno di un set di dati può esistere una sola tabella. 

Power BI usa i criteri di conservazione FIFO (First-in, First-out). I dati vengono raccolti in una tabella fino al raggiungimento di 200.000 righe.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Convertire un tipo di dati da analisi di flusso a Power BI
Analisi di flusso di Azure consente di aggiornare il modello di dati in modo dinamico in fase di esecuzione se viene modificato lo schema di output. Vengono rilevate tutte le modifiche al nome e al tipo di colonna e l'aggiunta o la rimozione di colonne.

Questa tabella illustra le conversioni dei tipi di dati dai [tipi di dati di analisi di flusso](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) ai tipi Power bi [Entity Data Model (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), se non esistono un set di dati Power bi e una tabella.

Dall'analisi di flusso | A Power BI
-----|-----
bigint | Int64
nvarchar(max) | string
Datetime | DateTime
float | Double
Matrice di record | Tipo stringa, valore costante "IRecord" o "IArray"

### <a name="update-the-schema"></a>Aggiornare lo schema
L'analisi di flusso deduce lo schema del modello di dati in base al primo set di eventi dell'output. In seguito, se necessario, lo schema del modello di dati viene aggiornato in modo da contenere gli eventi in ingresso che potrebbero non rientrare nello schema originale.

Evitare la `SELECT *` query per impedire l'aggiornamento dinamico dello schema tra le righe. Oltre alle potenziali implicazioni in termini di prestazioni, è possibile che si verifichino incertezze sul tempo impiegato per i risultati. Selezionare i campi esatti che devono essere visualizzati nel dashboard Power BI. È anche necessario che i valori dei dati siano conformi al tipo di dati scelto.


Precedente/corrente | Int64 | string | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | string | string | Double
Double | Double | string | string | Double
string | String | String | String | string 
DateTime | string | string |  DateTime | String

## <a name="table-storage"></a>Archivio tabelle

[archiviazione tabelle di Azure](../storage/common/storage-introduction.md) offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica di un'applicazione in base alle richieste degli utenti. L'archivio tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft, che è possibile usare per i dati strutturati con meno vincoli sullo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente.

Nella tabella seguente sono elencati i nomi delle proprietà e le relative descrizioni per la creazione di un output di tabella.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione tabelle. |
| Account di archiviazione |Nome dell'account di archiviazione in cui si sta inviando l'output. |
| Chiave dell'account di archiviazione |Chiave di accesso associata all'account di archiviazione. |
| Nome tabella |Nome della tabella. La tabella viene creata se non esiste. |
| Chiave di partizione |Nome della colonna di output contenente la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione all'interno di una tabella che costituisce la prima parte della chiave primaria di un'entità. Si tratta di un valore stringa che può avere dimensioni massime di 1 KB. |
| Chiave di riga |Nome della colonna di output contenente la chiave di riga. La chiave di riga è un identificatore univoco per un'entità all'interno di una partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa che può avere dimensioni massime di 1 KB. |
| Dimensioni batch |Numero di record per un'operazione batch. Il valore predefinito (100) è sufficiente per la maggior parte dei processi. Per ulteriori informazioni sulla modifica di questa impostazione, vedere la [specifica dell'operazione batch della tabella](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) . |

## <a name="service-bus-queues"></a>Code del bus di servizio

Le [code del bus di servizio](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) consentono il recapito di messaggi FIFO a uno o più consumer concorrente. In genere, i messaggi vengono ricevuti ed elaborati dai destinatari nell'ordine temporale in cui sono stati aggiunti alla coda. Ogni messaggio viene ricevuto ed elaborato da un solo consumer di messaggi.

Nella tabella seguente sono elencati i nomi delle proprietà e le relative descrizioni per la creazione di un output della coda.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output della query a questa coda del bus di servizio. |
| Spazio dei nomi del bus di servizio |Contenitore per un set di entità di messaggistica. |
| Nome coda |Nome della coda del bus di servizio. |
| Nome criteri coda |Quando si crea una coda, è anche possibile creare criteri di accesso condiviso nella scheda **Configura** della coda. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri coda |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta vari delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Formato |Applicabile solo per il tipo JSON. **Delimitato da righe** specifica che l'output viene formattato in base a ogni oggetto JSON separato da una nuova riga. **Array** specifica che l'output viene formattato come una matrice di oggetti JSON. |
| Colonne delle proprietà | facoltativo. Colonne delimitate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché del payload. Altre informazioni su questa funzionalità sono illustrate nella sezione [proprietà dei metadati personalizzati per l'output](#custom-metadata-properties-for-output). |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="service-bus-topics"></a>Argomenti del bus di servizio
Le code del bus di servizio forniscono un metodo di comunicazione uno-a-uno dal mittente al destinatario. Gli [argomenti del bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrono una forma di comunicazione uno-a-molti.

La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per la creazione dell'output di un argomento del bus di servizio.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo argomento del bus di servizio. |
| Spazio dei nomi del bus di servizio |Contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio. |
| Nome argomento |Gli argomenti sono entità di messaggistica, simili agli Hub eventi e alle code. Sono progettati per raccogliere flussi di eventi da dispositivi e servizi. Quando viene creato un argomento, viene assegnato anche un nome specifico. I messaggi inviati a un argomento non sono disponibili a meno che non venga creata una sottoscrizione, quindi assicurarsi che esistano una o più sottoscrizioni nell'argomento. |
| Nome criteri argomento |Quando si crea un argomento del bus di servizio, è anche possibile creare criteri di accesso condiviso nella scheda **Configura** dell'argomento. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri argomento |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Se si usa il formato CSV o JSON, è necessario specificare una codifica. Al momento, UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta vari delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Colonne delle proprietà | facoltativo. Colonne delimitate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché del payload. Altre informazioni su questa funzionalità sono illustrate nella sezione [proprietà dei metadati personalizzati per l'output](#custom-metadata-properties-for-output). |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore integer univoco per ogni partizione.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) è un servizio di database distribuito a livello globale che offre scalabilità elastica illimitata in tutto il mondo, query avanzate e indicizzazione automatica su modelli di dati indipendenti dallo schema. Per informazioni sulle opzioni del contenitore Azure Cosmos DB per l'analisi di flusso, vedere l'articolo [analisi di flusso con Azure Cosmos DB come output](stream-analytics-documentdb-output.md) .

Azure Cosmos DB output di analisi di flusso non è attualmente disponibile nelle aree Azure China 21Vianet e Azure Germania (T-Systems International).

> [!Note]
> A questo punto, analisi di flusso di Azure supporta solo la connessione a Azure Cosmos DB usando l'API SQL.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente.

Nella tabella seguente sono descritte le proprietà per la creazione di un output di Azure Cosmos DB.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output | Alias per fare riferimento a questo output nella query di Analisi di flusso di Azure. |
| Sink | Azure Cosmos DB. |
| Opzione di importazione | Scegliere **selezionare Cosmos DB dalla sottoscrizione** o **specificare Cosmos DB impostazioni manualmente**.
| ID account | Nome o URI endpoint dell'account Azure Cosmos DB. |
| Chiave dell'account | Chiave di accesso condiviso per l'account Azure Cosmos DB. |
| Database | Nome del database Azure Cosmos DB. |
| Nome contenitore | Nome del contenitore da usare, che deve esistere in Cosmos DB. Esempio:  <br /><ul><li> _Contenitore_: Deve esistere un contenitore denominato "contenitore".</li>|
| ID documento |facoltativo. Nome del campo negli eventi di output usato per specificare la chiave primaria su cui si basano le operazioni di inserimento o aggiornamento.

## <a name="azure-functions"></a>Funzioni di Azure
Funzioni di Azure è un servizio di calcolo senza server che è possibile usare per eseguire il codice su richiesta senza dover effettuare esplicitamente il provisioning o la gestione dell'infrastruttura. Consente di implementare il codice attivato da eventi che si verificano in Azure o nei servizi partner. Questa funzionalità di funzioni di Azure per rispondere ai trigger rende l'output naturale per analisi di flusso di Azure. Questo adattatore di output consente agli utenti di connettere analisi di flusso a funzioni di Azure ed eseguire uno script o un frammento di codice in risposta a diversi eventi.

L'output di funzioni di Azure da analisi di flusso non è attualmente disponibile nelle aree Azure China 21Vianet e Azure Germania (T-Systems International).

Analisi di flusso di Azure richiama Funzioni di Azure tramite trigger HTTP. L'adattatore di output di funzioni di Azure è disponibile con le proprietà configurabili seguenti:

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| App per le funzioni |Nome dell'app funzioni di Azure. |
| Funzione |Nome della funzione nell'app funzioni di Azure. |
| Chiave |Se si vuole usare una funzione di Azure da un'altra sottoscrizione, è possibile fornire la chiave per accedere alla funzione. |
| Dimensioni massime batch |Proprietà che consente di impostare le dimensioni massime per ogni batch di output inviato alla funzione di Azure. L'unità di input è espressa in byte. Per impostazione predefinita, questo valore è 262.144 byte (256 KB). |
| Numero massimo di batch  |Proprietà che consente di specificare il numero massimo di eventi in ogni batch inviato a funzioni di Azure. Il valore predefinito è 100. |

Quando analisi di flusso di Azure riceve un'eccezione di 413 ("entità richiesta HTTP troppo grande") da una funzione di Azure, riduce la dimensione dei batch che invia a funzioni di Azure. Usare questa eccezione nel codice della funzione di Azure per fare in modo che Analisi di flusso di Azure non invii batch troppo grandi. Assicurarsi anche che il numero massimo di batch e i valori delle dimensioni usati nella funzione siano coerenti con i valori immessi nel portale di analisi dei flussi.

> [!NOTE]
> Durante il test della connessione, analisi di flusso Invia un batch vuoto a funzioni di Azure per verificare se la connessione tra i due funziona. Assicurarsi che l'app funzioni gestisca le richieste batch vuote per assicurarsi che il test della connessione venga superato.

Inoltre, in una situazione in cui non è presente alcun evento di destinazione in un intervallo di tempo, non viene generato alcun output. Di conseguenza, la funzione **computeResult** non viene chiamata. Questo comportamento è coerente con le funzioni di aggregazione finestra predefinite.

## <a name="custom-metadata-properties-for-output"></a>Proprietà dei metadati personalizzati per l'output 

È possibile aggiungere colonne di query come proprietà utente ai messaggi in uscita. Queste colonne non vengono inserite nel payload. Le proprietà sono presenti sotto forma di dizionario nel messaggio di output. *Key* è il nome della colonna e *value* è il valore della colonna nel dizionario delle proprietà. Sono supportati tutti i tipi di dati di analisi di flusso, ad eccezione di record e Array.  

Output supportati: 
* Coda del bus di servizio 
* Argomento del bus di servizio 
* Hub eventi 

Nell'esempio seguente vengono aggiunti i due campi `DeviceId` e `DeviceStatus` i metadati. 
* Query: `select *, DeviceId, DeviceStatus from iotHubInput`
* Configurazione di output:`DeviceId,DeviceStatus`

![Colonne delle proprietà](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

La schermata seguente mostra le proprietà dei messaggi di output ispezionate in EventHub tramite [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Proprietà personalizzate dell'evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Partizionamento

Nella tabella seguente viene riepilogato il supporto della partizione e il numero di writer di output per ogni tipo di output:

| Tipo di output | Supporto del partizionamento | Chiave di partizione  | Numero di writer di output |
| --- | --- | --- | --- |
| Archivio Azure Data Lake | Yes | Usare i token {date} e {Time} nel modello di prefisso del percorso. Scegliere il formato della data, ad esempio AAAA/MM/GG, GG/MM/AAAA o MM-gg-aaaa. HH viene usato per il formato dell'ora. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Database SQL di Azure | Sì, deve essere abilitato. | In base alla clausola PARTITION BY nella query. | Quando l'opzione eredita partizionamento è abilitata, segue il partizionamento dell'input per le [query completamente eseguibili](stream-analytics-scale-jobs.md). Per altre informazioni su come ottenere prestazioni migliori per la velocità effettiva di scrittura quando si caricano dati nel database SQL di Azure, vedere l' [output di analisi di flusso di Azure nel database SQL di Azure](stream-analytics-sql-output-perf.md). |
| Archivio BLOB di Azure | Sì | Usare i token {date} e {time} dei campi evento nel modello di percorso. Scegliere il formato della data, ad esempio AAAA/MM/GG, GG/MM/AAAA o MM-gg-aaaa. HH viene usato per il formato dell'ora. L'output del BLOB può essere partizionato in base a un singolo attributo dell'evento personalizzato {fieldname} o {datetime:\<specifier>}. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Hub eventi di Azure | Yes | Sì | Varia a seconda dell'allineamento della partizione.<br /> Quando la chiave di partizione per l'output di hub eventi è allineata con il passaggio di query upstream (precedente), il numero di writer corrisponde al numero di partizioni nell'output dell'hub eventi. Ogni writer usa la [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) per inviare eventi alla partizione specifica. <br /> Quando la chiave di partizione per l'output di hub eventi non è allineata con il passaggio di query upstream (precedente), il numero di writer corrisponde al numero di partizioni nel passaggio precedente. Ogni writer usa la [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) in **EventHubClient** per inviare eventi a tutte le partizioni di output. |
| Power BI | No | Nessuna | Non applicabile. |
| Archiviazione tabelle di Azure | Sì | Qualsiasi colonna di output.  | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Argomento del bus di servizio di Azure | Yes | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore integer univoco per ogni partizione.| Corrisponde al numero di partizioni nell'argomento di output.  |
| Coda del bus di servizio di Azure | Yes | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore integer univoco per ogni partizione.| Corrisponde al numero di partizioni nella coda di output. |
| Azure Cosmos DB | Sì | In base alla clausola PARTITION BY nella query. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Funzioni di Azure | No | Nessuna | Non applicabile. |

Il numero di writer di output può essere controllato anche `INTO <partition count>` usando la clausola (see [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) nella query, che può essere utile per ottenere una topologia di lavoro desiderata. Se l'adattatore di output non è partizionato, la mancanza di dati in una partizione di input causerà un ritardo fino alla quantità di tempo di arrivo in ritardo. In questi casi, l'output viene unito a un singolo writer, che potrebbe causare colli di bottiglia nella pipeline. Per altre informazioni sui criteri di arrivo in ritardo, vedere [considerazioni sull'ordine degli eventi di analisi di flusso di Azure](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Dimensione del batch di output
Analisi di flusso di Azure usa batch di dimensioni variabili per elaborare gli eventi e scrivere negli output. In genere, il motore di analisi di flusso non scrive un messaggio alla volta e usa i batch per migliorare l'efficienza. Quando la frequenza degli eventi in ingresso e in uscita è elevata, analisi di flusso usa batch più grandi. Quando la frequenza in uscita è bassa, usa batch di dimensioni minori per mantenere bassa la latenza.

Nella tabella seguente vengono illustrate alcune considerazioni relative all'invio in batch di output:

| Tipo di output | Dimensioni massime messaggio | Ottimizzazione delle dimensioni batch |
| :--- | :--- | :--- |
| Archivio Azure Data Lake | Vedere [Data Lake storage limiti](../azure-subscription-service-limits.md#data-lake-store-limits). | Utilizzare fino a 4 MB per operazione di scrittura. |
| Database SQL di Azure | Configurabile con numero massimo di batch. 10.000 numero massimo e 100 di righe minime per singolo inserimento bulk per impostazione predefinita.<br />Vedere [limiti di SQL Azure](../sql-database/sql-database-resource-limits.md). |  Ogni batch viene inizialmente inserito in blocco con il numero massimo di batch. Batch è diviso a metà (fino al numero minimo di batch) in base a errori che è necessario ripetere da SQL. |
| Archivio BLOB di Azure | Vedere [limiti di archiviazione di Azure](../azure-subscription-service-limits.md#storage-limits). | La dimensione massima del blocco BLOB è 4 MB.<br />Il numero massimo di Bock BLOB è 50.000. |
| Hub eventi di Azure  | 256 KB o 1 MB per messaggio. <br />Vedere [limiti di hub eventi](../event-hubs/event-hubs-quotas.md). |  Quando il partizionamento di input/output non è allineato, ogni `EventData` evento viene compresso singolarmente e inviato in un batch fino alla dimensione massima del messaggio. Questo errore si verifica anche se vengono usate le [proprietà dei metadati personalizzati](#custom-metadata-properties-for-output) . <br /><br />  Quando il partizionamento di input/output è allineato, più eventi vengono `EventData` compressi in una singola istanza, fino alla dimensione massima del messaggio e inviati. |
| Power BI | Vedere i [limiti dell'API REST di Power bi](https://msdn.microsoft.com/library/dn950053.aspx). |
| Archiviazione tabelle di Azure | Vedere [limiti di archiviazione di Azure](../azure-subscription-service-limits.md#storage-limits). | Il valore predefinito è 100 entità per singola transazione. È possibile configurarlo su un valore inferiore in base alle esigenze. |
| Coda del bus di servizio di Azure   | 256 KB per messaggio per il livello standard, 1 MB per il livello Premium.<br /> Vedere [limiti del bus di servizio](../service-bus-messaging/service-bus-quotas.md). | Usare un singolo evento per messaggio. |
| Argomento del bus di servizio di Azure | 256 KB per messaggio per il livello standard, 1 MB per il livello Premium.<br /> Vedere [limiti del bus di servizio](../service-bus-messaging/service-bus-quotas.md). | Usare un singolo evento per messaggio. |
| Azure Cosmos DB   | Vedere [Azure Cosmos DB limiti](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Le dimensioni del batch e la frequenza di scrittura vengono modificate in modo dinamico in base alle risposte Azure Cosmos DB. <br /> Non esistono limitazioni predeterminate dall'analisi di flusso. |
| Funzioni di Azure   | | Le dimensioni predefinite del batch sono pari a 262.144 byte (256 KB). <br /> Il numero di eventi predefinito per batch è 100. <br /> Le dimensioni batch sono configurabili e possono essere aumentate o ridotte nelle [opzioni di output](#azure-functions) di Analisi di flusso.

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
