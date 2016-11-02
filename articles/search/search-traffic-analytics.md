<properties 
    pageTitle="Analisi del traffico di ricerca per Ricerca di Azure | Microsoft Azure" 
    description="Abilitare Analisi del traffico di ricerca per Ricerca di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure, per sbloccare informazioni dettagliate su utenti e dati." 
    services="search" 
    documentationCenter="" 
    authors="bernitorres" 
    manager="pablocas" 
    editor=""
/>

<tags 
    ms.service="search" 
    ms.devlang="multiple" 
    ms.workload="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/23/2016" 
    ms.author="betorres"
/>



# <a name="enabling-and-using-search-traffic-analytics"></a>Abilitazione e uso di Analisi del traffico di ricerca

Analisi del traffico di ricerca è una funzionalità di Ricerca di Azure che consente di ottenere visibilità nel servizio di ricerca e informazioni dettagliate sugli utenti e il relativo comportamento. Quando si abilita questa funzionalità, i dati del servizio di ricerca vengono copiati in un account di archiviazione di propria scelta. Questi dati includono i registri del servizio di ricerca e le metriche operative aggregate che è possibile elaborare e manipolare per analisi aggiuntive.

## <a name="how-to-enable-search-traffic-analytics"></a>Come abilitare Analisi del traffico di ricerca

È necessario disporre di un account di archiviazione nella stessa area e sottoscrizione del servizio di ricerca.

> [AZURE.IMPORTANT] Per questo account di archiviazione si applicano le tariffe standard.

È possibile abilitare l'analisi del traffico di ricerca nel portale o tramite PowerShell. Dopo averlo abilitato, i dati iniziano a passare nell'account di archiviazione entro 5-10 minuti, nei due contenitori BLOB seguenti:

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


