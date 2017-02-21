---
title: Abilitazione delle metriche di archiviazione nel Portale di Azure | Microsoft Docs
description: Come abilitare le metriche di archiviazione per i servizi BLOB, di accodamento, di tabelle e file
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/03/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: c3519943bf60aebc8510b7a7ac577bbe04587bd8
ms.openlocfilehash: 05f4d8d6e9155f163ef44ef189067427ac606f46


---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Abilitazione di Metriche di archiviazione di Azure e visualizzazione dei dati delle metriche
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Panoramica
Per impostazione predefinita, Metriche di archiviazione non è abilitato per i servizi di archiviazione. È possibile abilitare il monitoraggio tramite il [portale di Azure](https://portal.azure.com) o Windows PowerShell o a livello di codice con la libreria del client di archiviazione.

Quando si abilita Metriche di archiviazione, è necessario scegliere un periodo di memorizzazione per i dati: questo periodo determina per quanto tempo il servizio di archiviazione mantiene le metriche e addebita all'utente lo spazio necessario per archiviarle. In genere, è consigliabile usare un periodo di memorizzazione per le metriche al minuto più breve che per le metriche orarie, a causa dello spazio supplementare significativo necessario per le metriche al minuto. È consigliabile scegliere un periodo di memorizzazione tale da avere tempo sufficiente per analizzare i dati e scaricare le metriche da mantenere per l'analisi non in linea o la creazione di report. Tenere presente che verrà addebitato anche il download dei dati di metrica dall'account di archiviazione.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Come abilitare le metriche usando il portale di Azure
Seguire questi passaggi per abilitare le metriche nel [portale di Azure](https://portal.azure.com):

1. Passare all'account di archiviazione.
1. Selezionare **Diagnostica** nel pannello **Menu**
1. Assicurarsi che **Stato** sia impostato su **On**.
1. Selezionare le metriche per i servizi che si desidera monitorare.
1. Specificare un criterio di conservazione per indicare per quanto tempo conservare le metriche e dati di log.
1. Selezionare **Salva**.

Il [portale di Azure](https://portal.azure.com) non consente attualmente di configurare le metriche al minuto nell'account di archiviazione; è necessario abilitare le metriche al minuto usando PowerShell o a livello di codice.

## <a name="how-to-enable-metrics-using-powershell"></a>Come abilitare le metriche usando PowerShell
È possibile usare PowerShell nel computer locale per configurare Metriche di archiviazione nell'account di archiviazione usando il cmdlet di Azure PowerShell Get-AzureStorageServiceMetricsProperty per recuperare le impostazioni correnti e il cmdlet Set-AzureStorageServiceMetricsProperty per modificare le impostazioni correnti.

I cmdlet che controllano Metriche di archiviazione usano i seguenti parametri:

* I valori possibili di MetricsType sono Hour e Minute.
* I valori possibili di ServiceType sono Blob, Queue e Table.
* I valori possibili di MetricsLevel sono None, Service e ServiceAndApi.

Ad esempio, il seguente comando attiva le metriche al minuto per il servizio BLOB nell'account di archiviazione predefinito con il periodo di memorizzazione impostato su cinque giorni:

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

Il seguente comando recupera il livello delle metriche orarie corrente e i giorni di memorizzazione per il servizio BLOB nell'account di archiviazione predefinito:

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Per informazioni su come configurare i cmdlet di Azure PowerShell per usare la sottoscrizione di Azure e su come selezionare l'account di archiviazione predefinito da utilizzare, vedere: [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

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
Una volta configurate le metriche dell’analisi di archiviazione per monitorare l'account di archiviazione, l’analisi di archiviazione registra le metriche in un set di tabelle note nell'account di archiviazione. È possibile configurare i grafici per visualizzare le metriche orarie nel [portale di Azure](https://portal.azure.com):

1. Passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Metriche** nel pannello **Menu** per il servizio di cui visualizzare le metriche.
1. Selezionare **Modifica** del grafico che si vuole configurare.
1. Nel pannello **Modifica grafico** selezionare l'**intervallo di tempo**, il **tipo di grafico** e le metriche da visualizzare nel grafico.
1. Selezionare **OK**.

Se si vogliono scaricare le metriche per l'archiviazione a lungo termine o per analizzare le metriche in locale, è necessario:

* Usare uno strumento che riconosca queste tabelle e consenta di visualizzarle e scaricarle.
* Scrivere un'applicazione personalizzata o script per leggere e archiviare le tabelle.

Molti strumenti di esplorazione dell'archivio di terze parti sono compatibili con queste tabelle e consentono di visualizzarle direttamente.
Vedere [Strumenti di esplorazione degli archivi di Azure](storage-explorers.md) per un elenco di strumenti disponibili.

> [!NOTE]
> A partire dalla versione 0.8.0 di [Microsoft Azure Storage Explorer](http://storageexplorer.com/), a questo punto sarà possibile visualizzare e scaricare le tabelle della metrica di analisi.
> 
> 

Per accedere alle tabelle di analisi a livello di codice, si noti che non vengono visualizzate se si elencano tutte le tabelle presenti nell'account di archiviazione. È possibile accedervi direttamente con il nome o usare l' [APICloudAnalyticsClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) nella libreria client .NET per eseguire query sui nomi delle tabelle.

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

È consigliabile impostare gli avvisi nella pagina Monitoraggio del [portale di Azure](https://portal.azure.com) in modo che Metriche di archiviazione possa notificare automaticamente eventuali importanti modifiche nel comportamento dei servizi di archiviazione. Se si usa uno strumento di esplorazione di archiviazione per scaricare i dati di metrica in un formato delimitato, è possibile usare Microsoft Excel per analizzare i dati. Per un elenco di strumenti di esplorazione di archiviazione disponibili, vedere il post di blog [Microsoft Azure Storage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).

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

## <a name="next-steps"></a>Passaggi successivi
[Abilitazione di Registrazione archiviazione e accesso ai dati di log](https://msdn.microsoft.com/library/dn782840.aspx)


<!--HONumber=Feb17_HO1-->


