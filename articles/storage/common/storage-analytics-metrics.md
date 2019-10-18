---
title: Metriche di analisi archiviazione di Azure (versione classica)
description: Informazioni su come usare le metriche in archiviazione di Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514789"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metriche di analisi archiviazione di Azure (versione classica)

Analisi archiviazione è in grado di archiviare le metriche che includono le statistiche delle transazioni aggregate e i dati di capacità relativi alle richieste in un servizio di archiviazione. Le transazioni vengono segnalate sia a livello di operazione API, sia a livello di servizio di archiviazione, mentre la capacità viene segnalata a livello di servizio di archiviazione. I dati delle metriche possono essere utilizzati per analizzare l'uso del servizio di archiviazione, diagnosticare i problemi relativi alle richieste effettuate al servizio di archiviazione e per migliorare le prestazioni delle applicazioni che usano un servizio.  

 Le metriche di Analisi archiviazione sono abilitate per impostazione predefinita per i nuovi account di archiviazione. È possibile configurare le metriche nell' [portale di Azure](https://portal.azure.com/); per informazioni dettagliate, vedere [monitorare un account di archiviazione nell'portale di Azure](/azure/storage/storage-monitor-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Utilizzare le operazioni di impostazione delle proprietà del servizio per abilitare Analisi archiviazione per ogni servizio.  

> [!NOTE]
> Sono disponibili metriche di Analisi archiviazione per i servizi BLOB, di Accodamento, tabelle e file.
> Analisi archiviazione metrica sono ora metriche classiche. Microsoft consiglia di usare le [metriche di archiviazione in monitoraggio di Azure](storage-metrics-in-azure-monitor.md) invece di analisi archiviazione metrica.

## <a name="transaction-metrics"></a>Metriche di transazione  
 A intervalli di ore o minuti viene registrato un set di dati consistente per ciascun servizio di archiviazione e operazione API richiesta, inclusi ingresso/uscita, disponibilità, errori e vengono suddivise in categorie le percentuali di richieste. È possibile vedere un elenco completo dei dettagli delle transazioni nell'argomento [Schema di tabella della metrica di Analisi di archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema) .  

 I dati delle transazioni vengono registrati a due livelli: a livello di servizio e di operazione API. A livello di servizio, le statistiche che riepilogano tutte le operazioni API richieste vengono scritte in un'entità di tabella ogni ora, persino se non sono state eseguite richieste al servizio. A livello di operazione API, le statistiche vengono scritte in un'entità solo se l'operazione è stata richiesta entro l'ora.  

 Se, ad esempio, si esegue un'operazione **GetBlob** nel servizio BLOB, la metrica Analisi archiviazione registrerà la richiesta e la includerà nei dati aggregati sia per il servizio BLOB, sia per l'operazione **GetBlob**. Tuttavia, se durante l'ora non viene richiesta alcuna operazione **GetBlob** , un'entità non verrà scritta nel *$MetricsTransactionsBlob* per quell'operazione.  

 Le metriche delle transazioni vengono registrate sia per le richieste utente, sia per quelle eseguite dalla stessa Analisi archiviazione. Ad esempio, le richieste di Analisi archiviazione di scrivere entità di log e di tabella vengono registrate.

## <a name="capacity-metrics"></a>Metriche della capacità  

> [!NOTE]
>  Al momento, le metriche per la capacità sono disponibili solo per il servizio BLOB.

 I dati relativi alla capacità vengono registrati quotidianamente per il servizio BLOB di un account di archiviazione e vengono scritte due entità di tabella. Un'entità fornisce le statistiche per i dati utente e l'altra le statistiche sul contenitore BLOB `$logs` utilizzato da Analisi archiviazione. La tabella *$MetricsCapacityBlob* include le statistiche seguenti:  

- **Capacity**: la quantità di archiviazione utilizzata dal servizio BLOB dell'account di archiviazione, in byte.  
- **ContainerCount**: il numero di contenitori BLOB del servizio BLOB dell'account di archiviazione.  
- **ObjectCount**: il numero di BLOB di pagine o blocchi inviati nel servizio BLOB dell'account di archiviazione.  

  Per altre informazioni sulle metriche della capacità, vedere [Schema di tabella della metrica di Analisi di archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Come vengono archiviate le metriche  

 Tutti i dati delle metriche per ciascun servizio di archiviazione vengono archiviati in tre tabelle riservate per tale servizio: una tabella per le informazioni sulle transazioni, una per le informazioni sulle transazioni al minuto e un'altra per le informazioni relative alla capacità. Le informazioni sulla transazione e sulle transazioni al minuto consistono nei dati di richiesta e di risposta, mentre le informazioni sulla capacità consistono nei dati d'uso dell'archiviazione. Le metriche orarie, le metriche al minuto e la capacità per il servizio BLOB di un account di archiviazione sono accessibili nelle tabelle denominate come descritto nella tabella seguente.  

|Livello metrica|Nomi di tabella|Supportato per le versioni|  
|-------------------|-----------------|----------------------------|  
|Metriche orarie, posizione principale|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Solo le versioni precedenti al 15-08-2013. Sebbene tali nomi siano supportati, è consigliabile passare all'utilizzo delle tabelle elencate di seguito.|  
|Metriche orarie, posizione principale|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Tutte le versioni. Il supporto per le metriche del servizio file è disponibile solo nella versione 2015-04-05 e successive.|  
|Metriche per minuto, posizione principale|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Tutte le versioni. Il supporto per le metriche del servizio file è disponibile solo nella versione 2015-04-05 e successive.|  
|Metriche orarie, posizione secondaria|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Tutte le versioni. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica.|  
|Metriche al minuto, posizione secondaria|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Tutte le versioni. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica.|  
|Capacità (solo servizio BLOB)|$MetricsCapacityBlob|Tutte le versioni.|  

 Queste tabelle vengono create automaticamente quando Analisi archiviazione è abilitato per un endpoint del servizio di archiviazione. È possibile accedervi tramite lo spazio dei nomi dell'account di archiviazione, ad esempio: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Le tabelle delle metriche non vengono visualizzate in un'operazione di elenco ed è necessario accedervi direttamente tramite il nome della tabella.  

## <a name="enable-metrics-using-the-azure-portal"></a>Abilitare le metriche usando il portale di Azure
Seguire questi passaggi per abilitare le metriche nel [portale di Azure](https://portal.azure.com):

1. Passare all'account di archiviazione.
1. Nel riquadro dei **menu** Selezionare **impostazioni di diagnostica (versione classica)** .
1. Assicurarsi che **Stato** sia impostato su **On**.
1. Selezionare le metriche per i servizi che si desidera monitorare.
1. Specificare un criterio di conservazione per indicare per quanto tempo conservare le metriche e dati di log.
1. Selezionare **Salva**.

Il [portale di Azure](https://portal.azure.com) non consente attualmente di configurare le metriche di minuti nell'account di archiviazione; è necessario abilitare le metriche di minuti usando PowerShell o a livello di codice.

## <a name="enable-storage-metrics-using-powershell"></a>Abilitare le metriche di archiviazione con PowerShell  
È possibile usare PowerShell nel computer locale per configurare le metriche di archiviazione nell'account di archiviazione usando il cmdlet Azure PowerShell **Get-AzStorageServiceMetricsProperty** per recuperare le impostazioni correnti e il cmdlet  **Impostare-AzStorageServiceMetricsProperty** per modificare le impostazioni correnti.  

I cmdlet che controllano Metriche di archiviazione usano i seguenti parametri:  

* **ServiceType**, valore possibile: **BLOB**, **Code**, **tabelle**e **file**.
* **MetricsType**, i valori possibili sono **hour** e **minute**.  
* **MetricsLevel**, i valori possibili sono:
* **None**: disattiva il monitoraggio.
* **Servizio**: raccoglie metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo, aggregate per i servizi BLOB, di Accodamento, tabelle e file.
* **ServiceAndApi**: oltre alle metriche del servizio, raccoglie lo stesso set di metriche per ogni operazione di archiviazione nell'API del servizio di archiviazione di Azure.

Ad esempio, il comando seguente attiva le metriche al minuto per il servizio BLOB nell'account di archiviazione con il periodo di memorizzazione impostato su cinque giorni: 

> [!NOTE]
> Questo comando presuppone che sia stato effettuato l'accesso alla sottoscrizione di Azure usando il comando `Connect-AzAccount`.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Sostituire il valore del segnaposto `<resource-group-name>` con il nome del gruppo di risorse.
        
* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.



Il seguente comando recupera il livello delle metriche orarie corrente e i giorni di memorizzazione per il servizio BLOB nell'account di archiviazione predefinito:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Per informazioni su come configurare i cmdlet di Azure PowerShell per usare la sottoscrizione di Azure e su come selezionare l'account di archiviazione predefinito da utilizzare, vedere: [Come installare e configurare Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Abilitare le metriche di archiviazione a livello di codice  
Oltre a usare il portale di Azure o i cmdlet Azure PowerShell per controllare le metriche di archiviazione, è anche possibile usare una delle API di archiviazione di Azure. Se ad esempio si usa un linguaggio .NET, è possibile usare la libreria client di archiviazione.  

Le classi **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**e **CloudFileClient** hanno tutti metodi come **SetServiceProperties** e **SetServicePropertiesAsync** che accettano  **Oggetto ServiceProperties** come parametro. È possibile usare l'oggetto **ServiceProperties** per configurare le metriche di archiviazione. Ad esempio, il frammento di codice seguente C# Mostra come modificare il livello di metrica e i giorni di conservazione per le metriche della coda oraria:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Per altre informazioni sull'uso di un linguaggio .NET per configurare le metriche di archiviazione, vedere [libreria client di archiviazione per .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Per informazioni generali sulla configurazione di metriche di archiviazione tramite l'API REST, vedere [Abilitazione e configurazione di analisi archiviazione](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Visualizzazione di Metriche di archiviazione  
Una volta configurate le metriche dell’analisi di archiviazione per monitorare l'account di archiviazione, l’analisi di archiviazione registra le metriche in un set di tabelle note nell'account di archiviazione. È possibile configurare i grafici per visualizzare le metriche orarie nel [portale di Azure](https://portal.azure.com):

1. Passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com).
1. Selezionare **metrica (classica)** nel pannello **menu** per il servizio di cui si desidera visualizzare le metriche.
1. Fare clic sul grafico che si desidera configurare.
1. Nel pannello **Modifica grafico** selezionare l'**intervallo di tempo**, il **tipo di grafico** e le metriche da visualizzare nel grafico.

Nella sezione **monitoraggio (classica)** del pannello del menu dell'account di archiviazione nell'portale di Azure è possibile configurare [le regole di avviso](#metrics-alerts), ad esempio l'invio di avvisi di posta elettronica per ricevere una notifica quando una metrica specifica raggiunge un determinato valore.

Se si desidera scaricare le metriche per l'archiviazione a lungo termine o per analizzarle localmente, è necessario utilizzare uno strumento o scrivere codice per leggere le tabelle. È necessario scaricare le metriche al minuto per l'analisi. Se si elencano tutte le tabelle nell'account di archiviazione, le tabelle non vengono visualizzate, ma è possibile accedervi direttamente mediante il nome. Molti strumenti di esplorazione dell'archiviazione sono a conoscenza di queste tabelle e consentono di visualizzarli direttamente (vedere [strumenti client di archiviazione di Azure](/azure/storage/storage-explorers) per un elenco degli strumenti disponibili).

||||  
|-|-|-|  
|**Metriche**|**Nomi di tabella**|**Note**|  
|Metriche orarie|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Nelle versioni precedenti alla 2013-08-15, queste tabelle erano note come:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Le metriche per il servizio file sono disponibili a partire dalla versione 2015-04-05.|  
|Metriche al minuto|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Può essere abilitato solo usando PowerShell o a livello di codice.<br /><br /> Le metriche per il servizio file sono disponibili a partire dalla versione 2015-04-05.|  
|Capacity|$MetricsCapacityBlob|Solo servizio BLOB.|  

È possibile trovare i dettagli completi degli schemi di queste tabelle in [Schema di tabella della metrica di Analisi di archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema). Le righe di esempio riportate di seguito mostrano solo un subset delle colonne disponibili, ma illustrano alcune importanti funzionalità relative al modo in cui Metriche di archiviazione salva queste metriche:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Disponibilità**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

Nei dati delle metriche al minuto di questo esempio, la chiave di partizione usa la risoluzione ora al minuto. La chiave di riga identifica il tipo di informazioni archiviate nella riga ed è composta da due informazioni, il tipo di accesso e il tipo di richiesta:  

-   Il tipo di accesso è **User** o **System**, in cui l' **utente** fa riferimento a tutte le richieste utente al servizio di archiviazione e **System** si riferisce alle richieste effettuate da analisi archiviazione.  

-   Il tipo di richiesta è **All** , nel qual caso si tratta di una linea di riepilogo, oppure identifica l'API specifica, ad esempio **QueryEntity** o **UpdateEntity**.  

I dati di esempio riportati sopra mostrano tutti i record per un singolo minuto (a partire dalle 11.00), quindi il numero di richieste **QueryEntities** più il numero di richieste **QueryEntity** più il numero di richieste **UpdateEntity** che aggiungono fino a sette, ovvero totale visualizzato nell' **utente: tutte** le righe. Analogamente, è possibile derivare la latenza end-to-end media 104,4286 sull' **utente: tutte** le righe calcolando ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Avvisi delle metriche
È consigliabile configurare gli avvisi nel [portale di Azure](https://portal.azure.com) in modo da ricevere automaticamente notifiche relative a modifiche importanti nel comportamento dei servizi di archiviazione. Se si usa uno strumento di esplorazione di archiviazione per scaricare i dati di metrica in un formato delimitato, è possibile usare Microsoft Excel per analizzare i dati. Vedere [Strumento client di Archiviazione di Azure](/azure/storage/storage-explorers) per un elenco di strumenti di esplorazione di archiviazione disponibili. È possibile configurare gli avvisi nel pannello **avviso (classico)** , accessibile da **monitoraggio (classico)** nel pannello del menu dell'account di archiviazione.

> [!IMPORTANT]
> Potrebbe verificarsi un ritardo tra un evento di archiviazione e la memorizzazione dei relativi dati di metrica oraria o al minuto. In caso di metriche al minuto, è possibile che vengano scritti contemporaneamente diversi dati. Ciò può comportare l'aggregazione di transazioni dai minuti precedenti nella transazione per il minuto corrente. In questo caso il servizio avvisi potrebbe non avere tutti i dati di metrica a disposizione per l'intervallo di avviso configurato, il che potrebbe determinare l'attivazione imprevista degli avvisi.
>

## <a name="accessing-metrics-data-programmatically"></a>Accesso ai dati di metrica a livello di codice  
Nell'elenco riportato di seguito viene illustrato il codice C# che consente l'accesso alle metriche al minuto per un intervallo di minuti. Vengono inoltre visualizzati i risultati in una finestra della console. L'esempio di codice usa la libreria client di archiviazione di Azure versione 4. x o successiva, che include la classe **CloudAnalyticsClient** che semplifica l'accesso alle tabelle di metrica nell'archiviazione.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>Fatturazione per le metriche di archiviazione  
Le richieste di scrittura per creare entità di tabella per le metriche vengono addebitate alle tariffe standard applicabili a tutte le operazioni di archiviazione di Azure.  

Anche le richieste di lettura ed eliminazione dei dati di metrica da un client vengono fatturate in base alle tariffe standard. Se è stato configurato un criterio di memorizzazione dati, non è necessario sostenere alcun addebito quando Archiviazione di Azure elimina i vecchi dati di metrica. Tuttavia, se si eliminano dati di analisi, all'account vengono addebitate le operazioni di eliminazione.  

Anche la capacità usata dalle tabelle di metrica è fatturabile. Per stimare la quantità di capacità usata per l'archiviazione dei dati di metrica, è possibile usare il codice seguente:  

-   Se ogni ora un servizio utilizza ogni API in ogni servizio, circa 148 KB di dati vengono archiviati ogni ora nelle tabelle delle transazioni di metrica se è stato abilitato il riepilogo a livello di servizio e di API.  
-   Se entro ogni ora un servizio utilizza tutte le API nel servizio, circa 12 KB di dati vengono archiviati ogni ora nelle tabelle delle transazioni di metrica se è stato abilitato solo il riepilogo a livello di servizio.  
-   Alla tabella Capacity per i BLOB sono state aggiunte due righe ogni giorno, purché si sia scelto di partecipare ai log. Questo implica che ogni giorno la dimensione di questa tabella aumenta di circa 300 byte.

## <a name="next-steps"></a>Passaggi successivi
* [Come monitorare un account di archiviazione](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schema di tabella della metrica di Analisi di archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Registrazione di Analisi archiviazione](storage-analytics-logging.md)
