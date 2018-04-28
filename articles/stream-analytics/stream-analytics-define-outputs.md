---
title: Tipi di output di processi di Analisi di flusso di Azure
description: Informazioni sulla destinazione di opzioni di output dei dati di Analisi di flusso tra cui Power BI per i risultati dell'analisi.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 30fa7e081c24339b7fa9f572d9feb25a0f920a86
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="stream-analytics-outputs-options-for-storage-and-analysis"></a>Output di Analisi di flusso: opzioni per l'archiviazione e l'analisi
Quando si crea un processo di Analisi di flusso di Azure, una considerazione da fare riguarda l'uso dei dati risultanti. Come è possibile visualizzare i risultati del processo di Analisi di flusso di Azure e dove è possibile archiviarli?

Per poter abilitare un'ampia gamma di modelli dell'applicazione, Analisi di flusso di Azure offre metodi diversi per archiviare l'output e visualizzare i risultati dell'analisi. In questo modo è possibile sia visualizzare facilmente l'output del processo che ottenere flessibilità nell'uso e nell'archiviazione dell'output del processo per il data warehouse e altri scopi. Qualsiasi elemento output configurato nel processo deve esistere prima che il processo venga avviato e gli eventi avviino il flusso. Ad esempio, se si utilizza l'archiviazione BLOB come output, il processo non crea un account di archiviazione automaticamente. Creare un account di archiviazione prima dell'avvio del processo di Analisi di flusso di Azure.

