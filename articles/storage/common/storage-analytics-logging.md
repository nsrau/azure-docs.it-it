---
title: Registrazione Analisi archiviazione di Azure
description: Informazioni su come registrare i dettagli delle richieste effettuate nell'archiviazione di Azure.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 3b61e8680ef2484b1ad42837711adef171fdde25
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882629"
---
# <a name="azure-storage-analytics-logging"></a>Registrazione di analisi archiviazione di Azure

Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo.

 Analisi archiviazione la registrazione non è abilitata per impostazione predefinita per l'account di archiviazione. È possibile abilitarla nel [portale di Azure](https://portal.azure.com/); per informazioni dettagliate, vedere [Monitorare un account di archiviazione nel portale di Azure](/azure/storage/storage-monitor-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Utilizzare le operazioni ottenere le proprietà del servizio [BLOB](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), ottenere le proprietà del servizio di [Accodamento](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)e [ottenere le proprietà del servizio tabelle](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) per abilitare analisi archiviazione per ogni servizio.

 Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Se ad esempio un account di archiviazione ha un'attività nell'endpoint BLOB ma non negli endpoint della tabella o della coda, verranno creati solo i log relativi al servizio BLOB.

> [!NOTE]
>  Analisi archiviazione registrazione è attualmente disponibile solo per i servizi BLOB, di Accodamento e tabelle. Tuttavia, l'account di archiviazione Premium non è supportato.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>Richieste di registrazione registrate
### <a name="logging-authenticated-requests"></a>Registrazione delle richieste autenticate

 Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite
- Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo
- Richieste con una firma di accesso condiviso o OAuth, incluse le richieste non riuscite e riuscite
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

Tutti i log vengono archiviati in BLOB in blocchi in un contenitore denominato `$logs`, che viene creato automaticamente quando Analisi archiviazione è abilitato per un account di archiviazione. Il contenitore `$logs` si trova nello spazio dei nomi BLOB dell'account di archiviazione, ad esempio: `http://<accountname>.blob.core.windows.net/$logs`. Questo contenitore non può essere eliminato una volta abilitata Analisi di archiviazione, sebbene sia possibile eliminarne il contenuto. Se si usa lo strumento di esplorazione dell'archiviazione per passare direttamente al contenitore, vengono visualizzati tutti i BLOB contenenti i dati di registrazione.

> [!NOTE]
>  Il contenitore `$logs` non viene visualizzato quando viene eseguita un'operazione di elenco dei contenitori, ad esempio l'operazione list Containers. È necessario effettuare l'accesso diretto. Ad esempio, è possibile usare l'operazione list Blobs per accedere ai BLOB nel contenitore `$logs`.

Nel momento in cui vengono registrate le richieste, Analisi archiviazione carica i risultati intermedi come blocchi. Analisi archiviazione invierà periodicamente questi blocchi e li renderà disponibili come BLOB. Potrebbe essere necessaria fino a un'ora per la visualizzazione dei dati di log nei BLOB del contenitore **$logs** perché la frequenza con cui il servizio di archiviazione Scarica i writer di log. Per i log creati nella stessa ora possono esistere record duplicati. È possibile determinare se un record è un duplicato controllandone il numero **RequestId** e **Operation**.

Se si dispone di un volume elevato di dati di log con più file per ogni ora, è possibile usare i metadati del BLOB per determinare quali dati sono contenuti nel log esaminando i campi dei metadati del BLOB. Questa operazione è utile anche in quanto a volte è possibile che si verifichi un ritardo durante la scrittura dei dati nei file di log: i metadati del BLOB forniscono un'indicazione più accurata del contenuto del BLOB rispetto al nome del BLOB.

