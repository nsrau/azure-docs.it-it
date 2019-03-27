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
ms.openlocfilehash: f2318d3026578aef1e0e5c08d4a816b8f95a366f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448712"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Informazioni sugli output di Analisi di flusso di Azure
Questo articolo descrive i diversi tipi di output disponibili per un processo di Analisi di flusso di Azure. Gli output consentono di archiviare e salvare i risultati del processo di Analisi di flusso di Azure. Usando i dati di output, è possibile eseguire altre analisi di business e il data warehousing dei dati.

Quando si progetta la query di Analisi di flusso, fare riferimento al nome dell'output usando la [clausola INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). È possibile usare un singolo output per ogni processo o più output per ogni processo di streaming se occorre, specificando più clausole INTO nella query.

Per creare, modificare e testare il processo di Stream Analitica gli output, è possibile usare la [portale di Azure](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), e [Visual Studio](stream-analytics-quick-create-vs.md).

Alcuni tipi di output supportano il [partizionamento](#partitioning) e le [dimensioni batch dell'output](#output-batch-size) variano per ottimizzare la velocità effettiva.


## <a name="azure-data-lake-store"></a>Archivio Azure Data Lake
Analisi di flusso supporta [Archivio Data Lake di Azure](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Archivio Data Lake consente di archiviare dati di qualsiasi dimensione, tipo e velocità di inserimento per le analisi esplorative e operative. Analisi di flusso deve essere autorizzato ad accedere a Data Lake Store.

L'output di Azure Data Lake Store da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina (21Vianet) e Germania (T-Systems International).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autorizzare un account Azure Data Lake Store

1. Se nel portale di Azure è selezionato Data Lake Store come output, viene richiesto di autorizzare una connessione a un Data Lake Store esistente.

   ![Autorizzare la connessione a Data Lake Storage](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Se si ha già accesso a Data Lake Store, selezionare **Autorizza ora**. Verrà visualizzata una pagina con il messaggio **Reindirizzamento all'autorizzazione in corso**. Dopo che l'autorizzazione ha esito positivo, viene visualizzata la pagina che consente di configurare l'output di Data Lake Store.

3. Dopo aver autenticato l'account, è possibile configurare le proprietà per l'output di Archivio Data Lake. La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Archivio Data Lake.

   ![Definire Data Lake Storage come output di Analisi di flusso](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a questo Data Lake Store. |
| Nome account | Nome dell'account di archiviazione Data Lake a cui si sta inviando l'output. Viene visualizzato un elenco a discesa degli account di Data Lake Store disponibili nella sottoscrizione. |
| Schema prefisso percorso | Percorso del file usato per scrivere i file nell'account di Archivio Data Lake specificato. È possibile specificare una o più istanze delle variabili {date} e {time}.<br /><ul><li>Esempio 1: folder1/logs/{date}/{time}</li><li>Esempio 2: folder1/logs/{date}</li></ul><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />Se il modello di percorso del file non contiene un "/" finale, l'ultimo modello nel percorso del file viene considerato come prefisso del nome file. <br /><br />Vengono creati nuovi file nei casi seguenti:<ul><li>Modifica dello schema di output</li><li>Riavvio interno o esterno di un processo.</li></ul> |
| Formato data | facoltativo. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
|Formato ora | facoltativo. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro.|
| Codifica | Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato.|
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.|
| Format | Applicabile solo per la serializzazione JSON. Separato da righe specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna gestione speciale durante la scrittura del file di output.|

### <a name="renew-data-lake-store-authorization"></a>Rinnovare l'autorizzazione per Data Lake Store
Se la password dell'account Data Lake Store è stata modificata dopo la creazione o l'ultima autenticazione del processo, è necessario autenticare nuovamente l'account. Se non si ripete l'autenticazione, il processo non restituisce risultati di output e nei log delle operazioni viene visualizzato un errore che indica che è necessario ripetere l'autorizzazione. Attualmente esiste una limitazione secondo cui il token di autenticazione deve essere aggiornato manualmente ogni 90 giorni per tutti i processi con output di Archivio Data Lake. Tuttavia, è possibile ovviare a questa limitazione [esegue l'autenticazione con gestita (anteprima) identità](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Per rinnovare l'autorizzazione, **arrestare** il processo, passare all'output di Data Lake Store, quindi fare clic sul collegamento **Rinnova autorizzazione**. Verrà brevemente visualizzata una pagina che indica **Reindirizzamento all'autorizzazione in corso**. La pagina verrà chiusa automaticamente e, in caso di esito positivo, verrà visualizzato il messaggio **L'autorizzazione è stata rinnovata**. È quindi necessario fare clic su **Salva** nella parte inferiore della pagina e, per continuare, riavviare il processo dall'**ora dell'ultimo arresto** per evitare la perdita di dati.

![Rinnovare l'autorizzazione per Data Lake Storage nell'output](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>Database SQL
[database SQL di Azure](https://azure.microsoft.com/services/sql-database/) può essere usato come output per i dati di natura relazionale o per applicazioni che dipendono dal contesto ospitato in un database relazionale. I processi di Analisi di flusso di Azure eseguono la scrittura in una tabella esistente di un database SQL di Azure. Lo schema della tabella deve corrispondere esattamente ai campi e ai relativi tipi generati dal processo. Un [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) può anche essere specificato come output tramite l'opzione di output del database SQL. Per informazioni sui modi che consentono di migliorare la velocità effettiva di scrittura, vedere l'articolo [Analisi di flusso con database SQL di Azure come output](stream-analytics-sql-output-perf.md). La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un database SQL di output.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
| Database | Nome del database a cui si sta inviando l'output. |
| Nome server | Nome server del database SQL di Azure. |
| Username | Nome utente che ha accesso in scrittura al database. Analisi di flusso supporta solo l'autenticazione SQL. |
| Password | Password per la connessione al database. |
| Tabella | Nome della tabella in cui viene scritto l'output. Il nome della tabella fa distinzione tra maiuscole e minuscole e lo schema della tabella deve corrispondere esattamente al numero di campi e ai relativi tipi generati dall'output del processo. |
|Eredita schema di partizione| Questa proprietà consente di ereditare lo schema di partizionamento del passaggio di query precedente per abilitare la topologia perfettamente parallela con più writer nella tabella. Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md).|
|Corrispondenza tra il numero di batch| Il limite massimo consigliato di record inviati con ogni transazione di inserimento bulk.|

> [!NOTE]
> Attualmente l'offerta relativa al database SQL di Azure è supportata per un output di processi in Analisi di flusso. Non è tuttavia supportata una macchina virtuale di Azure che esegue SQL Server con un database collegato. Questo comportamento sarà soggetto a modifiche nelle versioni future.
>

## <a name="blob-storage"></a>Archiviazione BLOB
L'archiviazione BLOB offre una soluzione conveniente e scalabile per archiviare grandi quantità di dati non strutturati nel cloud. Per un'introduzione all'archivio BLOB di Azure e al relativo utilizzo, vedere la documentazione in [Come usare i BLOB](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output del BLOB.

| Nome proprietà       | DESCRIZIONE                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias di output        | Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione BLOB. |
| Account di archiviazione     | Nome dell'account di archiviazione a cui si sta inviando l'output.               |
| Chiave dell'account di archiviazione | Chiave privata associata all'account di archiviazione.                              |
| Contenitore di archiviazione   | I contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB. |
| Modello di percorso | facoltativo. Il modello di percorso del file usato per scrivere i BLOB nel contenitore specificato. <br /><br /> Nel modello del percorso è possibile scegliere di usare una o più istanze delle variabili di data e ora per specificare la frequenza di scrittura dei BLOB: <br /> {date}, {time} <br /><br />È possibile usare un partizionamento BLOB personalizzato per specificare un nome personalizzato {field} dai dati di evento ai BLOB di partizione. Il nome del campo è un valore alfanumerico e può includere spazi, trattini e caratteri di sottolineatura. Le restrizioni sui campi personalizzati includono le seguenti: <ul><li>Nessuna distinzione tra maiuscole e minuscole (impossibilità di distinguere tra le colonne "ID" e "id")</li><li>I campi annidati non sono consentiti. Usare in alternativa un alias della query di processo per rendere flat il campo.</li><li>Le espressioni non possono essere usate come nome di campo.</li></ul> <br /><br /> Questa funzionalità consente di usare anche configurazioni dell'identificatore di formato data/ora personalizzate nel percorso. I formati di data e ora personalizzati devono essere specificati uno alla volta, racchiusi tra la parola chiave {datetime:\<identificatore >}. Gli input consentiti \<identificatore> sono aaaa, MM, M, gg, g, HH, H, mm, m, ss o s. La parola chiave {datetime:\<identificatore>} può essere usata più volte nel percorso in modo da formare delle configurazioni data/ora personalizzate. <br /><br />Esempi: <ul><li>Esempio 1: cluster1/logs/{date}/{time}</li><li>Esempio 2: cluster1/logs/{date}</li><li>Esempio 3: cluster1/{client_id}/{date}/{time}</li><li>Esempio 4: cluster1/{datetime:ss}/{myField} in cui la query è: SELECT data.myField AS myField FROM Input;</li><li>Esempio 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br /><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />La denominazione dei file segue questa convenzione: <br /><br />{Schema prefisso percorso}/schemaHashcode_Guid_Number.extension<br /><br />File di output di esempio:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br /><br /> Per altre informazioni su questa funzionalità, visitare [Modelli di percorso di data/ora personalizzati per l'output di archiviazione BLOB di Analisi di flusso di Azure (anteprima)](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato data | facoltativo. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
| Formato ora | facoltativo. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica    | Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore   | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format      | Applicabile solo per la serializzazione JSON. Separato da righe specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna gestione speciale durante la scrittura del file di output. |

Quando si usa come output l'archiviazione BLOB, viene creato un nuovo file nel BLOB nei casi seguenti:

* Se il file supera il numero massimo di blocchi consentiti (attualmente 50.000). Il numero massimo di blocchi consentiti può essere raggiunto senza raggiungere le dimensioni massime consentite per il BLOB. Ad esempio, se la frequenza di output è elevata, ogni blocco può contenere più byte e le dimensioni del file sono superiori. Se la frequenza di output è bassa, ogni blocco contiene meno dati e le dimensioni del file sono inferiori.
* Se l'output contiene una modifica dello schema e il formato di output richiede uno schema fisso (CSV e Avro).
* Se un processo viene riavviato, esternamente con l'arresto e l'avvio da parte di un utente oppure internamente per la manutenzione del sistema o il recupero da errori.
* Se la query è completamente partizionata, per ogni partizione di output viene creato un nuovo file.
* Se un file o un contenitore dell'account di archiviazione viene eliminato dall'utente.
* Se l'output è partizionato in base al tempo usando lo schema prefisso percorso, quando la query passa all'ora successiva viene usato un nuovo BLOB.
* Se l'output è partizionato da un campo personalizzato, viene creato un nuovo BLOB per ogni chiave di partizione, se inesistente.
* Se l'output è partizionato da un campo personalizzato in cui la cardinalità della chiave di partizione supera 8000, può essere creato un nuovo BLOB per ogni chiave di partizione.

## <a name="event-hub"></a>Hub eventi
Il servizio [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) è un inseritore eventi di pubblicazione-sottoscrizione altamente scalabile. Può raccogliere milioni di eventi al secondo. Un uso di un hub eventi come output si verifica quando l'output di un processo di Analisi di flusso di Azure diventa l'input di un altro processo di streaming.

Per configurare i flussi dei dati dell'hub eventi, sono necessari alcuni parametri come output.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a questo hub eventi. |
| Spazio dei nomi dell'hub eventi |Uno spazio dei nomi dell'hub eventi è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi dell'hub eventi. |
| Nome dell'hub eventi | Nome dell'output dell'hub eventi. |
| Nome criteri hub eventi | Criteri di accesso condiviso che possono essere creati nella scheda Configura dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri hub eventi | Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi dell'hub eventi. |
| Colonna chiave di partizione [facoltativo] | Questa colonna contiene la chiave di partizione per l'output dell'Hub eventi. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica | Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format | Applicabile solo per la serializzazione JSON. Separato da righe specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna gestione speciale durante la scrittura del file di output. |
| Colonne delle proprietà [facoltativi] | Le colonne che devono essere associati come proprietà utente del messaggio in uscita invece di payload delimitati da virgole. Altre informazioni su questa funzionalità nella sezione "Proprietà personalizzate dei metadati per l'output" |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) può essere usato come output per un processo di Analisi di flusso per offrire un'esperienza di visualizzazione avanzata dei risultati di analisi. Questa funzionalità può essere usata per i dashboard operativi, la generazione di report e la creazione di report basati sulle metriche.

L'output di Power BI da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina (21Vianet) e Germania (T-Systems International).

### <a name="authorize-a-power-bi-account"></a>Autorizzare un account Power BI
1. Se nel portale di Azure è selezionato Power BI come output, viene richiesto di autorizzare un utente Power BI esistente oppure di creare un nuovo account Power BI.
   
   ![Autorizzare l'utente di Power BI alla configurazione dell'output](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Creare un nuovo account se non è ancora presente, quindi scegliere Autorizza ora. Viene visualizzata la pagina seguente:
   
   ![Eseguire l'autenticazione a Power BI dall'account di Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. In questo passaggio immettere l'account aziendale o dell'istituto di istruzione per autorizzare l'output di Power BI. Se non si è già iscritti a Power BI, scegliere Iscriviti ora. L'account aziendale o dell'istituto di istruzione usato per Power BI potrebbe differire dall'account della sottoscrizione di Azure con cui si è attualmente connessi.

### <a name="configure-the-power-bi-output-properties"></a>Configurare le proprietà di output di Power BI
Dopo aver autenticato l'account Power BI, è possibile configurare le proprietà per l'output di Power BI. La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Power BI.

| Nome proprietà | description |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo output di Power BI. |
| Area di lavoro del gruppo |Per abilitare la condivisione dei dati con altri utenti Power BI, è possibile selezionare i gruppi all'interno dell'account Power BI o scegliere "Area di lavoro personale" se non si vuole eseguire la scrittura in un gruppo. L'aggiornamento di un gruppo esistente richiede il rinnovo dell'autenticazione di Power BI. |
| Nome del set di dati |Immettere un nome per il set di dati che dovrà essere usato dall'output di Power BI |
| Nome tabella |Immettere un nome per la tabella nel set di dati dell'output di Power BI. Attualmente, l’output di Power BI da processi di Analisi di flusso può avere solo una tabella in un set di dati |

Per una procedura dettagliata della configurazione di un output di Power BI e del dashboard, vedere l'articolo [Analisi di flusso di Azure e Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Non creare in modo esplicito il set di dati e la tabella nel dashboard di Power BI. Il set di dati e la tabella vengono compilati automaticamente quando il processo viene avviato e il processo inizia a generare output in Power BI. Si noti che se la query di processo non genera alcun risultato, il set di dati e la tabella non vengono creati. Si noti che se Power BI ha già un set di dati e una tabella con lo stesso nome fornito dall'utente nel processo di analisi di flusso, i dati esistenti vengono sovrascritti.
>

### <a name="schema-creation"></a>Creazione dello schema
Analisi di flusso di Azure crea un set di dati e una tabella di Power BI per conto dell'utente, se non esiste già. In tutti gli altri casi, la tabella viene aggiornata con nuovi valori. Attualmente è presente una limitazione che prevede l'esistenza di una sola tabella in un set di dati. Power BI usa i criteri di conservazione FIFO. Se abilitati, i dati verranno raccolti in una tabella fino al raggiungimento di 200.000 righe.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Conversione di tipi di dati da Analisi di flusso di Azure a Power BI
Analisi di flusso di Azure consente di aggiornare il modello di dati in modo dinamico in fase di esecuzione se viene modificato lo schema di output. Vengono rilevate tutte le modifiche al nome e al tipo di colonna e l'aggiunta o la rimozione di colonne.

Questa tabella contiene le conversioni dei tipi di dati dai [tipi di dati di analisi di flusso](https://msdn.microsoft.com/library/azure/dn835065.aspx) ai [tipi Entity Data Model (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) di Power BI se non esistono né un set di dati né una tabella di Power BI.

Dall'analisi di flusso | A Power BI
-----|-----
bigint | Int64
nvarchar(max) | string
Datetime | DateTime
float | Double
Matrice di record | Tipo String, valore di tipo Constant "IRecord" o "IArray"

### <a name="schema-update"></a>Aggiornamento dello schema
L'analisi di flusso deduce lo schema del modello di dati in base al primo set di eventi dell'output. In un secondo momento, se necessario, lo schema del modello di dati viene aggiornato per gestire gli eventi in ingresso che potrebbero non rientrare nello schema originale.

È consigliabile non usare la query `SELECT *` per evitare l'aggiornamento dinamico dello schema nelle righe. Oltre a implicazioni potenziali sulle prestazioni, potrebbe anche verificarsi un problema di incertezza rispetto al tempo necessario per ottenere i risultati. È necessario selezionare i campi esatti che devono essere visualizzati nel dashboard di Power BI. È anche necessario che i valori dei dati siano conformi al tipo di dati scelto.


Precedente/Corrente | Int64 | string | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | string | string | Double
Double | Double | string | string | Double
string | string | string | string | string 
DateTime | string | string |  DateTime | string


### <a name="renew-power-bi-authorization"></a>Rinnovare l'autorizzazione di Power BI
Se la password dell'account Power BI viene modificata dopo la creazione o l'ultima autenticazione del processo di Analisi di flusso, è necessario ripetere l'autenticazione di Analisi di flusso. Se nel tenant di Azure Active Directory (AAD) è configurata la Multi-Factor Authentication (MFA), sarà necessario rinnovare anche l'autorizzazione di Power BI ogni due settimane. Un sintomo di questo problema è che non ci sono output del processo e un "Errore nell’autenticazione dell’utente" nei log delle operazioni:

  ![Errore di autenticazione dell'utente di Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Power BI. Fare clic sul collegamento **Rinnova autorizzazione** e riavviare il processo dall'**ora dell'ultimo arresto** per evitare la perdita di dati.

  ![Rinnovare l'autorizzazione di Power BI per l'output](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Archiviazione tabelle
[archiviazione tabelle di Azure](../storage/common/storage-introduction.md) offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica di un'applicazione in base alle richieste degli utenti. Archiviazione tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft che è possibile sfruttare per i dati strutturati con meno vincoli allo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di tabelle.

| Nome proprietà | description |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione tabelle. |
| Account di archiviazione |Nome dell'account di archiviazione a cui si sta inviando l'output. |
| Chiave dell'account di archiviazione |Chiave di accesso associata all'account di archiviazione. |
| Nome tabella |Nome della tabella. Se non esiste, viene creata una nuova tabella. |
| Chiave di partizione |Nome della colonna di output contenente la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione di una determinata tabella che costituisce la prima parte della chiave primaria di un'entità. Si tratta di un valore stringa le cui dimensioni massime sono di 1 KB. |
| Chiave di riga |Nome della colonna di output contenente la chiave di riga. La chiave di riga è un identificatore univoco per un’entità all'interno di una determinata partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa le cui dimensioni massime sono di 1 KB. |
| Dimensioni dei batch |Numero di record per un'operazione batch. Il valore predefinito (100) è sufficiente per la maggior parte dei processi. Vedere la [specifica relativa alle operazioni batch su tabella](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) per altre informazioni sulla modifica di questa impostazione. |

## <a name="service-bus-queues"></a>Code del bus di servizio
[Code del bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx) consentono un recapito dei messaggi di tipo FIFO (First In, First Out) a uno o più consumer concorrenti. Si prevede in genere che i messaggi vengano ricevuti ed elaborati nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di coda.

| Nome proprietà | description |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa coda del bus di servizio. |
| Spazio dei nomi del bus di servizio |Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. |
| Nome della coda |Nome della coda del bus di servizio. |
| Nome criteri coda |Durante la creazione di una coda, nella scheda Configura coda è anche possibile creare criteri di accesso condiviso. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri coda |Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format |Applicabile solo per il tipo JSON. Separato da righe specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output viene formattato come matrice di oggetti JSON. |
| Colonne delle proprietà [facoltativi] | Le colonne che devono essere associati come proprietà utente del messaggio in uscita invece di payload delimitati da virgole. Altre informazioni su questa funzionalità nella sezione "Proprietà personalizzate dei metadati per l'output" |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="service-bus-topics"></a>Argomenti del bus di servizio
Le code del bus di servizio forniscono un metodo di comunicazione uno-a-uno dal mittente al destinatario, invece gli [argomenti del bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrono una forma di comunicazione uno-a-molti.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di tabelle.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo argomento del bus di servizio. |
| Spazio dei nomi del bus di servizio |Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio |
| Nome argomento |Gli argomenti sono entità di messaggistica, simili agli Hub eventi e alle code. Sono progettati per raccogliere i flussi di eventi da diversi dispositivi e servizi. Quando un argomento viene creato, gli viene assegnato un nome specifico. Dal momento che i messaggi inviati a un argomento non sono disponibili se non viene creata una sottoscrizione, assicurarsi che esistano una o più sottoscrizioni per l'argomento |
| Nome criteri argomento |Durante la creazione di un argomento, nella scheda Configura argomento è anche possibile creare criteri di accesso condiviso. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso |
| Chiave criteri argomento |Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Colonne delle proprietà [facoltativi] | [Facoltativo] Le colonne che devono essere associati come proprietà utente del messaggio in uscita invece di payload delimitati da virgole. Altre informazioni su questa funzionalità nella sezione "Proprietà personalizzate dei metadati per l'output" |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) è un servizio di database multimodello distribuito a livello globale che offre scalabilità elastica illimitata in tutto il mondo, query avanzate e indicizzazione automatica su modelli di dati indipendenti dallo schema, è garantito a bassa latenza ed è leader del settore per i contratti di servizio completi. Per informazioni sulle opzioni di raccolta di Cosmos DB per Analisi di flusso, consultare l'articolo [Analisi di flusso con Cosmos DB come output](stream-analytics-documentdb-output.md).

L'output di Azure Cosmos DB da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina (21Vianet) e Germania (T-Systems International).

> [!Note]
> Ad oggi, Analisi di flusso di Azure supporta la connessione a CosmosDB solo tramite l'**API SQL**.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente.

Nella tabella seguente sono descritte le proprietà per la creazione di un output di Azure Cosmos DB.

| Nome proprietà | description |
| --- | --- |
| Alias di output | Alias per fare riferimento a questo output nella query di Analisi di flusso di Azure. |
| Sink | Cosmos DB |
| Opzione di importazione | Scegliere "Selezionare Cosmos DB dalle sottoscrizioni correnti" o "Specificare le impostazioni di Cosmos DB manualmente".
| ID account | Nome o URI endpoint dell'account Cosmos DB. |
| Chiave account | Chiave di accesso condiviso per l'account Cosmos DB. |
| Database | Nome del database Cosmos DB. |
| Modello nome raccolta | Nome di raccolta o relativo modello per le raccolte da usare. <br />Il formato del nome di raccolta può essere costruito utilizzando il token {partizione} facoltativo, dove le partizioni iniziano da 0. Due esempi:  <br />1. _MyCollection_: deve essere presente una raccolta denominata "MyCollection".  <br />2. _MyCollection{partition}_: basata sulla colonna di partizionamento. <br />Devono essere presenti le raccolte della colonna di partizionamento "MyCollection0", "MyCollection1", "MyCollection2" e così via. |
| Chiave di partizione | facoltativo. È necessario solo se si usa un token {partition} nel modello del nome di raccolta.<br /> La chiave di partizione è il nome del campo negli eventi di output usato per specificare la chiave per il partizionamento dell'output tra le raccolte.<br /> Per l'output di una singola raccolta si può usare qualsiasi colonna di output, ad esempio PartitionId. |
| Document ID |facoltativo. Il nome del campo negli eventi di output usato per specificare la chiave primaria su cui si basano le operazioni di inserimento o aggiornamento.

## <a name="azure-functions"></a>Funzioni di Azure
Funzioni di Azure è un servizio di calcolo senza server che consente di eseguire codice su richiesta senza dover gestire l'infrastruttura o effettuare il provisioning in modo esplicito. Consente di implementare il codice attivato da eventi generati nei servizi di Azure o in servizi di terze parti. La possibilità offerta da Funzioni di Azure di rispondere ai trigger la rende l'output naturale per Analisi di flusso di Azure. Questo adattatore di output consente agli utenti di collegare Analisi di flusso a Funzioni di Azure ed eseguire uno script o una porzione di codice in risposta a diversi eventi.

L'output di Funzioni di Azure da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina (21Vianet) e Germania (T-Systems International).

Analisi di flusso di Azure richiama Funzioni di Azure tramite trigger HTTP. Il nuovo adattatore di input di Funzioni di Azure è disponibile con le seguenti proprietà configurabili:

| Nome proprietà | description |
| --- | --- |
| App per le funzioni |Nome dell'app Funzioni di Azure |
| Funzione |Nome della funzione nell'app Funzioni di Azure |
| Chiave |Per usare una funzione di Azure di un'altra sottoscrizione, è necessario fornire la chiave per accedere alla funzione |
| Dimensioni massime batch |Questa proprietà può essere usata per impostare le dimensioni massime per ogni batch di output inviato a Funzioni di Azure. L'unità di input è espressa in byte. Per impostazione predefinita, questo valore è 262.144 byte (256 kB) |
| Numero massimo di batch  |Come indica il nome, questa proprietà consente di specificare il numero massimo di eventi in ogni batch che vengono inviati a Funzioni di Azure. Il valore di conteggio massimo di batch predefinito è 100 |

Quando Analisi di flusso di Azure riceve l'eccezione 413 (Entità richiesta HTTP troppo grande) dalla funzione di Azure, riduce la dimensione dei batch che invia a Funzioni di Azure. Usare questa eccezione nel codice della funzione di Azure per fare in modo che Analisi di flusso di Azure non invii batch troppo grandi. Assicurarsi anche che i valori relativi alle dimensioni massime e al numero massimo di batch usati nella funzione siano conformi ai valori inseriti nel portale di Analisi di flusso di Azure.

Quando non avviene alcun evento in un intervallo di tempo, non viene generato alcun output. Di conseguenza, non viene chiamata la funzione computeResult. Questo comportamento è coerente con le funzioni di aggregazione finestra predefinite.

## <a name="custom-metadata-properties-for-output"></a>Proprietà di metadati personalizzati per l'output 

Questa funzionalità consente di associare le colonne di query come le proprietà dell'utente ai messaggi in uscita. Queste colonne non entrano nel payload. Queste proprietà sono presenti sotto forma di un dizionario nel messaggio di output. Chiave è il nome della colonna e valore è il valore della colonna nel dizionario delle proprietà. Tutti i tipi di dati di Analitica Stream sono supportati, ad eccezione di Record e la matrice.  

Output supportati: 
* Code del bus di servizio 
* Argomenti del bus di servizio 
* Hub eventi 

Esempio: Nell'esempio seguente, si aggiungerà i 2 campi DeviceId e DeviceStatus ai metadati. 
* Query: `select *, DeviceId, DeviceStatus from iotHubInput` .
* Configurazione di output: `DeviceId,DeviceStatus`.

![Colonne delle proprietà](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Le proprietà del messaggio esaminate nell'uso di hub eventi di output [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   ![Proprietà personalizzate di evento](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Partizionamento

Nella tabella seguente viene riepilogato il supporto della partizione e il numero di writer di output per ogni tipo di output:

| Tipo di output | Supporto del partizionamento | Chiave di partizione  | Numero di writer di output |
| --- | --- | --- | --- |
| Archivio Azure Data Lake | Sì | Use i token {date} e {time} nello schema prefisso percorso. Scegliere il formato della data, ad esempio YYYY/MM/DD, DD/MM/YYYY, MM-DD-YYYY. HH viene usato per il formato dell'ora. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Database SQL di Azure | Sì | Basato sulla clausola PARTITION BY nella query | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). Per altre informazioni su come ottenere migliori prestazioni per la velocità effettiva di scrittura quando si caricano dati nel Database SQL di Azure, vedere [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md). |
| Archivio BLOB di Azure | Sì | Usare i token {date} e {time} dai campi dell'evento nel modello di percorso. Scegliere il formato della data, ad esempio YYYY/MM/DD, DD/MM/YYYY, MM-DD-YYYY. HH viene usato per il formato dell'ora. L'output del BLOB può essere partizionato in base a un singolo attributo dell'evento personalizzato {fieldname} o {datetime:\<specifier>}. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Hub eventi di Azure | Sì | Sì | Varia a seconda dell'allineamento della partizione.<br /> Quando la chiave di partizione di output di Hub eventi è ugualmente allineata al passo di query upstream (precedente), il numero di writer è uguale al numero di partizioni di Hub eventi. Ogni writer usa la [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) di EventHub per inviare eventi alla partizione specifica. <br /> Quando la chiave di partizione di output di Hub eventi non è allineata al passo di query upstream (precedente), il numero di writer è uguale al numero di partizioni in tale passo precedente. Ogni writer usa la [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) di EventHubClient per inviare eventi a tutte le partizioni di output. |
| Power BI | No  | Nessuna | Non applicabile |
| Archiviazione tabelle di Azure | Sì | Qualsiasi colonna di output.  | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Argomento del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.| Corrisponde al numero di partizioni nell'argomento di output.  |
| Coda del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.| Corrisponde al numero di partizioni nella coda di output. |
| Azure Cosmos DB | Sì | Usare il token {partition} nel modello del nome della raccolta. Il valore di {partition} è basato sulla clausola PARTITION BY nella query. | Segue il partizionamento dell'input per le [query completamente eseguibili in parallelo](stream-analytics-scale-jobs.md). |
| Funzioni di Azure | No  | Nessuna | Non applicabile |

Se l'adattatore di output non è partizionato, la mancanza di dati in una partizione di input causerà un ritardo fino alla quantità di tempo di arrivo in ritardo.  In questi casi, l'output viene unito a un unico scrittore che può causare colli di bottiglia nella pipeline. Per altre informazioni sui criteri di arrivo in ritardo, visitare [Considerazioni sull'ordine degli eventi con Analisi di flusso di Azure](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Dimensione del batch di output
Analisi di flusso di Azure usa batch di dimensioni variabili per l'elaborazione degli eventi e la scrittura negli output. Il motore di Analisi di flusso non scrive generalmente un messaggio alla volta e usa i batch per migliorare l'efficienza. Quando la frequenza di eventi in ingresso e in uscita è elevata, usa batch di dimensioni maggiori. Quando la frequenza in uscita è bassa, usa batch di dimensioni minori per mantenere bassa la latenza.

La tabella seguente spiega alcune considerazioni per l'invio in batch dell'output:

| Tipo di output | Dimensioni massime messaggio | Ottimizzazione delle dimensioni batch |
| :--- | :--- | :--- |
| Archivio Azure Data Lake | Vedere [Limiti di Data Lake Store](../azure-subscription-service-limits.md#data-lake-store-limits) | Fino a 4 MB per ogni operazione di scrittura |
| Database SQL di Azure | Numero massimo di 10.000 righe per ogni singolo inserimento bulk<br />Numero minimo di 100 righe per ogni singolo inserimento bulk <br />Vedere anche [Limiti di Azure SQL](../sql-database/sql-database-resource-limits.md) |  Per ogni batch viene inizialmente eseguito l'inserimento bulk con le dimensioni batch massime. Il batch può essere diviso a metà (fino alla dimensione batch minima) in base a errori SQL non irreversibili. |
| Archivio BLOB di Azure | Vedere [Limiti di Archiviazione di Azure](../azure-subscription-service-limits.md#storage-limits) | Le dimensioni massime dei blocchi di BLOB sono pari a 4 MB<br />Il numero massimo di blocchi di BLOB è 50000 |
| Hub eventi di Azure   | 256 kB per ogni messaggio <br />Vedere anche [Limiti relativi all'hub eventi](../event-hubs/event-hubs-quotas.md) |   Quando il partizionamento di input/output non è allineato, ogni evento viene inserito individualmente in un elemento EventData e inviato in un batch che può raggiungere le dimensioni massime del messaggio (1 MB per SKU Premium). <br /><br />  Quando il partizionamento di input/output è allineato, più eventi vengono inseriti in un singolo elemento EventData fino alle dimensioni massime del messaggio e inviati.  |
| Power BI | Vedere [Limitazioni dell'API REST di Power BI](https://msdn.microsoft.com/library/dn950053.aspx) |
| Archiviazione tabelle di Azure | Vedere [Limiti di Archiviazione di Azure](../azure-subscription-service-limits.md#storage-limits) | Il valore predefinito è 100 entità per ogni singola transazione e può essere configurato su un valore inferiore in base alle esigenze. |
| Coda del bus di servizio di Azure   | 256 kB per ogni messaggio<br /> Vedere anche [Limiti del bus di servizio](../service-bus-messaging/service-bus-quotas.md) | Singolo evento per ogni messaggio |
| Argomento del bus di servizio di Azure | 256 kB per ogni messaggio<br /> Vedere anche [Limiti del bus di servizio](../service-bus-messaging/service-bus-quotas.md) | Singolo evento per ogni messaggio |
| Azure Cosmos DB   | Vedere [Limiti relativi ad Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | Le dimensioni batch e la frequenza di scrittura vengono modificate in modo dinamico in base alle risposte di CosmosDB. <br /> Nessuna limitazione predeterminata di Analisi di flusso. |
| Funzioni di Azure   | | La dimensione del batch predefinita è 262.144 byte (256 kB). <br /> Il numero di eventi predefinito per ogni batch è 100. <br /> Le dimensioni batch sono configurabili e possono essere aumentate o ridotte nelle [opzioni di output](#azure-functions) di Analisi di flusso.

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