### <a name="a.-using-the-portal"></a>A. Tramite il portale
Aprire il servizio Ricerca di Azure nel [portale di Azure](http://portal.azure.com). In Impostazioni trovare l'opzione Analisi del traffico di ricerca. 

![][1]

Impostare lo stato su **Sì**, selezionare l'account di archiviazione di Azure e scegliere i dati che si vuole copiare: log, metriche o entrambi. È consigliabile copiare sia i log che le metriche. È possibile impostare i criteri di conservazione per i dati su un valore compreso tra 1 e 365 giorni. Se non si vogliono conservare i dati a tempo indeterminato, impostare Conservazione (giorni) su 0.

![][2]

### <a name="b.-using-powershell"></a>B. Tramite PowerShell

Verificare innanzitutto che sia installata la versione più recente dei [cmdlet di Azure PowerShell](https://github.com/Azure/azure-powershell/releases) .

Cercare quindi gli ID della risorsa per il servizio di ricerca e l'account di archiviazione. È possibile trovarli nel portale in Impostazioni -> Proprietà -> ResourceId.

![][3]

```PowerShell
Login-AzureRmAccount
$SearchServiceResourceId = "Your Search service resource id"
$StorageAccountResourceId = "Your Storage account resource id"
Set-AzureRmDiagnosticSetting -ResourceId $SearchServiceResourceId StorageAccountId $StorageAccountResourceId -Enabled $true
```

## <a name="understanding-the-data"></a>Informazioni sui dati

I dati vengono archiviati nei BLOB di Archiviazione di Azure in formato JSON.

È presente un BLOB ogni ora per ciascun contenitore.
  
Percorso di esempio: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### <a name="logs"></a>Log

I BLOB dei log contengono i log di traffico del servizio di ricerca.
Ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti di log.
Ogni BLOB contiene record su tutte le operazioni eseguite nell'arco della stessa ora.

####<a name="log-schema"></a>Schema del log

Nome |Tipo |Esempio |Note 
------|-----|----|-----
time |datetime |"2015-12-07T00:00:43.6872559Z" |Timestamp dell'operazione
resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |resourceId in uso
operationName |string |"Query.Search" |Nome dell'operazione
operationVersion |string |"2015-02-28"|api-version usata
category |string |"OperationLogs" |costante 
resultType |string |"Esito positivo" |Valori possibili: esito positivo o negativo 
resultSignature |int |200 |Codice risultato HTTP 
durationMS |int |50 |Durata dell'operazione in millisecondi 
properties |object |Vedere la tabella seguente |Oggetto contenente dati specifici dell'operazione

####<a name="properties-schema"></a>Schema delle proprietà

|Nome |Tipo |Esempio |Note|
|------|-----|----|-----|
|Descrizione|string |"GET /indexes('content')/docs" |Endpoint dell'operazione |
|Query |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |Parametri della query |
|Documenti |int |42 |Numero di documenti elaborati|
|IndexName |string |"testindex"|Nome dell'indice associato all'operazione |

### <a name="metrics"></a>Metrica

I BLOB delle metriche contengono i valori aggregati per il servizio di ricerca. In ogni file è presente un oggetto radice denominato **record** contenente una matrice di oggetti di metrica. Questo oggetto radice contiene metriche per ogni minuto per il quale i dati erano disponibili. 

Metriche disponibili:

- SearchLatency: tempo di cui il servizio di ricerca necessita per elaborare la query di ricerca, aggregato per minuto.
- SearchQueriesPerSecond: numero di query di ricerca ricevute al secondo, aggregato per minuto.
- ThrottledSearchQueriesPercentage: percentuale di query di ricerca che sono state limitate, aggregata per minuto.

> [AZURE.IMPORTANT] La limitazione si verifica quando viene inviato un numero eccessivo di query, esaurendo la capacità delle risorse di provisioning del servizio. È possibile aggiungere più repliche al servizio.

####<a name="metrics-schema"></a>Schema delle metriche

|Nome |Tipo |Esempio |Note|
|------|-----|----|-----|
|resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE"  |ID risorsa in uso |
|metricName |string |"Latenza" |Nome della metrica |
|time|datetime |"2015-12-07T00:00:43.6872559Z" |Timestamp dell'operazione |
|average |int |64|Valore medio degli esempi non elaborati nell'intervallo di tempo della metrica |
|minimum |int |37 |Valore minimo degli esempi non elaborati nell'intervallo di tempo della metrica |
|maximum |int |78 |Valore massimo degli esempi non elaborati nell'intervallo di tempo della metrica |
|total |int |258 |Valore totale degli esempi non elaborati nell'intervallo di tempo della metrica |
|count |int |4 |Numero degli esempi non elaborati usati per generare la metrica |
|timegrain |string |"PT1M" |Intervallo di tempo della metrica nel formato ISO 8601|

Tutte le metriche vengono segnalate in intervalli di un minuto. Ogni metrica espone i valori minimi, massimi e medi al minuto.

Per la metrica SearchQueriesPerSecond, il valore minimo è quello più basso per le query di ricerca al secondo registrato durante questo minuto. Lo stesso vale anche per il valore massimo. Il valore medio è l'aggregato nel corso dell'intero minuto. Considerare questo scenario nell'arco di un minuto: un secondo di carico elevato, che è il valore massimo per SearchQueriesPerSecond, seguito da 58 secondi di carico medio e infine da un secondo con una sola query, che è il valore minimo.

Per ThrottledSearchQueriesPercentage, i valori minimo, massimo, medio e totale corrispondono tutti allo stesso valore: la percentuale di query di ricerca che sono state limitate, dal numero totale di query di ricerca nell'arco di un minuto.

## <a name="analyzing-your-data"></a>Analisi dei dati

I dati si trovano nel proprio account di archiviazione e si consiglia di esaminarli nel modo più adatto alle proprie esigenze.

Innanzitutto, si consiglia di usare [Power BI](https://powerbi.microsoft.com) per esplorare e visualizzare i dati. È possibile connettersi facilmente all'account di Archiviazione di Azure e avviare in modo rapido l'analisi dei dati. 

#### <a name="power-bi-online"></a>Power BI online

[Pacchetto di contenuto di Power BI](https://app.powerbi.com/getdata/services/azure-search): consente di creare un dashboard di Power BI e un set di report di Power BI che mostrino automaticamente i dati e forniscano informazioni dettagliate sul servizio di ricerca. Vedere la [pagina della guida del pacchetto di contenuto](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-search/).

![][4]

#### <a name="power-bi-desktop"></a>Power BI Desktop

[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop): consente di esplorare i dati e creare visualizzazioni personalizzate dei dati. Vedere la query iniziale nella sezione seguente:

1. Aprire un nuovo report di Power BI Desktop
2. Selezionare Recupera dati -> Altro.

    ![][5]

3. Selezionare Archiviazione BLOB di Microsoft Azure e Connetti

    ![][6]

4. Immettere il nome e la chiave dell'account di archiviazione
5. Selezionare "insight-log-operationlogs" e "insights-metrics-pt1m", quindi fare clic su Modifica.
6. Quando l'editor di query si apre, verificare che a sinistra sia selezionato "insight-log-operationlogs". A questo punto aprire l'editor avanzato selezionando Visualizza -> Editor avanzato

    ![][7]

7. Mantenere le prime due righe e sostituire la parte restante con la query seguente:

    >     #"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
    >     #"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
    >     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
    >     #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
    >     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
    >     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
    >     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
    >     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
    >     #"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
    >     #"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
    >     #"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
    >     #"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
    >     #"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
    >     #"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
    >     #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
    >     #"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
    >     #"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
    >     #"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
    >     #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
    >     #"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
    >     #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
    >     #"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
    >     #"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
    >     #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
    >     in
    >     #"Changed Type2"

8. Fare clic su Done.

9. Selezionare ora "insights-metrics-pt1m" dall’elenco di query sulla sinistra, quindi aprire nuovamente l'Editor avanzato. Mantenere le prime due righe e sostituire la parte restante con la query seguente: 

    >     #"insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
    >     #"Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
    >     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
        #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
    >     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
    >     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
    >     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
    >     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
    >     #"Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
    >     #"Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
    >     #"Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
    >     #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
    >         Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
    >     #"Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
    >     #"Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
    >     in
        #"Inserted Date"

10. Fare clic su Fine e quindi selezionare Chiudi e applica nella scheda Home.

11. I dati degli ultimi 30 giorni sono ora pronti per essere usati. Proseguire e creare alcune [visualizzazioni](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-report-view/).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla sintassi di ricerca e sui parametri di query. Per informazioni dettagliate, vedere [Eseguire ricerche nei documenti (API REST per Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) .

Altre informazioni sulla creazione di report utili Per informazioni dettagliate, vedere [Introduzione a Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/).

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/ResourceId.png
[4]: ./media/search-traffic-analytics/Dashboard.png
[5]: ./media/search-traffic-analytics/GetData.png
[6]: ./media/search-traffic-analytics/BlobStorage.png
[7]: ./media/search-traffic-analytics/QueryEditor.png




<!--HONumber=Oct16_HO2-->