La maggior parte degli strumenti di esplorazione dell'archiviazione consente di visualizzare i metadati dei BLOB. è anche possibile leggere queste informazioni usando PowerShell o a livello di codice. Il frammento di codice di PowerShell seguente è un esempio di filtro dell'elenco di BLOB di log in base al nome per specificare un'ora e in base ai metadati per identificare solo i log che contengono operazioni di **scrittura** .  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Per informazioni sull'elenco di BLOB a livello di codice, vedere [enumerazione di risorse BLOB](https://msdn.microsoft.com/library/azure/hh452233.aspx) e [impostazione e recupero di proprietà e metadati per le risorse BLOB](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Convenzioni di denominazione dei log

 Ogni log verrà scritto nel formato seguente:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 La tabella seguente descrive ogni attributo nel nome del log:

|Attributo|Description|
|---------------|-----------------|
|`<service-name>`|Nome del servizio di archiviazione. Ad esempio: `blob`, `table`o `queue`|
|`YYYY`|Anno a quattro cifre per il log. Ad esempio: `2011`|
|`MM`|Mese a due cifre per il log. Ad esempio: `07`|
|`DD`|Giorno a due cifre per il log. Ad esempio: `31`|
|`hh`|Ora a due cifre che indica l'ora di inizio per i log, nel formato UTC di 24 ore. Ad esempio: `18`|
|`mm`|Numero a due cifre che indica il minuto di inizio per i log. **Nota:**  Questo valore non è supportato nella versione corrente di Analisi archiviazione e il relativo valore sarà sempre `00`.|
|`<counter>`|Contatore base zero con sei cifre che indica il numero di BLOB di log generati per il servizio di archiviazione in un'ora. Questo contatore inizia a `000000`. Ad esempio: `000001`|

 Di seguito è riportato un nome di log di esempio completo che combina gli esempi precedenti:

 `blob/2011/07/31/1800/000001.log`

 Di seguito è riportato un URI di esempio che può essere usato per accedere al log precedente:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando viene registrata una richiesta di archiviazione, il nome log risultante è correlato all'ora in cui è stata completata l'operazione richiesta. Ad esempio, se una richiesta GetBlob è stata completata alle 6.00 del 7/31/2011, il log viene scritto con il prefisso seguente: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadati dei log

 Tutti i BLOB dei log vengono archiviati con i metadati che possono essere utilizzati per identificare i dati di registrazione contenuti nei BLOB. La tabella seguente descrive ogni attributo di metadati:

|Attributo|Description|
|---------------|-----------------|
|`LogType`|Descrive se il log contiene informazioni relative alle operazioni di lettura, scrittura o eliminazione. Questo valore può includere un solo tipo o una combinazione di tutti e tre, separati da virgola.<br /><br /> Esempio 1: `write`<br /><br /> Esempio 2: `read,write`<br /><br /> Esempio 3: `read,write,delete`|
|`StartTime`|La prima ora di una voce nel log, sotto forma di `YYYY-MM-DDThh:mm:ssZ`. Ad esempio: `2011-07-31T18:21:46Z`|
|`EndTime`|Ora più recente di una voce nel log, sotto forma di `YYYY-MM-DDThh:mm:ssZ`. Ad esempio: `2011-07-31T18:22:09Z`|
|`LogVersion`|Versione del formato del log.|

 L'elenco seguente Visualizza i metadati di esempio completi usando gli esempi precedenti:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Abilita registrazione archiviazione

È possibile abilitare la registrazione dell'archiviazione con portale di Azure, PowerShell e gli SDK di archiviazione.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Abilitare la registrazione dell'archiviazione usando il portale di Azure  

Nel portale di Azure usare il pannello **impostazioni di diagnostica (versione classica)** per controllare la registrazione dell'archiviazione, accessibile dalla sezione **monitoraggio (classica)** del pannello del **menu**di un account di archiviazione.

È possibile specificare i servizi di archiviazione che si desidera registrare e il periodo di memorizzazione (in giorni) per i dati registrati.  

### <a name="enable-storage-logging-using-powershell"></a>Abilitare la registrazione dell'archiviazione con PowerShell  

 È possibile usare PowerShell nel computer locale per configurare la registrazione dell'archiviazione nell'account di archiviazione usando il cmdlet Azure PowerShell **Get-AzureStorageServiceLoggingProperty** per recuperare le impostazioni correnti e il cmdlet  **Impostare-AzureStorageServiceLoggingProperty** per modificare le impostazioni correnti.  

 I cmdlet che controllano la registrazione dell'archiviazione utilizzano un parametro **LoggingOperations** che è una stringa contenente un elenco delimitato da virgole di tipi di richiesta da registrare. I tre tipi di richiesta possibili sono **Read**, **Write**e **Delete**. Per disattivare la registrazione, usare il valore **None** per il parametro **LoggingOperations** .  

 Il comando seguente attiva la registrazione per le richieste di lettura, scrittura ed eliminazione nel Servizio di accodamento nell'account di archiviazione predefinito con il periodo di conservazione impostato su cinque giorni:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Il comando seguente disattiva la registrazione per il servizio tabelle nell'account di archiviazione predefinito:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Per informazioni su come configurare i cmdlet di Azure PowerShell per usare la sottoscrizione di Azure e su come selezionare l'account di archiviazione predefinito da utilizzare, vedere: [Come installare e configurare Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Abilitare la registrazione dell'archiviazione a livello di codice  

 Oltre a usare il portale di Azure o i cmdlet Azure PowerShell per controllare la registrazione dell'archiviazione, è anche possibile usare una delle API di archiviazione di Azure. Se ad esempio si usa un linguaggio .NET, è possibile usare la libreria client di archiviazione.  

 Le classi **CloudBlobClient**, **CloudQueueClient**e **CloudTableClient** hanno tutti metodi quali **SetServiceProperties** e **SetServicePropertiesAsync** che accettano un oggetto **ServiceProperties** come parametro. È possibile usare l'oggetto **ServiceProperties** per configurare la registrazione dell'archiviazione. Ad esempio, il frammento di codice seguente C# Mostra come modificare gli elementi registrati e il periodo di memorizzazione per la registrazione della coda:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Per altre informazioni sull'uso di un linguaggio .NET per configurare la registrazione dell'archiviazione, vedere informazioni di [riferimento sulla libreria client di archiviazione](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Per informazioni generali sulla configurazione della registrazione dell'archiviazione con l'API REST, vedere [Abilitazione e configurazione di analisi archiviazione](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Scaricare i dati di log di registrazione archiviazione

 Per visualizzare e analizzare i dati di log, è necessario scaricare i BLOB contenenti i dati di log a cui si è interessati a un computer locale. Molti strumenti di esplorazione dell'archiviazione consentono di scaricare i BLOB dall'account di archiviazione. per scaricare i dati di log, è anche possibile usare il team di archiviazione di Azure fornito dallo strumento copia di Azure della riga di comando [AzCopy](storage-use-azcopy-v10.md) .  

 Per assicurarsi di scaricare i dati di log a cui si è interessati ed evitare di scaricare gli stessi dati di log più di una volta:  

-   Usare la convenzione di denominazione di data e ora per i BLOB contenenti dati di log per tenere traccia dei BLOB già scaricati per l'analisi, in modo da evitare di eseguire nuovamente il download degli stessi dati più di una volta.  

-   Usare i metadati nei BLOB contenenti dati di log per identificare il periodo specifico in cui il BLOB contiene i dati di log per identificare il BLOB esatto da scaricare.  

Per iniziare a usare AzCopy, vedere [Introduzione a AzCopy](storage-use-azcopy-v10.md) 

Nell'esempio seguente viene illustrato come è possibile scaricare i dati di log per il servizio di Accodamento per le ore a partire dalle 09, le 10.00 e le 11.00 del 20 maggio 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Per altre informazioni su come scaricare file specifici, vedere [scaricare file specifici](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

Una volta scaricati i dati di log, è possibile visualizzare le voci di log nei file. Questi file di log utilizzano un formato di testo delimitato che molti strumenti di lettura dei log sono in grado di analizzare, incluso Microsoft Message Analyzer (per ulteriori informazioni, vedere la Guida [monitoraggio, diagnosi e risoluzione dei problemi archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)). Strumenti diversi hanno diverse funzionalità per la formattazione, il filtro, l'ordinamento e la ricerca di Active Directory nei contenuti dei file di log. Per ulteriori informazioni sul formato e il contenuto del file di log di registrazione archiviazione, vedere [analisi archiviazione formato di log](/rest/api/storageservices/storage-analytics-log-format) e [analisi archiviazione operazioni registrate e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Passaggi successivi

* [Formato log Analisi archiviazione](/rest/api/storageservices/storage-analytics-log-format)
* [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metriche di Analisi archiviazione (versione classica)](storage-analytics-metrics.md)
