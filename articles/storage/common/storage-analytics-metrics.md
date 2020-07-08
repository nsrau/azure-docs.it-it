---
title: Metriche di Analisi archiviazione di Azure (versione classica)
description: Informazioni su come usare le metriche di Analisi archiviazione di Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 5613453667e3bb278f4da22ebed4502def70235b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83675910"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metriche di Analisi archiviazione di Azure (versione classica)

Archiviazione di Azure usa la soluzione Analisi archiviazione per archiviare le metriche che includono le statistiche delle transazioni aggregate e i dati di capacità relativi alle richieste in un servizio di archiviazione. Le transazioni vengono segnalate a livello di operazione API e a livello di servizio di archiviazione. La capacità viene segnalata a livello di servizio di archiviazione. Le metriche possono essere usate per:
- Analizzare l'utilizzo del servizio di archiviazione.
- Diagnosticare i problemi con richieste al servizio di archiviazione.
- Migliorare le prestazioni delle applicazioni che usano un servizio.

 Le metriche di Analisi archiviazione sono abilitate per impostazione predefinita per i nuovi account di archiviazione. È possibile configurare le metriche nel [portale di Azure](https://portal.azure.com/). Per altre informazioni, vedere [Monitorare un account di archiviazione nel portale di Azure](/azure/storage/storage-monitor-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Per abilitare Analisi archiviazione per ogni servizio, usare le operazioni che consentono di impostare le proprietà dei servizi.  

> [!NOTE]
> Le metriche di Analisi archiviazione sono disponibili per Archiviazione BLOB di Azure, Archiviazione code di Azure, Archiviazione tabelle di Azure e File di Azure.
> Le metriche di Analisi archiviazione sono ora metriche classiche. Si consiglia di usare [Metriche di archiviazione in Monitoraggio di Azure](monitor-storage.md) invece delle metriche di Analisi archiviazione.

## <a name="transaction-metrics"></a>Metriche di transazione  
 A intervalli di ore o minuti viene registrato un set di dati consistente per ciascun servizio di archiviazione e operazione API richiesta, inclusi ingresso e uscita, disponibilità, errori e percentuali di richieste suddivise in categorie. Un elenco completo dei dettagli delle transazioni è disponibile in [Schema di tabella della metrica di Analisi archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 I dati delle transazioni vengono registrati a livello di servizio e a livello di operazione API. A livello di servizio, le statistiche che riepilogano tutte le operazioni API richieste vengono scritte in un'entità di tabella ogni ora, persino se non sono state eseguite richieste al servizio. A livello di operazione API, le statistiche vengono scritte in un'entità solo se l'operazione è stata richiesta entro l'ora.  

 Se, ad esempio, si esegue un'operazione **GetBlob** nel servizio BLOB, la metrica Analisi archiviazione registra la richiesta e la include nei dati aggregati sia per il servizio BLOB, sia per l'operazione **GetBlob**. Se nessuna operazione **GetBlob** viene richiesta in quest'ora, non viene scritta alcuna entità in *$MetricsTransactionsBlob* per questa operazione.  

 Le metriche delle transazioni vengono registrate sia per le richieste utente, sia per quelle eseguite dalla stessa Analisi archiviazione. Ad esempio, le richieste di Analisi archiviazione di scrivere entità di log e di tabella vengono registrate.

## <a name="capacity-metrics"></a>Metriche della capacità  

> [!NOTE]
>  Al momento, le metriche per la capacità sono disponibili solo per il servizio BLOB.

 I dati relativi alla capacità vengono registrati quotidianamente per il servizio BLOB di un account di archiviazione e vengono scritte due entità di tabella. Un'entità fornisce le statistiche per i dati utente e l'altra le statistiche sul contenitore BLOB `$logs` utilizzato da Analisi archiviazione. Nella tabella *$MetricsCapacityBlob* sono contenute le statistiche seguenti:  

- **Capacity**: quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte;  
- **ContainerCount**: numero di contenitori BLOB nel servizio BLOB dell'account di archiviazione;  
- **ObjectCount**: numero di BLOB di pagine o blocchi inviati e non inviati nel servizio BLOB dell'account di archiviazione.  

  Per altre informazioni sulle metriche della capacità, vedere [Schema di tabella della metrica di Analisi archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Come vengono archiviate le metriche  

 Tutti i dati delle metriche per ognuno dei servizi di archiviazione vengono archiviati in tre tabelle riservate per il servizio. Una tabella per le informazioni sulle transazioni, una tabella per le informazioni sulle transazioni al minuto e un'altra tabella per le informazioni sulla capacità. Le informazioni sulla transazione e sulle transazioni al minuto consistono nei dati di richiesta e di risposta, mentre le informazioni sulla capacità consistono nei dati d'uso dell'archiviazione. Metriche per ora, minuto e capacità del servizio BLOB di un account di archiviazione sono accessibili nelle tabelle denominate nel modo descritto nella tabella seguente.  

|Livello metrica|Nomi tabella|Versioni supportate|  
|-------------------|-----------------|----------------------------|  
|Metriche orarie, posizione principale|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Solo versioni precedenti al 15 agosto 2013. Anche se tali nomi sono supportati, è consigliabile passare all'uso delle tabelle elencate di seguito.|  
|Metriche orarie, posizione principale|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|tutte le versioni. Il supporto per le metriche del servizio file è disponibile solo a partire dalla versione del 5 aprile 2015.|  
|Metriche per minuto, posizione principale|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|tutte le versioni. Il supporto per le metriche del servizio file è disponibile solo a partire dalla versione del 5 aprile 2015.|  
|Metriche orarie, posizione secondaria|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|tutte le versioni. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica.|  
|Metriche al minuto, posizione secondaria|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|tutte le versioni. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica.|  
|Capacità (solo servizio BLOB)|$MetricsCapacityBlob|tutte le versioni.|  

 Tali tabelle vengono create automaticamente quando viene abilitato Analisi archiviazione per un endpoint di servizio di archiviazione. L'accesso alle tabelle viene eseguito tramite lo spazio dei nomi dell'account di archiviazione, ad esempio, `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Le tabelle delle metriche non vengono visualizzate in un'operazione di elenco ed è necessario accedervi direttamente tramite il nome della tabella.

## <a name="enable-metrics-by-using-the-azure-portal"></a>Abilitare le metriche usando il portale di Azure
Seguire questi passaggi per abilitare le metriche nel [portale di Azure](https://portal.azure.com):

1. Passare all'account di archiviazione.
1. Selezionare **Impostazioni di diagnostica (versione classica)** nel riquadro del menu.
1. Assicurarsi che **Stato** sia impostato su **On**.
1. Selezionare le metriche per i servizi da monitorare.
1. Specificare un criterio di conservazione per indicare per quanto tempo conservare le metriche e dati di log.
1. Selezionare **Salva**.

Al momento il [portale di Azure](https://portal.azure.com) non consente di configurare le metriche al minuto nell'account di archiviazione. È necessario abilitarle usando PowerShell o a livello di codice.

## <a name="enable-storage-metrics-by-using-powershell"></a>Abilitare le metriche di archiviazione usando PowerShell  
È possibile usare PowerShell nel computer locale per configurare le metriche di archiviazione nell'account di archiviazione usando il cmdlet di Azure PowerShell **Get-AzStorageServiceMetricsProperty** per recuperare le impostazioni correnti. Per modificare le impostazioni correnti, usare il cmdlet **Set-AzStorageServiceMetricsProperty**.  

I cmdlet che controllano le metriche di archiviazione usano i seguenti parametri:  

* **ServiceType**: i valori possibili sono **Blob**, **Queue**, **Table** e **File**.
* **MetricsType**: i valori possibili sono **Hour** e **Minute**.  
* **MetricsLevel**: I valori possibili sono:
   * **Nessuna**: disattiva il monitoraggio.
   * **Servizio**: raccoglie metriche come ingresso e uscita, disponibilità, latenza e percentuali di successo aggregate per i servizi BLOB, Coda, Tabella e File.
   * **ServiceAndApi**: oltre alle metriche di servizio, raccoglie lo stesso set di metriche per ogni operazione di archiviazione eseguita nell'API del servizio Archiviazione di Azure.

Ad esempio, il seguente comando attiva le metriche al minuto per il servizio BLOB nell'account di archiviazione con il periodo di conservazione impostato su cinque giorni: 

> [!NOTE]
> Questo comando presuppone che sia stato effettuato l'accesso alla sottoscrizione di Azure usando il comando `Connect-AzAccount`.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Sostituire il valore segnaposto `<resource-group-name>` con il nome del proprio gruppo di risorse.      
* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.



Il seguente comando recupera il livello delle metriche orarie corrente e i giorni di memorizzazione per il servizio BLOB nell'account di archiviazione predefinito:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Per informazioni su come configurare i cmdlet di Azure PowerShell per usare la sottoscrizione di Azure e su come selezionare l'account di archiviazione predefinito da utilizzare, vedere [Installare e configurare Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Abilitare le metriche di archiviazione a livello di codice  
Oltre al portale di Azure o ai cmdlet di Azure PowerShell, per controllare le metriche di archiviazione è possibile anche usare una delle API di Archiviazione di Azure. Se, ad esempio, si usa un linguaggio .NET, è possibile usare la libreria client di Archiviazione di Azure.  

Le classi **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient** e **CloudFileClient** dispongono tutte di metodi quali **SetServiceProperties** e **SetServicePropertiesAsync** che accettano un oggetto **ServiceProperties** come parametro. È possibile usare l'oggetto **ServiceProperties** per configurare le metriche di archiviazione. Il seguente frammento di codice C#, ad esempio, mostra come modificare il livello di metrica e i giorni di conservazione per le metriche orarie della coda:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Per altre informazioni sull'uso di un linguaggio .NET per configurare le metriche di archiviazione, vedere [API di archiviazione di Azure per .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Per informazioni generali sulla configurazione delle metriche di archiviazione mediante l'API REST, vedere [Abilitazione e configurazione di Analisi archiviazione](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="view-storage-metrics"></a>Visualizzare le metriche di archiviazione  
Una volta configurate le metriche dell’analisi di archiviazione per monitorare l'account di archiviazione, l’analisi di archiviazione registra le metriche in un set di tabelle note nell'account di archiviazione. È possibile configurare i grafici per visualizzare le metriche orarie nel [portale di Azure](https://portal.azure.com):

1. Passare al proprio account di archiviazione nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Metriche (versione classica)** nel riquadro del menu per il servizio di cui si desidera visualizzare le metriche.
1. Selezionare il grafico da configurare.
1. Nel riquadro **Modifica grafico** selezionare l'**intervallo di tempo**, il **tipo di grafico** e le metriche da visualizzare nel grafico.

Nella sezione **Monitoraggio (versione classica)** del riquadro del menu dell'account di archiviazione nel portale di Azure, è possibile configurare le [regole di avviso](#metrics-alerts). Ad esempio, è possibile inviare avvisi di posta elettronica da inviare quando una metrica specifica raggiunge un determinato valore.

Se si desidera scaricare le metriche per l'archiviazione a lungo termine o per analizzarle in locale, è necessario usare uno strumento o scrivere il codice per leggere le tabelle. È necessario scaricare le metriche al minuto per l'analisi. Se si elencano tutte le tabelle nell'account di archiviazione, le tabelle non vengono visualizzate, ma è possibile accedervi direttamente mediante il nome. Molti strumenti di esplorazione dell'archivio sono compatibili con queste tabelle e consentono di visualizzarle direttamente. Vedere [Strumenti client di Archiviazione di Azure](/azure/storage/storage-explorers) per un elenco di strumenti disponibili.

||||  
|-|-|-|  
|**Metriche**|**Nomi di tabella**|**Note**|  
|Metriche orarie|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Nelle versioni precedenti al 15 agosto 2013, queste tabelle sono note come:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Le metriche per il servizio File sono disponibili a partire dalla versione del 5 aprile 2015.|  
|Metriche al minuto|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Può essere abilitata solo a livello di codice o usando PowerShell.<br /><br /> Le metriche per il servizio File sono disponibili a partire dalla versione del 5 aprile 2015.|  
|Capacità|$MetricsCapacityBlob|Solo servizio BLOB.|  

I dettagli completi sugli schemi di queste tabelle sono disponibili in [Schema di tabella della metrica di Analisi archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema). Le righe di esempio seguenti mostrano solo un subset delle colonne disponibili, ma illustrano alcune importanti funzionalità relative al modo in cui le metriche di archiviazione salvano queste metriche:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Disponibilità**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Nei dati delle metriche al minuto di questo esempio, la chiave di partizione usa la risoluzione ora al minuto. La chiave di riga identifica il tipo di informazioni archiviate nella riga. È composta dal tipo di accesso e dal tipo di richiesta:  

-   Il tipo di accesso è **user** o **system**, laddove **user** si riferisce a tutte le richieste utente al servizio di archiviazione e **system** si riferisce alle richieste effettuate da Analisi archiviazione.  
-   Il tipo di richiesta può essere **all**, e in questo caso si tratta di una riga di riepilogo, o identificare l'API specifica, ad esempio **QueryEntity** o **UpdateEntity**.  

Questi dati di esempio mostrano tutti i record per un solo minuto (a partire dalle 11.00). La somma del numero di richieste **QueryEntities**, del numero di richieste **QueryEntity** e del numero di richieste **UpdateEntity** è sette. Il totale è visualizzato nella riga **user:All**. Analogamente, è possibile ricavare la latenza end-to-end media 104,4286 nella riga **user:All** calcolando ((143,8 * 5) + 3 + 9) / 7.  

## <a name="metrics-alerts"></a>Avvisi delle metriche
È consigliabile impostare gli avvisi nel [portale di Azure](https://portal.azure.com) in modo da ricevere notifiche automatiche in caso di modifiche importanti nel comportamento dei servizi di archiviazione. Se si usa uno strumento di esplorazione di archiviazione per scaricare i dati di metrica in un formato delimitato, è possibile usare Microsoft Excel per analizzare i dati. In [Strumenti client di Archiviazione di Azure](/azure/storage/storage-explorers) è presente un elenco degli strumenti di esplorazione di archiviazione disponibili. È possibile configurare gli avvisi nel riquadro **Avviso (versione classica)** , accessibile da **Monitoraggio (versione classica)** nel riquadro del menu dell'account di archiviazione.

> [!IMPORTANT]
> Potrebbe verificarsi un ritardo tra un evento di archiviazione e la memorizzazione dei relativi dati di metrica oraria o al minuto. In caso di metriche al minuto, è possibile che vengano scritti contemporaneamente diversi minuti di dati. Ciò può causare l'aggregazione di transazioni dai minuti precedenti nella transazione per il minuto corrente. Se si verifica questo problema, il servizio di avviso potrebbe non avere tutti i dati di metrica disponibili per l'intervallo di avviso configurato, il che potrebbe determinare l'attivazione imprevista degli avvisi.
>

## <a name="access-metrics-data-programmatically"></a>Accedere ai dati delle metriche a livello di codice  
Nell'elenco riportato di seguito viene illustrato il codice C# di esempio che consente l'accesso alle metriche al minuto per un intervallo di minuti. I risultati vengono visualizzati in una finestra della console. Il codice di esempio usa la libreria client di Archiviazione di Azure versione 4.x o successiva che include la classe **CloudAnalyticsClient**, in grado di semplificare l'accesso alle tabelle di metrica nell'archiviazione.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
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

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
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

## <a name="billing-on-storage-metrics"></a>Fatturazione delle metriche di archiviazione
Le richieste di scrittura per creare entità di tabella per le metriche vengono addebitate alle tariffe standard applicabili a tutte le operazioni di archiviazione di Azure.  

Anche le richieste di lettura ed eliminazione dei dati delle metriche da parte di un client sono fatturabili alle tariffe standard. Se è stato configurato un criterio di conservazione dei dati, non è necessario sostenere alcun addebito quando Archiviazione di Azure elimina i vecchi dati delle metriche. Se si eliminano dati di analisi, all'account vengono addebitate le operazioni di eliminazione.  

È fatturabile anche la capacità usata dalle tabelle delle metriche. Per stimare la quantità di capacità usata per l'archiviazione dei dati delle metriche usare le seguenti informazioni:  

-   Se ogni ora un servizio utilizza tutte le API presenti in ciascun servizio, ogni ora circa 148 kB di dati vengono archiviati nelle tabelle delle transazioni metriche, se è stato abilitato il riepilogo a livello di servizio e a livello di API.  
-   Quando si abilita solo un riepilogo a livello di servizio, se entro ogni ora un servizio utilizza tutte le API presenti in ciascun servizio, nelle tabelle delle transazioni delle metriche vengono archiviati ogni ora circa 12 kB di dati.  
-   Alla tabella della capacità relativa ai BLOB vengono aggiunte due righe ogni giorno a condizione che l'utente abbia scelto i log. Questo implica che ogni giorno le dimensioni della tabella aumentano di circa 300 byte.

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare un account di archiviazione](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schema di tabella delle metriche di Analisi archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Registrazione di Analisi archiviazione](storage-analytics-logging.md)
