---
title: Registrazione Analitica di archiviazione di Azure
description: Informazioni su come registrare informazioni dettagliate sulle richieste effettuate per archiviazione di Azure.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: ab235c67e3a0e60999a0348d03a6e938944f7030
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260188"
---
# <a name="azure-storage-analytics-logging"></a>Registrazione analitica dell'archiviazione Azure

Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo.

 La registrazione Analitica dell'archiviazione non è abilitata per impostazione predefinita per l'account di archiviazione. È possibile abilitarla nel [portale di Azure](https://portal.azure.com/); per informazioni dettagliate, vedere [Monitorare un account di archiviazione nel portale di Azure](/azure/storage/storage-monitor-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Usare la [Get Blob Service Properties](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [Get Queue Service Properties](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties), e [Get Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) operazioni per abilitare archiviazione Analitica per ogni servizio.

 Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Ad esempio, se un account di archiviazione presenta un'attività nell'endpoint Blob ma non nel relativo endpoint tabella o coda, verrà creato solo log relativi al servizio Blob.

> [!NOTE]
>  La registrazione Analitica dell'archiviazione è attualmente disponibile solo per i servizi Blob, coda e tabella. Tuttavia, l'account di archiviazione premium non è supportato.

## <a name="requests-logged-in-logging"></a>Richieste registrate durante la registrazione

### <a name="logging-authenticated-requests"></a>Registrazione delle richieste autenticate

 Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite
- Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo
- Richieste tramite una firma di accesso condiviso (SAS) o OAuth, incluse le richieste riuscite e non riuscite
- Richieste ai dati di analisi

  Le richieste eseguite dalla stessa Analisi archiviazione, ad esempio, la creazione oppure l'eliminazione di log, non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato log Analisi archiviazione](/rest/api/storageservices/storage-analytics-log-format).

### <a name="logging-anonymous-requests"></a>Registrazione di richieste anonime

 Vengono registrati i seguenti tipi di richieste anonime:

- Richieste riuscite
- Errori server
- Errori di timeout per client e server
- Richieste GET non riuscite con codice di errore 304 (non modificate)

  Tutte le altre richieste anonime non riuscite non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato log Analisi archiviazione](/rest/api/storageservices/storage-analytics-log-format).

## <a name="how-logs-are-stored"></a>Come vengono archiviati i log

Tutti i log vengono archiviati in BLOB in blocchi in un contenitore denominato `$logs`, che viene creato automaticamente quando archiviazione Analitica è abilitata per un account di archiviazione. Il `$logs` contenitore si trova nello spazio dei nomi blob dell'account di archiviazione, ad esempio: `http://<accountname>.blob.core.windows.net/$logs`. Questo contenitore non può essere eliminato una volta abilitata Analisi di archiviazione, sebbene sia possibile eliminarne il contenuto. Se si usa lo strumento di esplorazione delle informazioni archiviate per passare direttamente al contenitore, verrà visualizzato tutti i BLOB contenenti i dati di registrazione.

> [!NOTE]
>  Il `$logs` contenitore non viene visualizzato quando viene eseguita un'operazione elenco contenitori, ad esempio l'operazione di elenco contenitori. È necessario effettuare l'accesso diretto. Ad esempio, è possibile usare l'operazione elencare i BLOB per accedere ai BLOB nel `$logs` contenitore.

Nel momento in cui vengono registrate le richieste, Analisi archiviazione carica i risultati intermedi come blocchi. Analisi archiviazione invierà periodicamente questi blocchi e li renderà disponibili come BLOB. Può richiedere fino a un'ora prima che i dati di log BLOB presenti nel **$logs** contenitore poiché la frequenza con cui il servizio di archiviazione Scarica i writer di log. Per i log creati nella stessa ora possono esistere record duplicati. È possibile determinare se un record è un duplicato controllandone il numero **RequestId** e **Operation**.

Se si dispone di un volume elevato di dati con più file di log per ogni ora, è possibile usare i metadati del blob per determinare quali dati contenuti nel log esaminando i campi di metadati del blob. È inoltre utile poiché possono talvolta essere presenti un ritardo mentre i dati vengono scritti nei file di log: i metadati del blob offre un'indicazione più accurata del contenuto del blob da quello del blob.

