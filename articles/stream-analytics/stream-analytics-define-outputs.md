---
title: 'Output di Analisi di flusso: opzioni per archiviazione, analisi | Documentazione Microsoft'
description: Informazioni sulla destinazione di opzioni di output dei dati di Analisi di flusso tra cui Power BI per i risultati dell&quot;analisi.
keywords: trasformazione dei dati, risultati dell&quot;analisi, opzioni di archiviazione dati
services: stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: ba6697ac-e90f-4be3-bafd-5cfcf4bd8f1f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/05/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3a42093a67fe1ded29e97343affa5df89ea5fd1a


---
# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Output di Analisi di flusso: opzioni per archiviazione, analisi
Quando si crea un processo di Analisi di flusso, una considerazione da fare riguarda l'uso dei dati risultanti. Come si visualizzeranno i risultati del processo di Analisi di flusso e dove saranno archiviati?

Per poter abilitare un'ampia gamma di modelli dell'applicazione, Analisi di flusso di Azure offre metodi diversi per archiviare l'output e visualizzare i risultati dell'analisi. In questo modo è possibile sia visualizzare facilmente l'output del processo che ottenere flessibilità nell'uso e nell'archiviazione dell'output del processo per il data warehouse e altri scopi. Qualsiasi elemento output configurato nel processo deve esistere prima che il processo venga avviato e gli eventi avviino il flusso. Ad esempio, se si utilizza l'archiviazione Blob come output, il processo non creerà un account di archiviazione automaticamente. Deve essere creato dall'utente prima che venga avviato il processo ASA.