## <a name="azure-data-lake-store"></a>Archivio Azure Data Lake
Analisi di flusso supporta [Archivio Data Lake di Azure](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Archivio Data Lake consente di archiviare dati di qualsiasi dimensione, tipo e velocità di inserimento per le analisi esplorative e operative. Inoltre, Analisi di flusso deve essere autorizzato ad accedere ad Archivio Data Lake.

### <a name="authorize-an-azure-data-lake-store-account"></a>Autorizzare un account Azure Data Lake Store

1. Se nel portale di Azure è selezionato Data Lake Store come output, viene richiesto di autorizzare una connessione a un Data Lake Store esistente.  

   ![Autorizzare Archivio Data Lake](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

2. Se si ha già accesso a Data Lake Store, fare clic su "Autorizza ora". Verrà visualizzata una pagina con il messaggio "Reindirizzamento all'autorizzazione in corso". Dopo che l'autorizzazione ha esito positivo, viene visualizzata la pagina che consente di configurare l'output di Data Lake Store.  

3. Dopo aver autenticato l'account, è possibile configurare le proprietà per l'output di Archivio Data Lake. La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Archivio Data Lake.

   ![Autorizzare Archivio Data Lake](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

<table>
<tbody>
<tr>
<td><B>NOME PROPRIETÀ</B></td>
<td><B>DESCRIZIONE</B></td>
</tr>
<tr>
<td>Alias di output</td>
<td>Nome descrittivo usato nelle query per indirizzare l'output delle query a questo Data Lake Store.</td>
</tr>
<tr>
<td>Nome account</td>
<td>Nome dell'account di archiviazione Data Lake a cui si sta inviando l'output. Viene visualizzato un elenco a discesa degli account di Data Lake Store disponibili nella sottoscrizione.</td>
</tr>
<tr>
<td>Schema prefisso percorso</td>
<td>Percorso del file usato per scrivere i file nell'account di Archivio Data Lake specificato. È possibile specificare una o più istanze delle variabili {date} e {time}.<BR> Esempio 1: folder1/logs/{date}/{time}<BR>Esempio 2: folder1/logs/{date}<BR>Inoltre, in queste situazioni viene creato un nuovo file:<BR>1. Modifica allo schema di output <BR>2. Riavvio interno o esterno di un processo<BR><BR>Inoltre, se il modello di percorso del file non contiene un "/" finale, l'ultimo modello nel percorso del file viene considerato come un prefisso del nome file.<BR></td>
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
<td>Applicabile solo per la serializzazione JSON. Separato da righe specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna gestione speciale durante la scrittura del file di output.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Rinnovare l'autorizzazione per Archivio Data Lake
Se la password dell'account Data Lake Store è stata modificata dopo la creazione o l'ultima autenticazione del processo, è necessario autenticare nuovamente l'account. Se non si ripete l'autenticazione, il processo non restituirà risultati e nei log delle operazioni verrà registrato un errore che indica che è necessario ripetere l'autorizzazione. Attualmente esiste una limitazione secondo cui il token di autenticazione deve essere aggiornato manualmente ogni 90 giorni per tutti i processi con output di Archivio Data Lake. 

Per rinnovare l'autorizzazione, **arrestare** il processo, passare all'output di Data Lake Store, quindi fare clic sul collegamento **Rinnova autorizzazione**. Per un istante verrà visualizzata una pagina che indica "Reindirizzamento all'autorizzazione...". La pagina verrà chiusa automaticamente e, in caso di esito positivo, verrà indicato "Autorizzazione rinnovata". È quindi necessario fare clic su **Salva** nella parte inferiore della pagina e, per continuare, riavviare il processo dall'**ora dell'ultimo arresto** per evitare la perdita di dati.

![Autorizzare Archivio Data Lake](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>Database SQL
[database SQL di Azure](https://azure.microsoft.com/services/sql-database/) può essere usato come output per i dati di natura relazionale o per applicazioni che dipendono dal contesto ospitato in un database relazionale. I processi di Analisi di flusso di Azure eseguono la scrittura in una tabella esistente di un database SQL di Azure.  Lo schema della tabella deve corrispondere esattamente ai campi e ai relativi tipi generati dal processo. Un [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) può anche essere specificato come output tramite l'opzione di output del database SQL. La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un database SQL di output.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
| Database |Nome del database a cui si sta inviando l'output |
| Server Name |Nome server del database SQL di Azure |
| Username |Nome utente che ha accesso in scrittura al database |
| Password |Password per connettersi al database |
| Tabella |Nome della tabella in cui viene scritto l'output. Il nome della tabella fa distinzione tra maiuscole e minuscole e lo schema della tabella deve corrispondere esattamente al numero di campi e ai relativi tipi generati dall'output del processo. |

> [!NOTE]
> Attualmente l'offerta relativa al database SQL di Azure è supportata per un output di processi in Analisi di flusso. Non è tuttavia supportata una macchina virtuale di Azure che esegue SQL Server con un database collegato. Questo comportamento sarà soggetto a modifiche nelle versioni future.
> 
> 

## <a name="blob-storage"></a>Archiviazione BLOB
L'archiviazione BLOB offre una soluzione conveniente e scalabile per archiviare grandi quantità di dati non strutturati nel cloud.  Per un'introduzione all'archivio BLOB di Azure e al relativo utilizzo, vedere la documentazione in [Come usare i BLOB](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output del BLOB.

<table>
<tbody>
<tr>
<td>Nome proprietà</td>
<td>DESCRIZIONE</td>
</tr>
<tr>
<td>Alias di output</td>
<td>Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione BLOB.</td>
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
<td>Il modello di percorso del file usato per scrivere i BLOB nel contenitore specificato. <BR> Nel modello del percorso è possibile scegliere di usare una o più istanze delle 2 variabili seguenti per specificare la frequenza di scrittura dei BLOB: <BR> {date}, {time} <BR> Esempio 1: cluster1/logs/{date}/{time} <BR> Esempio 2: cluster1/logs/{date} <BR> <BR> La denominazione dei file segue questa convenzione: <BR> {Schema prefisso percorso}/schemaHashcode_Guid_Number.extension <BR> <BR> File di output di esempio: <BR> Myoutput/20170901/00/45434_gguid_1.csv <BR> Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR> Inoltre, in queste situazioni viene creato un nuovo file: <BR> 1. Il file corrente supera il numero massimo consentito di blocchi (attualmente 50.000) <BR> 2. Modifica allo schema di output <BR> 3. Riavvio interno o esterno di un processo  </td>
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
<td>Applicabile solo per la serializzazione JSON. Separato da righe specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna gestione speciale durante la scrittura del file di output.</td>
</tr>
</tbody>
</table>

Quando si usa come output l'archiviazione BLOB, viene creato un nuovo file nel BLOB nei casi seguenti:

* Se il file supera il numero massimo di blocchi consentiti. Si noti che il numero massimo consentito di blocchi può essere raggiunto senza raggiungere le dimensioni massime consentite per il BLOB. Ad esempio, se la frequenza di output è elevata, ogni blocco può contenere più byte e le dimensioni del file sono superiori. Se la frequenza di output è bassa, ogni blocco contiene meno dati e le dimensioni del file sono inferiori.  
* Se l'output contiene una modifica dello schema e il formato di output richiede uno schema fisso (CSV e Avro).  
* Se un processo viene riavviato esternamente o in caso di riavvio interno di un processo.  
* Se la query è completamente partizionata, per ogni partizione di output viene creato un nuovo file.  
* Se un file o un contenitore dell'account di archiviazione viene eliminato dall'utente.  
* Se l'output è partizionato in base al tempo usando lo schema prefisso percorso, quando la query passa all'ora successiva viene usato un nuovo BLOB.

## <a name="event-hub"></a>Hub eventi
[Hub eventi](https://azure.microsoft.com/services/event-hubs/) è un ingestor di eventi di pubblicazione-sottoscrizione altamente scalabile. Può raccogliere milioni di eventi al secondo. Un uso di un hub eventi come output si verifica quando l'output di un processo di Analisi di flusso di Azure diventa l'input di un altro processo di streaming.

Per configurare i flussi dei dati dell'hub eventi, sono necessari alcuni parametri come output.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo hub eventi. |
| Spazio dei nomi del bus di servizio |Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi del bus di servizio |
| Hub eventi |Nome dell'output dell'hub eventi |
| Nome criterio hub eventi |Criteri di accesso condiviso che possono essere creati nella scheda Configura dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso |
| Chiave criterio hub eventi |Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio |
| Colonna chiave di partizione [facoltativo] |Questa colonna contiene la chiave di partizione per l'output dell'Hub eventi. |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output.  Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format |Applicabile solo per la serializzazione JSON. Separato da righe specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna gestione speciale durante la scrittura del file di output. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) può essere usato come output per un processo di Analisi di flusso per offrire un'esperienza di visualizzazione avanzata dei risultati di analisi. Questa funzionalità può essere usata per i dashboard operativi, la generazione di report e la creazione di report basati sulle metriche.

### <a name="authorize-a-power-bi-account"></a>Autorizzare un account Power BI
1. Se nel portale di Azure è selezionato Power BI come output, viene richiesto di autorizzare un utente Power BI esistente oppure di creare un nuovo account Power BI.  
   
   ![Autorizzare l'utente di Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. Creare un nuovo account se non è ancora presente, quindi scegliere Autorizza ora.  Viene presentata una schermata simile a quella seguente:  
   
   ![Power BI account Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. In questo passaggio immettere l'account aziendale o dell'istituto di istruzione per autorizzare l'output di Power BI. Se non si è già iscritti a Power BI, scegliere Iscriviti ora. L'account aziendale o dell'istituto di istruzione usato per Power BI potrebbe differire dall'account della sottoscrizione di Azure con cui si è attualmente connessi.

### <a name="configure-the-power-bi-output-properties"></a>Configurare le proprietà di output di Power BI
Dopo aver autenticato l'account Power BI, è possibile configurare le proprietà per l'output di Power BI. La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Power BI.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo output di Power BI. |
| Area di lavoro del gruppo |Per abilitare la condivisione dei dati con altri utenti Power BI, è possibile selezionare i gruppi all'interno dell'account Power BI o scegliere "Area di lavoro personale" se non si vuole eseguire la scrittura in un gruppo.  L'aggiornamento di un gruppo esistente richiede il rinnovo dell'autenticazione di Power BI. |
| Nome del set di dati |Immettere un nome per il set di dati che dovrà essere usato dall'output di Power BI |
| Nome tabella |Immettere un nome per la tabella nel set di dati dell'output di Power BI. Attualmente, l’output di Power BI da processi di Analisi di flusso può avere solo una tabella in un set di dati |

Per una procedura dettagliata di configurazione di un output di Power BI e del dashboard, vedere l'articolo [Analisi dei flussi di Azure e Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Non creare in modo esplicito il set di dati e la tabella nel dashboard di Power BI. Il set di dati e la tabella vengono compilati automaticamente quando il processo viene avviato e il processo inizia a generare output in Power BI. Si noti che se la query di processo non genera alcun risultato, il set di dati e la tabella non vengono creati. Si noti che se Power BI ha già un set di dati e una tabella con lo stesso nome fornito dall'utente nel processo di analisi di flusso, i dati esistenti vengono sovrascritti.
> 
> 

### <a name="schema-creation"></a>Creazione dello schema
Analisi di flusso di Azure crea un set di dati e una tabella di Power BI per conto dell'utente, se non esiste già. In tutti gli altri casi, la tabella viene aggiornata con nuovi valori. Attualmente è presente una limitazione che prevede l'esistenza di una sola tabella in un set di dati.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Conversione di tipi di dati da Analisi di flusso di Azure a Power BI
Analisi di flusso di Azure consente di aggiornare il modello di dati in modo dinamico in fase di esecuzione se viene modificato lo schema di output. Vengono rilevate tutte le modifiche al nome e al tipo di colonna e l'aggiunta o la rimozione di colonne.

Questa tabella contiene le conversioni dei tipi di dati dai [tipi di dati di analisi di flusso](https://msdn.microsoft.com/library/azure/dn835065.aspx) ai [tipi Entity Data Model (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) di Power BI se non esistono né un set di dati né una tabella di Power BI.


Dall'analisi di flusso | A Power BI
-----|-----|------------
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
string | string | string | string |  | string | 
DateTime | string | string |  DateTime | string


### <a name="renew-power-bi-authorization"></a>Rinnovare l'autorizzazione di Power BI
Se la password dell'account Power BI è stata modificata dopo la creazione o l'ultima autenticazione del processo, è necessario autenticare nuovamente l'account. Se nel tenant di Azure Active Directory (AAD) è configurata la Multi-Factor Authentication (MFA), sarà necessario rinnovare anche l'autorizzazione di Power BI ogni due settimane. Un sintomo di questo problema è che non ci sono output del processo e un "Errore nell’autenticazione dell’utente" nei log delle operazioni:

  ![Errore token di aggiornamento di Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Power BI.  Fare clic sul collegamento "Rinnovare autorizzazione" e riavviare il processo dall'ultima volta che è stato arrestato per evitare la perdita di dati.

  ![Rinnovo autorizzazione di Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Archiviazione tabelle
[Archiviazione tabelle di Azure](../storage/common/storage-introduction.md) offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica di un'applicazione in base alle richieste degli utenti. Archiviazione tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft che è possibile sfruttare per i dati strutturati con meno vincoli allo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di tabelle.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione tabelle. |
| Account di archiviazione |Nome dell'account di archiviazione a cui si sta inviando l'output. |
| Chiave dell'account di archiviazione |Chiave di accesso associata all'account di archiviazione. |
| Nome tabella |Nome della tabella. Se non esiste, viene creata una nuova tabella. |
| Chiave di partizione |Nome della colonna di output contenente la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione di una determinata tabella che costituisce la prima parte della chiave primaria di un'entità. Si tratta di un valore stringa le cui dimensioni massime sono di 1 KB. |
| Chiave di riga |Nome della colonna di output contenente la chiave di riga. La chiave di riga è un identificatore univoco per un’entità all'interno di una determinata partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa le cui dimensioni massime sono di 1 KB. |
| Dimensioni batch |Numero di record per un'operazione batch. Il valore predefinito in genere è sufficiente per la maggior parte dei processi. Per altri dettagli sulla modifica di questa impostazione, vedere la [specifica relativa alle operazioni batch su tabella](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx). |
 
## <a name="service-bus-queues"></a>Code del bus di servizio
[Code del bus di servizio](https://msdn.microsoft.com/library/azure/hh367516.aspx) consentono un recapito dei messaggi di tipo FIFO (First In, First Out) a uno o più consumer concorrenti. Si prevede in genere che i messaggi vengano ricevuti ed elaborati nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di coda.

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa coda del bus di servizio. |
| Spazio dei nomi del bus di servizio |Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. |
| Nome coda |Nome della coda del bus di servizio. |
| Nome criteri coda |Durante la creazione di una coda, nella scheda Configura coda è anche possibile creare criteri di accesso condiviso. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri coda |Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio |
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output.  Sono supportati i formati JSON, CSV e Avro. |
| Codifica |Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format |Applicabile solo per il tipo JSON. Separato da righe specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output viene formattato come matrice di oggetti JSON. |

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
| Formato di serializzazione eventi |Formato di serializzazione per i dati di output.  Sono supportati i formati JSON, CSV e Avro. |
 | Codifica |Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato |
| Delimitatore |Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |

Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) è un servizio di database multimodello distribuito a livello globale che offre scalabilità elastica illimitata in tutto il mondo, query avanzate e indicizzazione automatica su modelli di dati indipendenti dallo schema, è garantito a bassa latenza ed è leader del settore per i contratti di servizio completi. Per informazioni sulle opzioni di raccolta di Cosmos DB per Analisi di flusso, consultare l'articolo [Analisi di flusso con Cosmos DB come output](stream-analytics-documentdb-output.md).

> [!Note]
> Ad oggi, Analisi di flusso di Azure supporta la connessione a CosmosDB solo tramite l'**API SQL**.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente. 

Nella tabella seguente sono descritte le proprietà per la creazione di un output di Azure Cosmos DB.
| Nome proprietà | DESCRIZIONE |
| --- | --- |
| Alias di output | Alias per fare riferimento a questo output nella query di Analisi di flusso di Azure. |
| Sink | Cosmos DB |
| Opzione di importazione | Scegliere "Selezionare Cosmos DB dalle sottoscrizioni correnti" o "Specificare le impostazioni di Cosmos DB manualmente".
| ID account | Nome o URI endpoint dell'account Cosmos DB. |
| Chiave account | Chiave di accesso condiviso per l'account Cosmos DB. |
| Database | Nome del database Cosmos DB. |
| Modello nome raccolta | Nome di raccolta o relativo modello per le raccolte da usare. <br/>Il formato del nome di raccolta può essere costruito utilizzando il token {partizione} facoltativo, dove le partizioni iniziano da 0. Due esempi:  <br/>1. _MyCollection_: deve essere presente una raccolta denominata "MyCollection".  <br/>2. _MyCollection{partition}_: basata sulla colonna di partizionamento. <br/>Devono essere presenti le raccolte della colonna di partizionamento "MyCollection0", "MyCollection1", "MyCollection2" e così via. |
| Chiave di partizione | facoltativo. È necessario solo se si usa un token {partition} nel modello del nome di raccolta.<br/> La chiave di partizione è il nome del campo negli eventi di output usato per specificare la chiave per il partizionamento dell'output tra le raccolte.<br/> Per l'output di una singola raccolta si può usare qualsiasi colonna di output arbitraria, ad esempio PartitionId. |
| Document ID |facoltativo. Il nome del campo negli eventi di output usato per specificare la chiave primaria su cui si basano le operazioni di inserimento o aggiornamento.  

## <a name="azure-functions"></a>Funzioni di Azure
Funzioni di Azure è un servizio di calcolo senza server che consente di eseguire codice su richiesta senza dover gestire l'infrastruttura o effettuare il provisioning in modo esplicito. Consente di implementare il codice attivato da eventi generati nei servizi di Azure o in servizi di terze parti.  La possibilità offerta da Funzioni di Azure di rispondere ai trigger la rende l'output naturale per Analisi di flusso di Azure. Questo adattatore di output consente agli utenti di collegare Analisi di flusso a Funzioni di Azure ed eseguire uno script o una porzione di codice in risposta a diversi eventi.

Analisi di flusso di Azure richiama Funzioni di Azure tramite trigger HTTP. Il nuovo adattatore di input di Funzioni di Azure è disponibile con le seguenti proprietà configurabili:

| Nome proprietà | DESCRIZIONE |
| --- | --- |
| App per le funzioni |Nome dell'app Funzioni di Azure |
| Funzione |Nome della funzione nell'app Funzioni di Azure |
| Dimensioni massime batch |Questa proprietà può essere usata per impostare le dimensioni massime per ogni batch di output inviato a Funzioni di Azure. Per impostazione predefinita questo valore è 256 KB. |
| Numero massimo di batch  |Come indica il nome, questa proprietà consente di specificare il numero massimo di eventi in ogni batch che vengono inviati a Funzioni di Azure. Il valore di conteggio massimo di batch predefinito è 100 |
| Chiave |Per usare una funzione di Azure di un'altra sottoscrizione, è necessario fornire la chiave per accedere alla funzione |

Si noti che quando Analisi di flusso di Azure riceve l'eccezione 413 (Entità richiesta troppo grande) dalla funzione di Azure, riduce la dimensione dei batch che invia a Funzioni di Azure. Usare questa eccezione nel codice della funzione di Azure per fare in modo che Analisi di flusso di Azure non invii batch troppo grandi. Assicurarsi anche che i valori relativi alle dimensioni massime e al numero massimo di batch usati nella funzione siano conformi ai valori inseriti nel portale di Analisi di flusso di Azure. 

Quando non avviene alcun evento in un intervallo di tempo, non viene generato alcun output. Di conseguenza, non viene chiamata la funzione computeResult. Questo comportamento è coerente con le funzioni di aggregazione finestra predefinite.

## <a name="partitioning"></a>Partizionamento

Nella tabella seguente viene riepilogato il supporto della partizione e il numero di writer di output per ogni tipo di output:

| Tipo di output | Supporto del partizionamento | Chiave di partizione  | Numero di writer di output | 
| --- | --- | --- | --- |
| Archivio Azure Data Lake | Sì | Use i token {date} e {time} nello schema prefisso percorso. Scegliere il formato della data, ad esempio YYYY/MM/DD, DD/MM/YYYY, MM-DD-YYYY. HH viene usato per il formato dell'ora. | Uguale all'input. | 
| database SQL di Azure | No  | Nessuna | Non applicabile | 
| Archivio BLOB di Azure | Sì | Usare i token {date} e {time} nel modello di percorso. Scegliere il formato della data, ad esempio YYYY/MM/DD, DD/MM/YYYY, MM-DD-YYYY. HH viene usato per il formato dell'ora. | Uguale all'input. | 
| Hub eventi di Azure | Sì | Sì | Uguale alle partizioni di Hub eventi di output. |
| Power BI | No  | Nessuna | Non applicabile | 
| Archiviazione tabelle di Azure | Sì | Qualsiasi colonna di output.  | Uguale all'input o al passaggio precedente. | 
| Argomento del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.| Uguale all'output.  |
| Coda del bus di servizio di Azure | Sì | Scelto automaticamente. Il numero di partizioni è [basato sullo SKU e sulle dimensioni del bus di servizio](../service-bus-messaging/service-bus-partitioning.md). La chiave di partizione è un valore intero univoco per ogni partizione.| Uguale all'output. |
| Azure Cosmos DB | Sì | Usare il token {partition} nel modello del nome della raccolta. Il valore di {partition} è basato sulla clausola PARTITION BY nella query. | Uguale all'input. |
| Funzioni di Azure | No  | Nessuna | Non applicabile | 


## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
È stato presentato Analisi di flusso, un servizio gestito per l'analisi di flusso su dati provenienti da Internet delle cose. Per altre informazioni su questo servizio, vedere:

* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