La maggior parte degli strumenti di esplorazione di archiviazione consentono di visualizzare i metadati dei BLOB; è anche possibile leggere queste informazioni usando PowerShell o a livello di codice. Il frammento di PowerShell seguente è riportato un esempio di filtro nell'elenco di BLOB di log in base al nome per specificare un'ora e dai metadati per identificare soltanto i log che contengono **scrivere** operazioni.  

 ```  
 Get-AzureStorageBlob -Container '$logs' |  
 where {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 foreach {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Per informazioni sull'elenco di BLOB a livello di codice, vedere [enumerazione di risorse Blob](http://msdn.microsoft.com/library/azure/hh452233.aspx) e [impostazione e recupero di proprietà e metadati per le risorse Blob](http://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Convenzioni di denominazione dei log

 Ogni log verrà scritto nel formato seguente:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 La tabella seguente descrive ogni attributo del nome di log:

|Attributo|DESCRIZIONE|
|---------------|-----------------|
|`<service-name>`|Nome del servizio di archiviazione. Ad esempio: `blob`, `table`, o `queue`|
|`YYYY`|L'anno a quattro cifre per il log. Ad esempio: `2011`|
|`MM`|Mese a due cifre per il log. Ad esempio: `07`|
|`DD`|Il giorno a due cifre per il log. Ad esempio: `31`|
|`hh`|L'ora a due cifre che indica l'ora di inizio per i log, in UTC 24 ore di formato. Ad esempio: `18`|
|`mm`|Numero a due cifre che indica il minuto iniziale per i log. **Nota:**  Questo valore non è supportato nella versione corrente di Analitica di archiviazione e il relativo valore sarà sempre `00`.|
|`<counter>`|Contatore base zero con sei cifre che indica il numero di BLOB di log generati per il servizio di archiviazione in un'ora. Questo contatore parte da `000000`. Ad esempio: `000001`|

 Di seguito è un nome di log di esempio completo che combina gli esempi precedenti:

 `blob/2011/07/31/1800/000001.log`

 Di seguito è riportato un esempio di URI che può essere utilizzato per accedere al log precedente:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando viene registrata una richiesta di archiviazione, il nome log risultante è correlato all'ora in cui è stata completata l'operazione richiesta. Ad esempio, se una richiesta GetBlob è stata completata alle 6:30 PM 7/31/2011, il log viene scritto con il prefisso seguente: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadati dei log

 Tutti i BLOB dei log vengono archiviati con i metadati che possono essere utilizzati per identificare i dati di registrazione contenuti nei BLOB. La tabella seguente descrive ciascun attributo dei metadati:

|Attributo|DESCRIZIONE|
|---------------|-----------------|
|`LogType`|Descrive se il log contiene informazioni relative alle operazioni di lettura, scrittura o eliminazione. Questo valore può includere un solo tipo o una combinazione di tutti e tre, separati da virgola.<br /><br /> Esempio 1: `write`<br /><br /> Esempio 2: `read,write`<br /><br /> Esempio 3: `read,write,delete`|
|`StartTime`|La prima ora di una voce nel log, sotto forma di `YYYY-MM-DDThh:mm:ssZ`. Ad esempio: `2011-07-31T18:21:46Z`|
|`EndTime`|L'ultima ora di una voce nel log, sotto forma di `YYYY-MM-DDThh:mm:ssZ`. Ad esempio: `2011-07-31T18:22:09Z`|
|`LogVersion`|Versione del formato del log.|

 L'elenco seguente contiene i metadati di esempio completi tramite gli esempi precedenti:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Abilitare la registrazione di archiviazione

È possibile abilitare la registrazione dell'archiviazione con il portale di Azure, PowerShell e gli SDK di archiviazione.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Abilitare la registrazione di archiviazione nel portale di Azure  

Nel portale di Azure, usare il **delle impostazioni di diagnostica (versione classica)** per controllare registrazione archiviazione, accessibili dal pannello il **monitoraggio (versione classica)** sezione di un account di archiviazione **pannello del Menu** .

È possibile specificare i servizi di archiviazione che si desidera accedere e il periodo di memorizzazione (in giorni) per i dati registrati.  

### <a name="enable-storage-logging-using-powershell"></a>Abilitare la registrazione di archiviazione usando PowerShell  

 È possibile usare PowerShell nel computer locale per configurare registrazione archiviazione nell'account di archiviazione usando il cmdlet di Azure PowerShell **Get-AzureStorageServiceLoggingProperty** per recuperare le impostazioni correnti e il cmdlet  **Set-AzureStorageServiceLoggingProperty** per modificare le impostazioni correnti.  

 I cmdlet che controllano registrazione archiviazione usano un **LoggingOperations** parametro che è una stringa contenente un elenco delimitato da virgole di tipi di richiesta per accedere. I tre tipi possibili di richiesta sono **letto**, **scrivere**, e **Elimina**. Per disattivare la registrazione, usare il valore **none** per il **LoggingOperations** parametro.  

 Il seguente comando attiva la registrazione per la lettura, scrittura ed eliminazione delle richieste nel servizio di Accodamento nell'account di archiviazione predefinito con la conservazione di cinque giorni:  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Queue   
-LoggingOperations read,write,delete -RetentionDays 5  
```  

 Il seguente comando Disattiva la registrazione per il servizio tabelle nell'account di archiviazione predefinito:  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Table   
