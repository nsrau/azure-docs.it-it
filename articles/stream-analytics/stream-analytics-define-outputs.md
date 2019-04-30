---
title: Informazioni sugli output di Analisi di flusso di Azure
description: Questo articolo descrive opzioni di output di dati disponibili in Analisi di flusso di Azure, tra cui Power BI per i risultati dell'analisi.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/25/2019
ms.custom: seodec18
ms.openlocfilehash: 03871c3f3627e85cc2af2f05a5fba38bd8069a15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481192"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Informazioni sugli output di Analisi di flusso di Azure
Questo articolo descrive i tipi di output disponibili per un processo Azure Stream Analitica. Gli output consentono di archiviare e salvare i risultati del processo di Analisi di flusso di Azure. Usando i dati di output, è possibile eseguire ulteriormente analitica di business e il data warehousing dei dati.

Quando si progetta la query Analitica Stream, fare riferimento al nome dell'output usando il [clausola INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). È possibile usare un singolo output per ogni processo o più output per ogni processo di streaming (se necessario), fornendo più clausole INTO nella query.

Per creare, modificare e testare il processo di Stream Analitica gli output, è possibile usare la [portale di Azure](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), e [Visual Studio](stream-analytics-quick-create-vs.md).

Il supporto di alcuni tipi di output [partizionamento](#partitioning). [Le dimensioni dei batch di output](#output-batch-size) variano per ottimizzare la velocità effettiva.


## <a name="azure-data-lake-store"></a>Archivio Azure Data Lake
Analisi di flusso supporta [Archivio Data Lake di Azure](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store è un repository con iperscalabilità di livello aziendale, per carichi di lavoro analitici di big data. È possibile usare Data Lake Store per archiviare i dati di qualsiasi dimensione, tipo e velocità di inserimento per analitica esplorative e operative. Stream Analitica deve essere autorizzato ad accedere a Data Lake Store.

L'output di Azure Data Lake Store da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina (21Vianet) e Germania (T-Systems International).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autorizzare un account Azure Data Lake Store

1. Quando si seleziona Data Lake Store come output nel portale di Azure, viene chiesto di autorizzare una connessione a un'istanza di Data Lake Store esistente.

   ![Autorizzare una connessione a Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Se hai già accesso a Data Lake Store, selezionare **autorizza ora**. Una pagina viene visualizzata e indica **reindirizzamento all'autorizzazione**. Dopo aver concesso l'autorizzazione viene presentata la pagina che consente di configurare l'output di Data Lake Store.

3. Dopo aver autenticato l'account di Data Lake Store, è possibile configurare le proprietà per l'output di Data Lake Store.

   ![Definire Data Lake Storage come output di Analisi di flusso](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per configurare l'output di Data Lake Store.   

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query di Data Lake Store. |
| Nome account | Il nome dell'account Data Lake Store in cui si sta inviando l'output. Viene visualizzato un elenco di riepilogo a discesa degli account Data Lake Store disponibili nella sottoscrizione. |
| Schema prefisso percorso | Percorso del file utilizzato per scrivere i file nell'account Data Lake Store specificato. È possibile specificare una o più istanze della {data} e {time} variabili:<br /><ul><li>Esempio 1: folder1/logs/{date}/{time}</li><li>Esempio 2: folder1/logs/{date}</li></ul><br />Il timestamp della struttura di cartelle create segue ora UTC e non all'ora locale.<br /><br />Se il modello di percorso di file non contiene una barra finale (/), l'ultimo modello nel percorso del file viene considerato come un prefisso del nome file. <br /><br />Vengono creati nuovi file nei casi seguenti:<ul><li>Modifica dello schema di output</li><li>Riavvio interno o esterno di un processo</li></ul> |
| Formato data | facoltativo. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
|Formato ora | facoltativo. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Il formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro.|
| Codifica | Se si usa il formato CSV o JSON, è necessario specificare una codifica. Al momento UTF-8 è l'unico formato di codifica supportato.|
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.|
| Format | Applicabile solo per la serializzazione JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON separato da una nuova riga. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna gestione speciale durante ancora scritti nel file di output.|

### <a name="renew-data-lake-store-authorization"></a>Rinnovare l'autorizzazione per Data Lake Store
Se la password dell'account Data Lake Store è stata modificata dopo la creazione o l'ultima autenticazione del processo, è necessario autenticare nuovamente l'account. Se non ripete l'autenticazione, il processo non produce risultati di output e viene visualizzato un errore che indica la necessità di riautorizzazione nei log delle operazioni. 

Attualmente, il token di autenticazione deve essere aggiornato manualmente ogni 90 giorni per tutti i processi con l'output di Data Lake Store. È possibile ovviare a questa limitazione [l'autenticazione tramite gestita (anteprima) identità](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Per rinnovare l'autorizzazione:

1. Selezionare **arrestare** per arrestare il processo.
1. Andare alla Store di Data Lake di output e selezionare il **rinnova autorizzazione** collegamento.

   Per un breve tempo, indica una pagina a comparsa **reindirizzamento all'autorizzazione**. Se l'autorizzazione ha esito positivo, la pagina indicata **autorizzazione è stata rinnovata** e quindi lo chiude automaticamente. 
   
1. Selezionare **salvare** nella parte inferiore della pagina. È quindi possibile riavviare il processo selezionando la **ora ultimo arresto** per evitare la perdita di dati.

![Rinnovare l'autorizzazione per Data Lake Storage nell'output](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>Database SQL
È possibile usare [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) come output per i dati relazionali per natura o per le applicazioni che dipendono dal contenuto ospitato in un database relazionale. I processi di Analitica Stream scrittura in una tabella esistente nel Database SQL. Lo schema della tabella deve corrispondere esattamente i campi e i relativi tipi nell'output del processo. È inoltre possibile specificare [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) come output tramite il Database SQL di opzione di output. Per altre informazioni sui modi per migliorare la velocità effettiva di scrittura, vedere la [Analitica Stream con Database SQL di Azure come output](stream-analytics-sql-output-perf.md) articolo. 

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un Database SQL di output.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
| Database | Il nome del database in cui si sta inviando l'output. |
| Nome server | Nome del server di database SQL. |
| Username | Il nome utente che ha accesso in scrittura al database. Analitica Stream supporta solo l'autenticazione di SQL. |
| Password | Password per la connessione al database. |
| Tabella | Nome della tabella in cui viene scritto l'output. Il nome della tabella è tra maiuscole e minuscole. Lo schema della tabella deve corrispondere esattamente il numero di campi e i tipi che genera l'output del processo. |
|Eredita schema di partizione| Un'opzione per ereditare lo schema di partizionamento dell'istruzione di query precedente, per abilitare perfettamente parallela topologia con più processi di scrittura nella tabella. Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md).|
|Corrispondenza tra il numero di batch| Il limite consigliato per il numero di record inviati con ogni blocco Inserisci transazione.|

> [!NOTE]
> Attualmente l'offerta relativa al database SQL di Azure è supportata per un output di processi in Analisi di flusso. Non è supportata una macchina virtuale di Azure che esegue SQL Server con un database collegato. Questo comportamento sarà soggetto a modifiche nelle versioni future.
>

## <a name="blob-storage"></a>Archiviazione BLOB
Archiviazione Blob di Azure offre una soluzione conveniente e scalabile per archiviare grandi quantità di dati non strutturati nel cloud. Per informazioni introduttive sull'archiviazione Blob e il relativo utilizzo, vedere [come usare i BLOB](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per la creazione di un output di blob.

| Nome proprietà       | DESCRIZIONE                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias di output        | Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione BLOB. |
| Account di archiviazione     | Il nome dell'account di archiviazione in cui si sta inviando l'output.               |
| Chiave dell'account di archiviazione | Chiave privata associata all'account di archiviazione.                              |
| Contenitore di archiviazione   | Un raggruppamento logico dei blob archiviati nel servizio Blob di Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB. |
| Modello di percorso | facoltativo. Il modello di percorso file usato per scrivere i BLOB nel contenitore specificato. <br /><br /> Nel modello di percorso, è possibile scegliere di usare una o più istanze delle variabili di data e ora per specificare la frequenza di scrittura dei BLOB: <br /> {date}, {time} <br /><br />È possibile usare un partizionamento BLOB personalizzato per specificare un nome personalizzato {field} dai dati di evento ai BLOB di partizione. Il nome del campo è un valore alfanumerico e può includere spazi, trattini e caratteri di sottolineatura. Le restrizioni sui campi personalizzati includono le seguenti: <ul><li>I nomi dei campi non sono tra maiuscole e minuscole. Ad esempio, il servizio non è possibile distinguere colonna "ID" e colonna "id".</li><li>I campi annidati non sono consentiti. In alternativa, utilizzare un alias nella query di processo per il campo "appiattire".</li><li>Espressioni non possono essere utilizzate come un nome di campo.</li></ul> <br />Questa funzionalità consente di usare anche configurazioni dell'identificatore di formato data/ora personalizzate nel percorso. I formati di data e ora personalizzati devono essere specificati uno alla volta, racchiusi tra la parola chiave {datetime:\<identificatore >}. Gli input consentiti per \<identificatore > sono yyyy, MM, M, gg, d, HH, ore, mm, m, ss o s. La {datetime:\<identificatore >} parola chiave può essere usata più volte nel percorso in modo da formare delle configurazioni di data/ora personalizzato. <br /><br />Esempi: <ul><li>Esempio 1: cluster1/logs/{date}/{time}</li><li>Esempio 2: cluster1/logs/{date}</li><li>Esempio 3: cluster1/{client_id}/{date}/{time}</li><li>Esempio 4: cluster1/{datetime:ss}/{myField} in cui la query è: SELECT data.myField AS myField FROM Input;</li><li>Esempio 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Il timestamp della struttura di cartelle create segue ora UTC e non all'ora locale.<br /><br />La denominazione dei file, viene utilizzata la convenzione seguente: <br /><br />{Schema prefisso percorso}/schemaHashcode_Guid_Number.extension<br /><br />File di output di esempio:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Per altre informazioni su questa funzionalità, vedere [partizionamento output blob personalizzato di Azure Stream Analitica](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato data | facoltativo. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
| Formato ora | facoltativo. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica    | Se si usa il formato CSV o JSON, è necessario specificare una codifica. Al momento UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore   | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format      | Applicabile solo per la serializzazione JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON separato da una nuova riga. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna gestione speciale durante ancora scritti nel file di output. |

Quando si Usa archiviazione Blob come output, viene creato un nuovo file nel blob nei casi seguenti:

* Se il file supera il numero massimo di blocchi consentiti (attualmente 50.000). Si potrebbe raggiunge il numero massimo consentito di blocchi senza raggiungere le dimensioni massime consentite per il blob. Ad esempio, se la frequenza di output è elevata, ogni blocco può contenere più byte e le dimensioni del file sono superiori. Se la frequenza di output è bassa, ogni blocco contiene meno dati e le dimensioni del file sono inferiori.
* Se viene apportata una modifica dello schema nell'output e il formato di output richiede uno schema fisso (CSV e Avro).
* Se un processo viene riavviato, esternamente con l'arresto e l'avvio da parte di un utente oppure internamente per la manutenzione del sistema o il recupero da errori.
* Se la query è completamente partizionata e un nuovo file viene creato per ogni partizione di output.
* Se l'utente elimina un file o un contenitore dell'account di archiviazione.
* Se l'output è partizionato usando lo schema prefisso percorso tempo e un nuovo blob viene usato quando la query viene spostata all'ora successiva.
* Se l'output è partizionato da un campo personalizzato, e viene creato un nuovo blob ogni if chiave di partizione non esiste.
* Se l'output è partizionato da un campo personalizzato in cui la partizione della cardinalità della chiave superiore a 8.000 e per ogni chiave di partizione viene creato un nuovo blob.

## <a name="event-hubs"></a>Hub eventi
Il servizio [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) è un inseritore eventi di pubblicazione-sottoscrizione altamente scalabile. Può raccogliere milioni di eventi al secondo. Un uso di un hub eventi come output è quando l'output di un processo di Stream Analitica diventa l'input di un altro processo di streaming.

È necessario alcuni parametri per configurare i flussi di dati da hub eventi come output.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a questo hub eventi. |
| Spazio dei nomi dell'hub eventi |Un contenitore per un set di entità di messaggistica. Quando è stato creato un nuovo hub eventi, è stato creato anche uno spazio dei nomi dell'hub eventi. |
| Nome hub eventi | Il nome dell'output di hub eventi. |
| Nome criteri hub eventi | I criteri di accesso condiviso, che è possibile creare nell'hub di eventi **configura** scheda. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri hub eventi | La chiave di accesso condiviso che consente di autenticare l'accesso allo spazio dei nomi di hub eventi. |
| Colonna chiave di partizione | facoltativo. Una colonna che contiene la chiave di partizione per l'output di hub eventi. |
| Formato di serializzazione eventi | Il formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica | Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format | Applicabile solo per la serializzazione JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON separato da una nuova riga. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna gestione speciale durante ancora scritti nel file di output. |
| Colonne delle proprietà | facoltativo. Colonne delimitati da virgola che devono essere associati come proprietà utente del messaggio in uscita invece di payload. Altre informazioni su questa funzionalità sono disponibile nella sezione [delle proprietà di metadati personalizzati per l'output](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI
È possibile usare [Power BI](https://powerbi.microsoft.com/) come output per un processo di Stream Analitica per offrire un'esperienza di visualizzazione avanzata dei risultati di analisi. È possibile usare questa funzionalità per i dashboard operativi, la generazione di report e report basati su metriche.

L'output di Power BI da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina (21Vianet) e Germania (T-Systems International).

### <a name="authorize-a-power-bi-account"></a>Autorizzare un account Power BI
1. Quando è selezionato Power BI come output nel portale di Azure, viene richiesto di autorizzare un utente di Power BI esistente oppure di creare un nuovo account di Power BI.
   
   ![Autorizzare un utente di Power BI per configurare l'output](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Creare un nuovo account se non ancora presente e quindi selezionare **autorizza ora**. Viene visualizzata la pagina seguente:
   
   ![L'autenticazione a Power BI dall'account di Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Fornire l'account aziendale o dell'istituto di istruzione per autorizzare l'output di Power BI. Se sta non è già stato effettuato l'iscrizione per Power BI, selezionare **iscriversi adesso**. L'account aziendale o dell'istituto di istruzione usato per Power BI potrebbe differire dall'account di sottoscrizione di Azure che si è ora connessi con.

### <a name="configure-the-power-bi-output-properties"></a>Configurare le proprietà di output di Power BI
Dopo aver autenticato l'account di Power BI, è possibile configurare le proprietà per l'output di Power BI. La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per configurare l'output di Power BI.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Specificare un nome descrittivo che viene usato nelle query per indirizzare l'output delle query a questo output di Power BI. |
| Area di lavoro del gruppo |Per abilitare la condivisione dei dati con altri utenti di Power BI, è possibile selezionare i gruppi all'interno dell'account di Power BI o scegliere **area di lavoro personale** se non si vuole scrivere in un gruppo. L'aggiornamento di un gruppo esistente richiede il rinnovo dell'autenticazione di Power BI. |
| Nome del set di dati |Specificare un nome di set di dati che si desidera l'output di Power BI da usare. |
| Nome tabella |Immettere un nome per la tabella nel set di dati dell'output di Power BI. L'output di Power BI da processi di Analisi di flusso può avere al momento solo una tabella in un set di dati. |

Per informazioni dettagliate sulla configurazione di un output di Power BI e i dashboard, vedere la [Analitica Stream di Azure e Power BI](stream-analytics-power-bi-dashboard.md) articolo.

> [!NOTE]
> Non creare in modo esplicito il set di dati e la tabella nel dashboard di Power BI. Il set di dati e la tabella vengono popolati automaticamente quando viene avviato il processo e il processo inizia a generare output in Power BI. Se la query del processo non genera alcun risultato, il set di dati e la tabella non vengono creati. Se Power BI ha già un set di dati e un tabella con lo stesso nome di quello fornito nel processo di Stream Analitica, i dati esistenti vengono sovrascritti.
>

### <a name="create-a-schema"></a>Creare uno schema
Azure Stream Analitica crea uno schema di set di dati e la tabella di Power BI per l'utente se non sono già presenti. In tutti gli altri casi, la tabella viene aggiornata con nuovi valori. Attualmente, solo una tabella può trovarsi in un set di dati. 

Power BI Usa i criteri di conservazione First-Out (FIFO) first-in. I dati verranno raccolti in una tabella fino al raggiungimento di 200.000 righe.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Convertire un tipo di dati da Stream Analitica a Power BI
Analisi di flusso di Azure consente di aggiornare il modello di dati in modo dinamico in fase di esecuzione se viene modificato lo schema di output. Vengono rilevate tutte le modifiche al nome e al tipo di colonna e l'aggiunta o la rimozione di colonne.

Questa tabella vengono illustrate le conversioni di tipi di dati dal [tipi di dati di Analitica Stream](https://msdn.microsoft.com/library/azure/dn835065.aspx) in Power BI [tipi Entity Data Model (EDM)](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/entity-data-model), se non esiste un set di dati di Power BI e una tabella.

Dall'analisi di flusso | A Power BI
-----|-----
bigint | Int64
nvarchar(max) | string
Datetime | DateTime
float | Double
Matrice di record | Stringa di tipo, valore costante "IRecord" o "IArray"

### <a name="update-the-schema"></a>Aggiornare lo schema
L'analisi di flusso deduce lo schema del modello di dati in base al primo set di eventi dell'output. In un secondo momento, se necessario, lo schema del modello di dati viene aggiornato per contenere gli eventi in ingresso che potrebbero non rientrare nello schema originale.

Evitare il `SELECT *` query per evitare di aggiornare lo schema dinamico tra le righe. Oltre a implicazioni potenziali sulle prestazioni, potrebbe comportare incertezza correlati al tempo impiegato per i risultati. Selezionare i campi esatti che devono essere visualizzati nel dashboard di Power BI. È anche necessario che i valori dei dati siano conformi al tipo di dati scelto.


Indietro/corrente | Int64 | string | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | string | string | Double
Double | Double | string | string | Double
string | string | string | string | string 
DateTime | string | string |  DateTime | string


### <a name="renew-power-bi-authorization"></a>Rinnovare l'autorizzazione di Power BI
Se la password dell'account Power BI viene modificato dopo il processo di Stream Analitica è stato creato o l'ultima autenticazione, è necessario autenticare nuovamente Stream Analitica. Se Azure Multi-Factor Authentication è configurato nel tenant di Azure Active Directory (Azure AD), è anche necessario rinnovare l'autorizzazione di Power BI ogni due settimane. Un sintomo di questo problema è che non ci sono output del processo e un "Errore nell’autenticazione dell’utente" nei log delle operazioni:

  ![Errore di autenticazione dell'utente di Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Power BI. Fare clic sul collegamento **Rinnova autorizzazione** e riavviare il processo dall'**ora dell'ultimo arresto** per evitare la perdita di dati.

  ![Rinnovare l'autorizzazione di Power BI per l'output](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Archiviazione tabelle
[archiviazione tabelle di Azure](../storage/common/storage-introduction.md) offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica di un'applicazione in base alle richieste degli utenti. Archiviazione tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft, è possibile usare per i dati strutturati con meno vincoli allo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente.

La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per la creazione di un output di tabella.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione tabelle. |
| Account di archiviazione |Il nome dell'account di archiviazione in cui si sta inviando l'output. |
| Chiave dell'account di archiviazione |Chiave di accesso associata all'account di archiviazione. |
| Nome tabella |Nome della tabella. Se non esiste, viene creata la tabella. |
| Chiave di partizione |Il nome della colonna di output che contiene la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione all'interno di una tabella che costituisce la prima parte della chiave primaria di un'entità. È un valore stringa che può avere dimensioni fino a 1 KB. |
| Chiave di riga |Il nome della colonna di output che contiene la chiave di riga. La chiave di riga è un identificatore univoco per un'entità all'interno di una partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa che può avere dimensioni fino a 1 KB. |
| Dimensioni dei batch |Numero di record per un'operazione batch. Il valore predefinito (100) è sufficiente per la maggior parte dei processi. Vedere le [spec operazioni Batch su tabella](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) per altri dettagli su come modificare questa impostazione. |

## <a name="service-bus-queues"></a>Code del bus di servizio
[Le code del Bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrono un recapito dei messaggi FIFO a uno o più consumer concorrenti. In genere, i messaggi vengono ricevuti ed elaborati dai ricevitori nell'ordine temporale in cui sono stati aggiunti alla coda. Ogni messaggio viene ricevuto ed elaborato da un solo consumer.

La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per la creazione di un coda output.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa coda del Bus di servizio. |
| Spazio dei nomi del bus di servizio |Un contenitore per un set di entità di messaggistica. |
| Nome della coda |Il nome della coda del Bus di servizio. |
| Nome criteri coda |Quando si crea una coda, è anche possibile creare criteri di accesso condiviso della coda **configura** scheda. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri coda |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Il formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format |Applicabile solo per il tipo JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON separato da una nuova riga. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. |
| Colonne delle proprietà | facoltativo. Colonne delimitati da virgola che devono essere associati come proprietà utente del messaggio in uscita invece di payload. Altre informazioni su questa funzionalità sono disponibile nella sezione [delle proprietà di metadati personalizzati per l'output](#custom-metadata-properties-for-output). |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="service-bus-topics"></a>Argomenti del bus di servizio
Le code del Bus di servizio forniscono un metodo di comunicazione uno a uno dal mittente al destinatario. [Gli argomenti del Bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrono una forma uno-a-molti di comunicazione.

La tabella seguente elenca i nomi delle proprietà e le relative descrizioni per la creazione di un argomento output.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo argomento del Bus di servizio. |
| Spazio dei nomi del bus di servizio |Un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio. |
| Nome argomento |Gli argomenti sono entità di messaggistica, simili agli Hub eventi e alle code. Sono stati progettati per raccogliere flussi di eventi da dispositivi e servizi. Quando viene creato un argomento, ha inoltre assegnato un nome specifico. I messaggi inviati a un argomento non è disponibili a meno che non viene creata una sottoscrizione, assicurarsi quindi c'è uno o più sottoscrizioni per argomento. |
| Nome criteri argomento |Quando si crea un argomento, è anche possibile creare criteri di accesso condiviso per l'argomento **configura** scheda. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri argomento |Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. |
| Formato di serializzazione eventi |Il formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Se si usa il formato CSV o JSON, è necessario specificare una codifica. Al momento UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Colonne delle proprietà | facoltativo. Colonne delimitati da virgola che devono essere associati come proprietà utente del messaggio in uscita invece di payload. Altre informazioni su questa funzionalità sono disponibile nella sezione [delle proprietà di metadati personalizzati per l'output](#custom-metadata-properties-for-output). |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore di tipo integer univoco per ogni partizione.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) è un servizio di database distribuito a livello globale che offre scalabilità elastica illimitata in tutto il mondo, query avanzate e indicizzazione automatica su modelli di dati indipendenti dallo schema. Per altre informazioni sulle opzioni di raccolta di Azure Cosmos DB per Stream Analitica, vedere la [Analitica Stream con Azure Cosmos DB come output](stream-analytics-documentdb-output.md) articolo.

L'output di Azure Cosmos DB da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina (21Vianet) e Germania (T-Systems International).

> [!Note]
> A questo punto, Azure Stream Analitica supporta solo connessione ad Azure Cosmos DB usando l'API SQL.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente.

Nella tabella seguente sono descritte le proprietà per la creazione di un output di Azure Cosmos DB.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output | Alias per fare riferimento a questo output nella query di Analisi di flusso di Azure. |
| Sink | Azure Cosmos DB. |
| Opzione di importazione | Scegliere uno **selezionare Cosmos DB dalla sottoscrizione** oppure **Cosmos DB fornisce le impostazioni manualmente**.
| Account ID | Nome o URI endpoint dell'account Azure Cosmos DB. |
| Chiave account | Chiave di accesso condiviso per l'account Azure Cosmos DB. |
| Database | Nome del database Azure Cosmos DB. |
| Modello nome raccolta | Il nome della raccolta o il modello per le raccolte da usare. <br />È possibile costruire il formato del nome di raccolta utilizzando il token {partition} facoltativo, in cui le partizioni iniziano da 0. Due esempi:  <br /><ul><li> _MyCollection_: Deve essere presente una raccolta denominata "MyCollection".</li>  <li> _MyCollection{partition}_: In base alla colonna di partizionamento.</li></ul> Raccolte di colonne di partizionamento devono essere presente: "MyCollection0", "MyCollection1", "MyCollection2", e così via. |
| Chiave di partizione | facoltativo. È necessario solo se si usa un token {partition} nel modello del nome di raccolta.<br /> La chiave di partizione è il nome del campo negli eventi di output che viene usato per specificare la chiave di partizionamento dell'output tra raccolte.<br /> Per l'output di sola raccolta, è possibile usare qualsiasi colonna di output arbitraria. Un esempio è PartitionId. |
| Document ID |facoltativo. Il nome del campo negli eventi di output che viene usato per specificare la chiave primaria in cui insert o update si basano le operazioni.

## <a name="azure-functions"></a>Funzioni di Azure
Funzioni di Azure è un servizio di calcolo senza server che è possibile usare per eseguire codice on demand senza dover effettuare il provisioning o gestire l'infrastruttura in modo esplicito. Consente di implementare codice attivato da eventi generati nei servizi di Azure o partner. Questa possibilità di funzioni di Azure di rispondere ai trigger rende l'output naturale per Azure Stream Analitica. Questo adattatore di output consente agli utenti di connettersi Stream Analitica per funzioni di Azure ed eseguire uno script o un frammento di codice in risposta a una serie di eventi.

L'output di Funzioni di Azure da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina (21Vianet) e Germania (T-Systems International).

Analisi di flusso di Azure richiama Funzioni di Azure tramite trigger HTTP. L'adattatore di output di funzioni di Azure è disponibile con le proprietà configurabili seguenti:

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| App per le funzioni |Il nome dell'app per le funzioni di Azure. |
| Funzione |Il nome della funzione nell'app funzioni di Azure. |
| Chiave |Se si desidera utilizzare una funzione di Azure da un'altra sottoscrizione, è possibile farlo, fornendo la chiave per accedere alla funzione. |
| Dimensioni massime batch |Una proprietà che consente di imposta le dimensioni massime per ogni batch di output che viene inviato a funzioni di Azure. L'unità di input è espressa in byte. Per impostazione predefinita, questo valore è di 262.144 byte (256 KB). |
| Numero massimo di batch  |Una proprietà che consente di specificare il numero massimo di eventi in ogni batch viene inviato a funzioni di Azure. Il valore predefinito è 100. |

Quando Azure Stream Analitica riceve un 413 "http (entità richiesta troppo grande") eccezione da una funzione di Azure, riduce la dimensione dei batch che invia a funzioni di Azure. Usare questa eccezione nel codice della funzione di Azure per fare in modo che Analisi di flusso di Azure non invii batch troppo grandi. Inoltre, assicurarsi che i valori di numero e dimensioni massime dei batch usati nella funzione siano coerenti con i valori immessi nel portale di Stream Analitica.

Inoltre, in una situazione in cui è presente alcun evento in un intervallo di tempo, viene generato alcun output. Di conseguenza, il **computeResult** non viene chiamata la funzione. Questo comportamento è coerente con le funzioni di aggregazione finestra predefinite.

## <a name="custom-metadata-properties-for-output"></a>Proprietà di metadati personalizzati per l'output 

È possibile collegare le colonne di query come le proprietà dell'utente per i messaggi in uscita. Queste colonne non passano nel payload. Le proprietà sono presenti sotto forma di un dizionario nel messaggio di output. *Tasto* è il nome della colonna e *valore* è il valore della colonna nel dizionario delle proprietà. Tutti i tipi di dati di Analitica Stream sono supportati, ad eccezione di Record e la matrice.  

Output supportati: 
* Coda del bus di servizio 
* Argomento del bus di servizio 
* Hub eventi 

Nell'esempio seguente, aggiungiamo due campi `DeviceId` e `DeviceStatus` ai metadati. 
* Query: `select *, DeviceId, DeviceStatus from iotHubInput`
* Configurazione di output: `DeviceId,DeviceStatus`

![Colonne delle proprietà](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

La schermata seguente mostra l'output esaminate nell'hub eventi tramite le proprietà del messaggio [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Proprietà personalizzate di evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Partizionamento

Nella tabella seguente viene riepilogato il supporto della partizione e il numero di writer di output per ogni tipo di output:

| Tipo di output | Supporto del partizionamento | Chiave di partizione  | Numero di writer di output |
| --- | --- | --- | --- |
| Archivio Azure Data Lake | Sì | Usare {date} e i token {time} nello schema prefisso percorso. Scegliere il formato di data, ad esempio YYYY/MM/GG, MM/gg/aaaa o MM-GG-AAAA. HH viene usato per il formato dell'ora. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Database SQL di Azure | Sì | Basato sulla clausola PARTITION BY nella query. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). Per altre informazioni su come ottenere un' migliori prestazioni di scrittura della velocità effettiva quando si caricano dati nel Database SQL di Azure, vedere [output Analitica Stream di Azure al Database SQL di Azure](stream-analytics-sql-output-perf.md). |
| Archivio BLOB di Azure | Sì | Usare {date} e {time} token dai campi di evento nel modello di percorso. Scegliere il formato di data, ad esempio YYYY/MM/GG, MM/gg/aaaa o MM-GG-AAAA. HH viene usato per il formato dell'ora. L'output del BLOB può essere partizionato in base a un singolo attributo dell'evento personalizzato {fieldname} o {datetime:\<specifier>}. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Hub eventi di Azure | Sì | Sì | Varia a seconda dell'allineamento della partizione.<br /> Quando la chiave di partizione per l'output di hub eventi è allineata in modo uniforme con il passaggio della query upstream (precedente), il numero di writer è identico al numero di partizioni nell'output dell'hub eventi. Ogni writer utilizza il [EventHubSender classe](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) per inviare eventi per la partizione specifica. <br /> Quando la chiave di partizione per l'output di hub eventi non è allineata con il passaggio della query upstream (precedente), il numero di writer è identico al numero di partizioni nel passaggio precedente. Ogni writer utilizza il [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) nelle **EventHubClient** inviare eventi a tutte le partizioni di output. |
| Power BI | No  | Nessuna | Non applicabile |
| Archiviazione tabelle di Azure | Sì | Qualsiasi colonna di output.  | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Argomento del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore di tipo integer univoco per ogni partizione.| Corrisponde al numero di partizioni nell'argomento di output.  |
| Coda del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore di tipo integer univoco per ogni partizione.| Corrisponde al numero di partizioni nella coda di output. |
| Azure Cosmos DB | Sì | Usare il token {partition} nel modello di nome di raccolta. Il valore di {partition} è basato sulla clausola PARTITION BY nella query. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Funzioni di Azure | No  | Nessuna | Non applicabile |

Se l'adattatore di output non è partizionato, la mancanza di dati in una partizione di input causerà un ritardo fino alla quantità di tempo di arrivo in ritardo. In questi casi, l'output è unito a un unico scrittore, che potrebbe causare colli di bottiglia nella pipeline. Per altre informazioni sui criteri di arrivo in ritardo, vedere [considerazioni sull'ordine degli eventi di Azure Stream Analitica](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Dimensione del batch di output
Azure Stream Analitica utilizza batch di dimensioni variabili per elaborare gli eventi e scrivere in output. In genere il motore di Stream Analitica non scrive un messaggio alla volta e utilizza batch per migliorare l'efficienza. Quando la frequenza degli eventi in ingresso e in uscita è elevata, Analitica Stream utilizza batch di dimensioni maggiori. Quando la frequenza in uscita è bassa, usa batch di dimensioni minori per mantenere bassa la latenza.

Nella tabella seguente vengono illustrate alcune considerazioni per l'invio in batch di output:

| Tipo di output | Dimensioni massime messaggio | Ottimizzazione delle dimensioni batch |
| :--- | :--- | :--- |
| Archivio Azure Data Lake | Visualizzare [limiti di archiviazione di Data Lake](../azure-subscription-service-limits.md#data-lake-store-limits). | Usare fino a 4 MB per ogni operazione di scrittura. |
| Database SQL di Azure | 10.000 righe massime per ogni inserimento di massa singola.<br />100 righe minime per ogni inserimento di massa singola. <br />Visualizzare [SQL di Azure limita](../sql-database/sql-database-resource-limits.md). |  Ogni batch viene inizialmente bulk inseriti con dimensioni massime dei batch. È possibile suddividere il batch metà (fino a raggiungere le dimensioni del batch minimo) in base agli errori non irreversibili da SQL. |
| Archivio BLOB di Azure | Visualizzare [archiviazione di Azure limita](../azure-subscription-service-limits.md#storage-limits). | La dimensione del blocco massima dei blob è 4 MB.<br />Il conteggio di bock massime del blob è 50.000. |
| Hub eventi di Azure  | 256 KB per ogni messaggio. <br />Visualizzare [limiti di hub eventi](../event-hubs/event-hubs-quotas.md). |  Quando il partizionamento di input/output non è allineato, ogni evento viene compresso singolarmente nella **EventData** e inviati in un batch di fino a una dimensione massima messaggio (1 MB per lo SKU Premium). <br /><br />  Quando l'input/output di partizionamento è aligned, più eventi sono compresse in un unico **EventData** dell'istanza, fino alla dimensione massima del messaggio e inviati.  |
| Power BI | Visualizzare [limita l'API Rest di Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Archiviazione tabelle di Azure | Visualizzare [archiviazione di Azure limita](../azure-subscription-service-limits.md#storage-limits). | Il valore predefinito è 100 entità per ogni singola transazione. È possibile configurarlo su un valore inferiore in base alle esigenze. |
| Coda del bus di servizio di Azure   | 256 KB per ogni messaggio.<br /> Visualizzare [del Bus di servizio limita](../service-bus-messaging/service-bus-quotas.md). | Usare un singolo evento per ogni messaggio. |
| Argomento del bus di servizio di Azure | 256 KB per ogni messaggio.<br /> Visualizzare [del Bus di servizio limita](../service-bus-messaging/service-bus-quotas.md). | Usare un singolo evento per ogni messaggio. |
| Azure Cosmos DB   | Visualizzare [Azure Cosmos DB limita](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Dimensioni batch e sono regolate dinamicamente la frequenza di scrittura basati su Azure Cosmos DB le risposte. <br /> Non sono previsti limiti predeterminati dal Stream Analitica. |
| Funzioni di Azure   | | Le dimensioni del batch predefinito sono di 262.144 byte (256 KB). <br /> Il numero di eventi predefinito per ogni batch è 100. <br /> Le dimensioni batch sono configurabili e possono essere aumentate o ridotte nelle [opzioni di output](#azure-functions) di Analisi di flusso.

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