## <a name="azure-data-lake-store"></a>Archivio Azure Data Lake
Analisi di flusso supporta [Archivio Data Lake di Azure](https://azure.microsoft.com/services/data-lake-store/). Questa archiviazione consente di archiviare dati di qualsiasi dimensione, tipo e velocità di inserimento per le analisi esplorative e operative. Inoltre, Analisi di flusso deve essere autorizzato ad accedere ad Archivio Data Lake. I dettagli sull'autorizzazione e su come iscriversi per Data Lake Store (se necessario) sono forniti nell'[articolo relativo agli output di Data Lake](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Autorizzare un Archivio Azure Data Lake
Quando Archivio Data Lake è selezionato come output nel portale di gestione di Azure, viene richiesto di autorizzare una connessione a un Archivio Data Lake esistente.  

![Autorizzare Archivio Data Lake](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Compilare quindi le proprietà per l'output di Archivio Data Lake come illustrato di seguito:

![Autorizzare Archivio Data Lake](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione necessari per la creazione di un output di Archivio Data Lake.

<table>
<tbody>
<tr>
<td><B>Nome proprietà</B></td>
<td><B>Descrizione</B></td>
</tr>
<tr>
<td>Alias di output</td>
<td>È un nome descrittivo usato nelle query per indirizzare l'output delle query ad Archivio Data Lake in uso.</td>
</tr>
<tr>
<td>Nome account</td>
<td>Nome dell'account di archiviazione Data Lake a cui si sta inviando l'output. Verrà visualizzato un elenco a discesa degli account di Archivio Data Lake ai quali ha accesso l'utente connesso al portale.</td>
</tr>
<tr>
<td>Schema prefisso percorso [<I>facoltativo</I>]</td>
<td>Percorso del file usato per scrivere i file nell'account di Archivio Data Lake specificato. <BR>{date}, {time}<BR>Esempio 1: folder1/logs/{date}/{time}<BR>Esempio 2: folder1/logs/{date}</td>
</tr>
<tr>
<td>Formato data [<I>facoltativo</I>]</td>
<td>Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG</td>
</tr>
<tr>
<td>Formato ora [<I>facoltativo</I>]</td>
<td>Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH.</td>
</tr>
<tr>
<td>Formato di serializzazione eventi</td>
<td>Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codifica</td>
<td>Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato.</td>
</tr>
<tr>
<td>Delimitatore</td>
<td>Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.</td>
</tr>
<tr>
<td>Format</td>
<td>Applicabile solo per la serializzazione JSON. Separato da righe specifica che l'output verrà formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output verrà formattato come array di oggetti JSON.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Rinnovare l'autorizzazione per Archivio Data Lake
Se la password dell'account di Archivio Data Lake è stata modificata dopo la creazione o l'ultima autenticazione del processo, sarà necessario autenticare nuovamente l'account.

![Autorizzare Archivio Data Lake](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>Database SQL
[database SQL di Azure](https://azure.microsoft.com/services/sql-database/) può essere usato come output per i dati di natura relazionale o per applicazioni che dipendono dal contesto ospitato in un database relazionale. I processi di Analisi di flusso eseguiranno la scrittura in una tabella esistente di un database SQL di Azure.  Si noti che lo schema della tabella deve corrispondere esattamente ai campi e ai relativi tipi generati dal processo. Un [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) può anche essere specificato come output tramite l'opzione di output del database SQL (si tratta di una funzionalità di anteprima). La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un database SQL di output.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |È un nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
| Database |Nome del database a cui si sta inviando l'output |
| Server Name |Nome server del database SQL di Azure |
| Nome utente |Nome utente che ha accesso in scrittura al database |
| Password |Password per connettersi al database |
| Table |Nome della tabella in cui verrà scritto l'output. Il nome della tabella fa distinzione tra maiuscole e minuscole e lo schema della tabella deve corrispondere esattamente al numero di campi e ai relativi tipi generati dall'output del processo. |

> [!NOTE]
> Attualmente l'offerta relativa al database SQL di Azure è supportata per un output di processi in Analisi di flusso. Non è tuttavia supportata una macchina virtuale di Azure che esegue SQL Server con un database collegato. Questo comportamento sarà soggetto a modifiche nelle versioni future.
> 
> 

## <a name="blob-storage"></a>Archiviazione BLOB
L'archiviazione BLOB offre una soluzione conveniente e scalabile per archiviare grandi quantità di dati non strutturati nel cloud.  Per un'introduzione all'archivio BLOB di Azure e al relativo utilizzo, vedere la documentazione in [Come usare i BLOB](../storage/storage-dotnet-how-to-use-blobs.md).

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output del BLOB.

<table>
<tbody>
<tr>
<td>Nome proprietà</td>
<td>Descrizione</td>
</tr>
<tr>
<td>Alias di output</td>
<td>È un nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione BLOB.</td>
</tr>
<tr>
<td>Account di archiviazione</td>
<td>Nome dell'account di archiviazione a cui si sta inviando l'output.</td>
</tr>
<tr>
<td>Chiave dell'account di archiviazione</td>
<td>Chiave privata associata all'account di archiviazione.</td>
</tr>
<tr>
<td>Contenitore di archiviazione</td>
<td>I contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB.</td>
</tr>
<tr>
<td>Schema prefisso percorso [facoltativo]</td>
<td>Percorso del file usato per scrivere i BLOB nel contenitore specificato.<BR>All'interno del percorso è possibile scegliere di usare una o più istanze delle 2 variabili seguenti per specificare la frequenza di scrittura dei BLOB:<BR>{date}, {time}<BR>Esempio 1: cluster1/logs/{date}/{time}<BR>Esempio 2: cluster1/logs/{date}</td>
</tr>
<tr>
<td>Formato data [facoltativo]</td>
<td>Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG</td>
</tr>
<tr>
<td>Formato ora [facoltativo]</td>
<td>Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH.</td>
</tr>
<tr>
<td>Formato di serializzazione eventi</td>
<td>Formato di serializzazione per i dati di output.  Sono supportati i formati JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codifica</td>
<td>Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato.</td>
</tr>
<tr>
<td>Delimitatore</td>
<td>Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.</td>
</tr>
<tr>
<td>Format</td>
<td>Applicabile solo per la serializzazione JSON. Separato da righe specifica che l'output verrà formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output verrà formattato come array di oggetti JSON.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Hub eventi
[Hub eventi](https://azure.microsoft.com/services/event-hubs/) è un ingestor di eventi di pubblicazione-sottoscrizione altamente scalabile. Può raccogliere milioni di eventi al secondo.  Un uso di un hub eventi come output si verifica quando l'output di un processo di analisi di flusso rappresenta l'input di un altro processo di flusso.

Per configurare i flussi dei dati dell'hub eventi, sono necessari alcuni parametri come output.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |È un nome descrittivo usato nelle query per indirizzare l'output delle query a questo hub eventi. |
| Spazio dei nomi del bus di servizio |Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio |
| Hub eventi |Nome dell'output dell'hub eventi |
| Nome criterio hub eventi |Criteri di accesso condiviso che possono essere creati nella scheda Configura dell'hub eventi. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e tasti di scelta |
| Chiave criterio hub eventi |Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio |
| Colonna chiave di partizione [facoltativo] |Questa colonna contiene la chiave di partizione per l'output dell'Hub eventi. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output.  Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format |Applicabile solo per il tipo JSON. Separato da righe specifica che l'output verrà formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output verrà formattato come array di oggetti JSON. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) può essere usato come output per un processo di Analisi di flusso per offrire un'esperienza di visualizzazione avanzata dei risultati di analisi. Questa funzionalità può essere usata per i dashboard operativi, la generazione di report e la creazione di report basati sulle metriche.

### <a name="authorize-a-power-bi-account"></a>Autorizzare un account Power BI
1. Quando Power BI è selezionato come output nel portale di gestione di Azure, verrà richiesto di autorizzare un utente di Power BI esistente oppure di creare un nuovo account di Power BI.  
   
   ![Autorizzare l'utente di Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. Creare un nuovo account se non è ancora presente, quindi scegliere Autorizza ora.  Viene presentata una schermata simile a quella seguente:  
   
   ![Power BI account Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. In questo passaggio immettere l'account aziendale o dell'istituto di istruzione per autorizzare l'output di Power BI. Se non si è già iscritti a Power BI, scegliere Iscriviti ora. L'account aziendale o dell'istituto di istruzione usato per Power BI potrebbe differire dall'account della sottoscrizione di Azure con cui si è attualmente connessi.

### <a name="configure-the-power-bi-output-properties"></a>Configurare le proprietà di output di Power BI
Dopo aver autenticato l'account Power BI, è possibile configurare le proprietà per l'output di Power BI. La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Power BI.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |È un nome descrittivo usato nelle query per indirizzare l'output delle query a questo output di Power BI. |
| Area di lavoro del gruppo |Per abilitare la condivisione dei dati con altri utenti Power BI, è possibile selezionare i gruppi all'interno dell'account Power BI o scegliere "Area di lavoro personale" se non si vuole eseguire la scrittura in un gruppo.  L'aggiornamento di un gruppo esistente richiede il rinnovo dell'autenticazione di Power BI. |
| Nome del set di dati |Immettere un nome per il set di dati che dovrà essere usato dall'output di Power BI |
| Nome tabella |Immettere un nome per la tabella nel set di dati dell'output di Power BI. Attualmente, l’output di Power BI da processi di Analisi di flusso può avere solo una tabella in un set di dati |

Per una procedura dettagliata di configurazione di un output di Power BI e del dashboard, vedere l'articolo [Analisi dei flussi di Azure e Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Non creare in modo esplicito il set di dati e la tabella nel dashboard di Power BI. Il set di dati e la tabella verranno compilati automaticamente quando il processo viene avviato e il processo inizia a generare output in Power BI. Si noti che se la query di processo non genera alcun risultato, il set di dati e la tabella non verranno creati. Tenere anche presente che se Power BI ha già un set di dati e una tabella con lo stesso nome fornito dall'utente nel processo di analisi di flusso, i dati esistenti verranno sovrascritti.
> 
> 

### <a name="schema-creation"></a>Creazione dello schema
Analisi di flusso di Azure crea un set di dati e una tabella di Power BI per conto dell'utente, se non ne esistono già. In tutti gli altri casi la tabella viene aggiornata con nuovi valori. Attualmente non esiste alcuna limitazione che preveda l'esistenza di un'unica tabella in un set di dati.

### <a name="data-type-conversion-from-asa-to-power-bi"></a>Conversione di tipi di dati da Analisi di flusso di Azure in Power BI
Analisi di flusso di Azure consente di aggiornare il modello di dati in modo dinamico in fase di esecuzione se viene modificato lo schema di output. Vengono rilevate tutte le modifiche al nome e al tipo di colonna e l'aggiunta o la rimozione di colonne.

Questa tabella contiene le conversioni dei tipi di dati dai [tipi di dati di analisi di flusso](https://msdn.microsoft.com/library/azure/dn835065.aspx) ai [tipi Entity Data Model (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) di Power BI se non esistono né un set di dati né una tabella di Power BI.


Dall'analisi di flusso | A Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | String
datetime | DateTime
float | Double
Matrice di record | Tipo String, valore di tipo Constant "IRecord" o "IArray"

### <a name="schema-update"></a>Aggiornamento dello schema
L'analisi di flusso deduce lo schema del modello di dati in base al primo set di eventi dell'output. In un secondo momento, se necessario, lo schema del modello di dati viene aggiornato per gestire gli eventi in ingresso che potrebbero non rientrare nello schema originale.

È consigliabile non usare la query `SELECT *` per evitare l'aggiornamento dello schema dinamico nelle righe. Oltre a implicazioni potenziali sulle prestazioni, potrebbe anche verificarsi un problema di indeterminazione del tempo necessario per ottenere i risultati. È necessario selezionare i campi esatti che devono essere visualizzati nel dashboard di Power BI. È anche necessario che i valori dei dati siano conformi al tipo di dati scelto.


Precedente/Corrente | Int64 | string | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | string | String | Double
String | String | String | String |  | string | 
DateTime | string | string |  DateTime | String


### <a name="renew-power-bi-authorization"></a>Rinnovare l'autorizzazione di Power BI
Se la password dell'account Power BI è stata modificata dopo la creazione o l'ultima autenticazione del processo, sarà necessario autenticare nuovamente l'account. Se Multi-Factor Authentication (MFA) è configurata nel tenant Azure Active Directory (AAD), sarà necessario rinnovare anche l'autorizzazione Power BI ogni due settimane. Un sintomo di questo problema è che non ci sono output del processo e un "Errore nell’autenticazione dell’utente" nei log delle operazioni:

  ![Errore token di aggiornamento di Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Power BI.  Fare clic sul collegamento "Rinnovare autorizzazione" e riavviare il processo dall'ultima volta che è stato arrestato per evitare la perdita di dati.

  ![Rinnovo di autorizzazione di Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Archiviazione tabelle
[Archiviazione tabelle di Azure](../storage/storage-introduction.md) offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica di un'applicazione in base alle richieste degli utenti. L'archivio tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft che è possibile sfruttare per i dati strutturati con meno vincoli allo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di tabelle.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |È un nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione tabelle. |
| Account di archiviazione |Nome dell'account di archiviazione a cui si sta inviando l'output. |
| Chiave dell'account di archiviazione |Chiave di accesso associata all'account di archiviazione. |
| Nome tabella |Nome della tabella. Se non esiste, la tabella verrà creata. |
| Chiave di partizione |Nome della colonna di output contenente la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione di una determinata tabella che costituisce la prima parte della chiave primaria di un'entità. Si tratta di un valore stringa le cui dimensioni massime sono di 1 KB. |
| Chiave di riga |Nome della colonna di output contenente la chiave di riga. La chiave di riga è un identificatore univoco per un’entità all'interno di una determinata partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa le cui dimensioni massime sono di 1 KB. |
| Dimensioni batch |Numero di record per un'operazione batch. Il valore predefinito in genere è sufficiente per la maggior parte dei processi. Per altri dettagli sulla modifica di questa impostazione, vedere la [specifica relativa alle operazioni batch su tabella](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx). |

## <a name="service-bus-queues"></a>Code del bus di servizio
[Code del bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx) consentono un recapito dei messaggi di tipo FIFO (First In, First Out) a uno o più consumer concorrenti. Si prevede in genere che i messaggi vengano ricevuti ed elaborati nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di coda.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |È un nome descrittivo usato nelle query per indirizzare l'output delle query a questa coda del bus di servizio. |
| Spazio dei nomi del bus di servizio |Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. |
| Nome coda |Nome della coda del bus di servizio. |
| Nome criteri coda |Durante la creazione di una coda, nella scheda Configura coda è anche possibile creare criteri di accesso condiviso. Ogni criterio di accesso condiviso dispone di un nome, delle autorizzazioni impostate, e di tasti di scelta. |
| Chiave criteri coda |Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output.  Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format |Applicabile solo per il tipo JSON. Separato da righe specifica che l'output verrà formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output verrà formattato come array di oggetti JSON. |

## <a name="service-bus-topics"></a>Argomenti del bus di servizio
Le code del bus di servizio forniscono un metodo di comunicazione uno-a-uno dal mittente al destinatario, invece gli [argomenti del bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrono una forma di comunicazione uno-a-molti.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di tabelle.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |È un nome descrittivo usato nelle query per indirizzare l'output delle query a questo argomento del bus di servizio. |
| Spazio dei nomi del bus di servizio |Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio |
| Nome argomento |Gli argomenti sono entità di messaggistica, simili agli Hub eventi e alle code. Sono progettati per raccogliere i flussi di eventi da diversi dispositivi e servizi. Quando un argomento viene creato, gli viene assegnato un nome specifico. Dal momento che i messaggi inviati a un argomento non saranno disponibili se non viene creata una sottoscrizione, assicurarsi che esistano una o più sottoscrizioni per l'argomento |
| Nome criteri argomento |Durante la creazione di un argomento, nella scheda Configura argomento è anche possibile creare criteri di accesso condiviso. Ogni criterio di accesso condiviso ha un nome, autorizzazioni impostate e tasti di scelta |
| Chiave criteri argomento |Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output.  Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |

## <a name="documentdb"></a>DocumentDB
[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) è un servizio di database di documenti NoSQL completamente gestito che offre query e transazioni su dati senza schema, prestazioni prevedibili e affidabili e sviluppo rapido.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output DocumentDB.

<table>
<tbody>
<tr>
<td>Nome proprietà</td>
<td>DESCRIZIONE</td>
</tr>
<tr>
<td>Nome account</td>
<td>Nome dell'account DocumentDB.  Può essere anche l'endpoint per l'account.</td>
</tr>
<tr>
<td>Chiave account</td>
<td>La chiave di accesso condiviso per l'account DocumentDB.</td>
</tr>
<tr>
<td>Database</td>
<td>Il nome del database DocumentDB.</td>
</tr>
<tr>
<td>Modello del nome di raccolta</td>
<td>Il modello del nome di raccolta per le raccolte da utilizzare. Il formato del nome di raccolta può essere costruito utilizzando il token {partizione} facoltativo, dove le partizioni iniziano da 0.<BR>ad esempio Di seguito sono gli input validi:<BR>MyCollection{partition}<BR>MyCollection<BR>Si noti che le raccolte devono esistere prima che il processo di analisi di flusso sia avviato e non verranno create automaticamente.</td>
</tr>
<tr>
<td>Chiave di partizione</td>
<td>Il nome del campo negli eventi di output utilizzato per specificare la chiave per il partizionamento di output nelle raccolte.</td>
</tr>
<tr>
<td>Document ID</td>
<td>Il nome del campo negli eventi di output utilizzato per specificare la chiave primaria su cui si basano le operazioni di inserimento o aggiornamento.</td>
</tr>
</tbody>
</table>


## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
È stato presentato Analisi di flusso, un servizio gestito per l'analisi di flusso su dati provenienti da Internet delle cose. Per altre informazioni su questo servizio, vedere:

* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301



<!--HONumber=Dec16_HO2-->