-LoggingOperations none  
```  

 Per informazioni su come configurare i cmdlet di Azure PowerShell per usare la sottoscrizione di Azure e su come selezionare l'account di archiviazione predefinito da utilizzare, vedere [Come installare e configurare Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Abilitare la registrazione a livello di codice di archiviazione  
 Oltre a usare il portale di Azure o i cmdlet di PowerShell di Azure per controllare registrazione archiviazione, è anche possibile usare una delle API di archiviazione di Azure. Ad esempio, se si usa un linguaggio .NET è possibile usare la libreria Client di archiviazione.  

 Le classi **CloudBlobClient**, **CloudQueueClient**, e **CloudTableClient** dispongono tutte di metodi, ad esempio **SetServiceProperties** e **SetServicePropertiesAsync** che accettano un **ServiceProperties** oggetto come parametro. È possibile usare la **ServiceProperties** oggetto per configurare registrazione archiviazione. Il seguente, ad esempio, C# frammento di codice viene illustrato come modificare il periodo di memorizzazione per la registrazione della coda e ciò che viene registrato:  

```  
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Per altre informazioni sull'uso di un linguaggio .NET per configurare registrazione archiviazione, vedere [riferimenti alla libreria Client di archiviazione](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Per informazioni generali sulla configurazione di registrazione archiviazione usando l'API REST, vedere [abilitazione e configurazione di archiviazione Analitica](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Scaricare i dati di log alla registrazione di archiviazione

 Per visualizzare e analizzare i dati di log, è necessario scaricare i BLOB contenenti i dati di log che si è interessati a un computer locale. Molti strumenti di esplorazione di archiviazione consentono di scaricare i BLOB dall'account di archiviazione; è anche possibile usare il team di archiviazione di Azure fornito da riga di comando dello strumento di copia di Azure (**AzCopy**) per scaricare i dati di log.  

 Per assicurarsi di che scaricare i dati di log che si è interessati ed evitare di scaricare più volte gli stessi dati di log:  

-   Usare la data e convenzione di denominazione di tempo per i BLOB contenenti dati di log per tenere traccia di quali BLOB è già stato scaricato per l'analisi evitare di scaricare nuovamente gli stessi dati più volte.  

-   Usare i metadati dei BLOB contenenti i dati di log per identificare il periodo specifico per il quale il blob contiene i dati di log per individuare esattamente il blob da scaricare.  

> [!NOTE]
>  AzCopy è parte di Azure SDK, ma è sempre possibile scaricare la versione più recente dal [ https://aka.ms/AzCopy ](https://aka.ms/AzCopy). Per impostazione predefinita, AzCopy viene installato nella cartella **C:\Program Files (x86) SDKs\Windows Azure\AzCopy**, è necessario aggiungere questa cartella al percorso prima di provare a eseguire lo strumento in un prompt dei comandi o una finestra di PowerShell.  

 Nell'esempio seguente viene illustrato come è possibile scaricare i dati di log del servizio di Accodamento per le ore a partire da 09 AM, AM 10 e 11 AM 20 maggio 2014. Il **/S** parametro fa in modo che AzCopy compilare una struttura di cartelle locali basata su date e ore nei nomi dei file di log; il **/V** parametro fa in modo che AzCopy per produrre un output dettagliato; il **/Y** parametro fa in modo che AzCopy sovrascrivere eventuali file locali. Sostituire **< yourstorageaccount\>**  con il nome dell'account di archiviazione e sostituisci **< yourstoragekey\>**  con la chiave di account di archiviazione.  

```  
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy dispone anche alcuni utili parametri che controllano come imposta l'ora dell'ultima modifica nel file scaricati e se tenterà di scaricare i file più vecchi o più recenti rispetto a tutti i file già esistenti nel computer locale. È anche possibile eseguirlo in modalità riavviabile. Per informazioni dettagliate, visualizzare la Guida eseguendo il **AzCopy /?** comando.  

 Per un esempio di come scaricare i dati di log a livello di codice, vedere il post di blog [registrazione archiviazione Windows Azure: Uso dei log per tenere traccia delle richieste di archiviazione](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) e cercare la parola "DumpLogs" nella pagina.  

 Una volta scaricati i dati dei log, è possibile visualizzare le voci di log nei file. Questi file di log usano un formato di testo delimitato che molti log la lettura di strumenti sono in grado di analizzare, incluso Microsoft Message Analyzer (per altre informazioni, vedere la Guida [monitoraggio, diagnosi e risoluzione dei problemi di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)). Altri strumenti dispongono di funzionalità differenti per la formattazione, filtraggio, ordinamento e la ricerca dei contenuti dei file di log. Per altre informazioni sulla registrazione archiviazione formato file di log e sul contenuto, vedere [il formato di Log di archiviazione Analitica](/rest/api/storageservices/storage-analytics-log-format) e [archiviazione Analitica registrazione minima delle operazioni e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Passaggi successivi
* [Formato log Analisi archiviazione](/rest/api/storageservices/storage-analytics-log-format)
* [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metriche di Analitica di archiviazione (versione classica)](storage-analytics-metrics.md)
