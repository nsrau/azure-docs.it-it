---
title: Abilitazione delle metriche di archiviazione nel Portale di Azure | Microsoft Docs
description: Come abilitare le metriche di archiviazione per i servizi BLOB, di accodamento, di tabelle e file
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 2fb5b229-f099-4334-92be-4e0e7dd257d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: b37b71888dc823f1de0ccc773c7402dbaf6ddc7b
ms.lasthandoff: 04/27/2017


---
# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>Abilitazione di Metriche di archiviazione e visualizzazione dei dati di metrica
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Panoramica
Le metriche di archiviazione vengono abilitate per impostazione predefinita quando si crea un nuovo account di archiviazione. È possibile configurare il monitoraggio usando il [portale di Azure classico](https://manage.windowsazure.com) o Windows PowerShell. In alternativa, è possibile abilitarlo in modo programmatico tramite un'API di archiviazione.

Quando si abilita Metriche di archiviazione, è necessario scegliere un periodo di memorizzazione per i dati: questo periodo determina per quanto tempo il servizio di archiviazione mantiene le metriche e addebita all'utente lo spazio necessario per archiviarle. In genere, è consigliabile usare un periodo di memorizzazione per le metriche al minuto più breve che per le metriche orarie, a causa dello spazio supplementare significativo necessario per le metriche al minuto. È consigliabile scegliere un periodo di memorizzazione tale da avere tempo sufficiente per analizzare i dati e scaricare le metriche da mantenere per l'analisi non in linea o la creazione di report. Tenere presente che verrà addebitato anche il download dei dati di metrica dall'account di archiviazione.

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-portal"></a>Come abilitare Metriche di archiviazione usando il portale di Azure classico
Nel [portale di Azure classico](https://manage.windowsazure.com), usare la pagina Configura per fare in modo che un account di archiviazione controlli le metriche di archiviazione. Per il monitoraggio, è possibile impostare un livello e un periodo di memorizzazione in giorni per ciascun BLOB, tabella e coda. In ciascun caso, il livello è uno dei seguenti:

* Disattivato: non vengono raccolte metriche.
* Minimo: Metriche di archiviazione raccoglie un set base di metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo, aggregate per i servizi BLOB, di tabelle e di accodamento.
* Dettagliato: Metriche di archiviazione raccoglie un set completo di metriche che include le stesse metriche per ciascuna operazione API di archiviazione, oltre alle metriche a livello di servizio. Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione.

Si noti che il portale di Azure classico attualmente non consente di configurare le metriche al minuto nell'account di archiviazione; è necessario abilitare le metriche al minuto usando PowerShell o a livello di codice.

## <a name="how-to-enable-storage-metrics-using-powershell"></a>Come abilitare Metriche di archiviazione usando PowerShell
È possibile usare PowerShell nel computer locale per configurare Metriche di archiviazione nell'account di archiviazione usando il cmdlet di Azure PowerShell Get-AzureStorageServiceMetricsProperty per recuperare le impostazioni correnti e il cmdlet Set-AzureStorageServiceMetricsProperty per modificare le impostazioni correnti.

I cmdlet che controllano Metriche di archiviazione usano i seguenti parametri:

* I valori possibili di MetricsType sono Hour e Minute.
* I valori possibili di ServiceType sono Blob, Queue e Table.
* I valori possibili di MetricsLevel sono None (equivalente a Disattivato nel portale di Azure classico), Service (equivalente a Minimo nel portale di Azure classico) e ServiceAndApi (equivalente a Dettagliato nel portale di Azure classico).

Ad esempio, il seguente comando attiva le metriche al minuto per il servizio BLOB nell'account di archiviazione predefinito con il periodo di memorizzazione impostato su cinque giorni:

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5
```
Il seguente comando recupera il livello delle metriche orarie corrente e i giorni di memorizzazione per il servizio BLOB nell'account di archiviazione predefinito:

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```
Per informazioni su come configurare i cmdlet di Azure PowerShell per usare la sottoscrizione di Azure e su come selezionare l'account di archiviazione predefinito da utilizzare, vedere: [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Come abilitare Metriche di archiviazione a livello di codice
Il frammento C# seguente illustra come abilitare le metriche e la registrazione per il servizio BLOB con la libreria del client di archiviazione per .NET:

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days. 
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Visualizzazione di Metriche di archiviazione
Una volta configurato Metriche di archiviazione per monitorare l'account di archiviazione, le metriche vengono registrate in un set di tabelle note nell'account di archiviazione. È possibile usare la pagina Monitoraggio relativa all'account di archiviazione nel portale di Azure classico per visualizzare in un grafico le metriche orarie non appena risultano disponibili. In questa pagina del portale di Azure classico è possibile:

* Selezionare le metriche da tracciare nel grafico (la scelta delle metriche disponibili dipende dal fatto che sia stato scelto il monitoraggio dettagliato o minimo per il servizio nella pagina Configura).
* Selezionare l'intervallo di tempo per le metriche visualizzate nel grafico.
* Scegliere di usare una scala assoluta o relativa per tracciare le metriche.
* Configurare gli avvisi di posta elettronica per inviare una notifica all'utente quando una metrica specifica raggiunge un determinato valore.

Se si desidera scaricare le metriche per l'archiviazione a lungo termine o per analizzare le metriche in locale, è necessario usare uno strumento o scrivere il codice per leggere le tabelle. È necessario scaricare le metriche al minuto per l'analisi. Se si elencano tutte le tabelle nell'account di archiviazione, le tabelle non vengono visualizzate, ma è possibile accedervi direttamente mediante il nome. Molti strumenti di esplorazione di archiviazione di terze parti sono compatibili con queste tabelle e consentono di visualizzarle direttamente (per un elenco di strumenti disponibili, vedere il post di blog [Microsoft Azure Storage Explorer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) ).

### <a name="hourly-metrics"></a>Metriche orarie
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Metriche al minuto
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacità
* $MetricsCapacityBlob

È possibile trovare i dettagli completi degli schemi di queste tabelle in [Schema di tabella della metrica di Analisi di archiviazione](https://msdn.microsoft.com/library/azure/hh343264.aspx). Le righe di esempio riportate di seguito mostrano solo un subset delle colonne disponibili, ma illustrano alcune importanti funzionalità relative al modo in cui Metriche di archiviazione salva queste metriche:

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilità | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |user;QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

Nei dati delle metriche al minuto di questo esempio, la chiave di partizione usa la risoluzione ora al minuto. La chiave di riga identifica il tipo di informazioni archiviate nella riga ed è composta da due informazioni, il tipo di accesso e il tipo di richiesta:

* Il tipo di accesso è user o system, laddove user si riferisce a tutte le richieste utente al servizio di archiviazione e system si riferisce alle richieste effettuate da Analisi archiviazione.
* Il tipo di richiesta può essere all, e in questo caso si tratta di una riga di riepilogo, o identificare l'API specifica, ad esempio QueryEntity o UpdateEntity.

I dati di esempio sopra riportati mostrano tutti i record per un solo minuto (a partire dalle 11.00). La somma del numero di richieste QueryEntities, del numero di richieste QueryEntity e del numero di richieste UpdateEntity è sette, che corrisponde al totale visualizzato nella riga user:All. Analogamente, è possibile ricavare la latenza end-to-end media 104,4286 nella riga user:All calcolando ((143,8 * 5) + 3 + 9) / 7.

È consigliabile impostare gli avvisi nel portale di Azure classico nella pagina Monitoraggio in modo che Metriche di archiviazione possa notificare automaticamente eventuali importanti modifiche nel comportamento dei servizi di archiviazione. Se si usa uno strumento di esplorazione di archiviazione per scaricare i dati di metrica in un formato delimitato, è possibile usare Microsoft Excel per analizzare i dati. Per un elenco di strumenti di esplorazione di archiviazione disponibili, vedere il post di blog [Microsoft Azure Storage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).

## <a name="accessing-metrics-data-programmatically"></a>Accesso ai dati di metrica a livello di codice
Nell'elenco riportato di seguito viene illustrato il codice C# che consente l'accesso alle metriche al minuto per un intervallo di minuti. Vengono inoltre visualizzati i risultati in una finestra della console. Viene utilizzata la libreria di archiviazione di Azure versione 4 che include la classe CloudAnalyticsClient, in grado di semplificare l'accesso alle tabelle di metrica nell'archiviazione.

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
        // (StartsWith is not supported using LINQ with Azure table storage)
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

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Quali addebiti è necessario sostenere quando si abilitano le metriche di archiviazione?
Le richieste di scrittura per creare entità di tabella per le metriche vengono addebitate alle tariffe standard applicabili a tutte le operazioni di archiviazione di Azure.

Anche le richieste di lettura ed eliminazione da parte di un client relative ai dati di metrica sono fatturabili alle tariffe standard. Se è stato configurato un criterio di memorizzazione dati, non è necessario sostenere alcun addebito quando Archiviazione di Azure elimina i vecchi dati di metrica. Tuttavia, se si eliminano dati di analisi, all'account vengono addebitate le operazioni di eliminazione.

Anche la capacità usata dalle tabelle di metrica è fatturabile: è possibile usare le seguenti informazioni per calcolare la quantità di capacità usata per l'archiviazione dei dati di metrica:

* Se ogni ora un servizio utilizza tutte le API presenti in ciascun servizio, ogni ora circa 148 KB di dati vengono archiviati nelle tabelle delle transazioni metriche, se è stato abilitato il riepilogo a livello di servizio e di API.
* Se ogni ora un servizio utilizza tutte le API presenti in ciascun servizio, ogni ora circa 12 KB di dati vengono archiviati nelle tabelle delle transazioni metriche, se è stato abilitato solo il riepilogo a livello di servizio.
* La tabella di capacità per i BLOB contiene due righe aggiunte ogni giorno (se l'utente ha optato per i log): di conseguenza, ogni giorno la dimensione della tabella aumenta di circa 300 byte.

## <a name="next-steps"></a>Passaggi successivi:
[Abilitazione della registrazione di Analisi archiviazione e accesso ai dati di log](https://msdn.microsoft.com/library/dn782840.aspx)

