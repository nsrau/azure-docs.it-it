---
title: Usare i contatori delle prestazioni in Diagnostica di Azure Diagnostics | Documentazione Microsoft
description: Usare i contatori delle prestazioni nei servizi cloud o nelle macchine virtuali di Azure per trovare i colli di bottiglia e ottimizzare le prestazioni.
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: fc4c61e9-d49d-4ed9-a32c-b91cdf851909
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/29/2016
ms.author: robb
ms.openlocfilehash: 2cf765cb034725199127c547a9b8b997a4a6089c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Creare e usare contatori di prestazioni in un'applicazione Azure
Questo articolo descrive i vantaggi dei contatori delle prestazioni e come inserirli nell'applicazione Azure. È possibile usarli per raccogliere dati, trovare colli di bottiglia e ottimizzare le prestazioni del sistema e dell'applicazione.

I contatori delle prestazioni disponibili per Windows Server, IIS e ASP.NET possono essere raccolti e usati anche per determinare l'integrità di ruoli Web, ruoli di lavoro e macchine virtuali di Azure. È anche possibile creare e usare contatori delle prestazioni personalizzati.  

È possibile esaminare i dati dei contatori delle prestazioni

1. Direttamente nell'host applicazione con lo strumento Performance Monitor accessibile da Desktop remoto
2. Con System Center Operations Manager con Azure Management Pack
3. Con altri strumenti di monitoraggio che accedono ai dati di diagnostica trasferiti ad Archiviazione di Azure. Per altre informazioni, vedere [Archiviare e visualizzare i dati di diagnostica nell'account di archiviazione Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .  

Per altre informazioni sul monitoraggio delle prestazioni dell'applicazione nel [portale di Azure](http://portal.azure.com/), vedere [Come monitorare i servizi cloud](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Per ulteriori indicazioni dettagliate sulla creazione di una strategia di registrazione e traccia e sull'utilizzo della diagnostica e di altre tecniche per risolvere i problemi e ottimizzare le applicazioni Azure, vedere le [procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).

## <a name="enable-performance-counter-monitoring"></a>Abilitare il monitoraggio del contatore delle prestazioni
Per impostazione predefinita, i contatori delle prestazioni non sono abilitati. L'applicazione o un'attività di avvio deve modificare la configurazione predefinita dell'agente di diagnostica in modo che includa i contatori delle prestazioni specifici da monitorare per ogni istanza del ruolo.

### <a name="performance-counters-available-for-microsoft-azure"></a>Contatori delle prestazioni disponibili per Microsoft Azure
Azure fornisce un subset dei contatori delle prestazioni disponibili per Windows Server, IIS e lo stack ASP.NET. La tabella seguente elenca alcuni contatori delle prestazioni particolarmente interessanti per le applicazioni Azure.

| Categoria contatore: oggetto (istanza) | Nome contatore | Riferimento |
| --- | --- | --- |
| Eccezioni CLR .NET (*globale*) |N. eccezioni/sec |Contatori delle prestazioni delle eccezioni |
| Memoria CLR .NET (*globale*) |Percentuale tempo in GC |Contatori delle prestazioni di memoria |
| ASP.NET |Riavvii applicazione |Contatori delle prestazioni per ASP.NET |
| ASP.NET |Tempo di esecuzione della richiesta |Contatori delle prestazioni per ASP.NET |
| ASP.NET |Richieste disconnesse |Contatori delle prestazioni per ASP.NET |
| ASP.NET |Riavvii processo di lavoro |Contatori delle prestazioni per ASP.NET |
| Applicazioni ASP.NET (**totale**) |Totale richieste |Contatori delle prestazioni per ASP.NET |
| Applicazioni ASP.NET (**totale**) |Richieste/sec |Contatori delle prestazioni per ASP.NET |
| ASP.NET v4.0.30319 |Tempo di esecuzione della richiesta |Contatori delle prestazioni per ASP.NET |
| ASP.NET v4.0.30319 |Tempo di attesa richiesta |Contatori delle prestazioni per ASP.NET |
| ASP.NET v4.0.30319 |Richieste correnti |Contatori delle prestazioni per ASP.NET |
| ASP.NET v4.0.30319 |Richieste in coda |Contatori delle prestazioni per ASP.NET |
| ASP.NET v4.0.30319 |Richieste respinte |Contatori delle prestazioni per ASP.NET |
| Memoria |MByte disponibili |Contatori delle prestazioni di memoria |
| Memoria |Byte vincolati |Contatori delle prestazioni di memoria |
| Processor(_Total) |% di tempo processore |Contatori delle prestazioni per ASP.NET |
| TCPv4 |Errori di connessione |Oggetto TCP |
| TCPv4 |Connessioni stabilite |Oggetto TCP |
| TCPv4 |Connessioni ripristinate |Oggetto TCP |
| TCPv4 |Segmenti inviati/sec |Oggetto TCP |
| Interfaccia di rete (*) |Byte ricevuti/sec |Oggetto interfaccia di rete |
| Interfaccia di rete (*) |Byte inviati/sec |Oggetto interfaccia di rete |
| Interfaccia di rete (Scheda di rete per il bus delle macchine virtuali Microsoft _2) |Byte ricevuti/sec |Oggetto interfaccia di rete |
| Interfaccia di rete (Scheda di rete per il bus delle macchine virtuali Microsoft _2) |Byte inviati/sec |Oggetto interfaccia di rete |
| Interfaccia di rete (Scheda di rete per il bus delle macchine virtuali Microsoft _2) |Totale byte/sec |Oggetto interfaccia di rete |

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Creare e aggiungere contatori delle prestazioni personalizzati all'applicazione
Azure include il supporto per creare e modificare contatori delle prestazioni personalizzati per ruoli Web e ruoli di lavoro. I contatori possono essere usati per tenere traccia del comportamento specifico dell'applicazione e per monitorarlo. È possibile creare ed eliminare categorie e identificatori dei contatori delle prestazioni personalizzati da un'attività di avvio, un ruolo Web o un ruolo di lavoro con autorizzazioni elevate.

> [!NOTE]
> Il codice che apporta le modifiche ai contatori delle prestazioni personalizzati deve avere autorizzazioni di esecuzione elevate. Se il codice è in un ruolo Web o in un ruolo di lavoro, il ruolo deve includere il tag <Runtime executionContext="elevated" /> nel file ServiceDefinition.csdef per la corretta inizializzazione del ruolo.
>
>

È possibile inviare i dati dei contatori delle prestazioni personalizzati alla risorsa di archiviazione di Azure con l'agente di diagnostica.

I dati dei contatori delle prestazioni standard vengono generati dai processi di Azure. I dati dei contatori delle prestazioni personalizzati devono essere creati dall'applicazione ruolo Web o ruolo di lavoro. Per informazioni sui tipi di dati che possono essere archiviati nei contatori delle prestazioni personalizzati, vedere [Tipi di contatori delle prestazioni](https://msdn.microsoft.com/library/z573042h.aspx) . Per un esempio di creazione e impostazione dei dati dei contatori delle prestazioni personalizzati in un ruolo Web, vedere [Esempio PerformanceCounters](http://code.msdn.microsoft.com/azure/) .

## <a name="store-and-view-performance-counter-data"></a>Archiviare e visualizzare i dati dei contatori delle prestazioni
Azure memorizza nella cache i dati dei contatori delle prestazioni con altre informazioni di diagnostica. Questi dati sono disponibili per il monitoraggio remoto mentre l'istanza del ruolo è in esecuzione con l'accesso desktop remoto per visualizzare strumenti come Performance Monitor. Per rendere i dati persistenti al di fuori dell'istanza del ruolo, l'agente di diagnostica deve trasferire i dati nell'account di archiviazione di Azure. Il limite di dimensioni dei dati dei contatori delle prestazioni memorizzati nella cache può essere configurato nell'agente di diagnostica oppure come parte di un limite condiviso per tutti i dati di diagnostica. Per altre informazioni sull'impostazione delle dimensioni del buffer, vedere [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) e [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Per una panoramica della configurazione dell'agente di diagnostica per i trasferire i dati in un account di archiviazione, vedere [Archiviare e visualizzare i dati di diagnostica nell'account di archiviazione Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx)

Ogni istanza dei contatori delle prestazioni configurati viene registrata con una frequenza di campionamento specificata e i dati campionati vengono trasferiti nell'account di archiviazione in base a una richiesta di trasferimento pianificato o a una richiesta di trasferimento su richiesta. I trasferimenti automatici possono essere pianificati con una frequenza di una volta ogni minuto. I dati dei contatori delle prestazioni trasferiti dall'agente di diagnostica vengono archiviati in una tabella, WADPerformanceCountersTable, nell'account di archiviazione. Questa tabella è accessibile e può essere sottoposta a query con i metodi API di archiviazione standard di Azure. Per un esempio di query e di visualizzazione dei dati dei contatori delle prestazioni dalla tabella WADPerformanceCountersTable, vedere l' [esempio PerformanceCounters per Microsoft Azure](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) .

> [!NOTE]
> A seconda della latenza della coda e della frequenza di trasferimento dell'agente di diagnostica, i dati dei contatori delle prestazioni più recenti nell'account di archiviazione potrebbero essere scaduti da alcuni minuti.
>
>

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Abilitare i contatori delle prestazioni con il file di configurazione della diagnostica
Usare la procedura seguente per abilitare i contatori delle prestazioni nell'applicazione Azure.

## <a name="prerequisites"></a>Prerequisiti
Questa sezione presuppone che il monitor di diagnostica sia stato importato nell'applicazione e che il file di configurazione della diagnostica sia stato aggiunto alla soluzione Visual Studio (diagnostics.wadcfg in SDK 2.4 e versioni precedenti o diagnostics.wadcfgx in SDK 2.5 e versioni successive). Per altre informazioni, vedere i passaggi 1 e 2 in [Abilitazione della diagnostica nei servizi cloud e nelle macchine virtuali di Azure](cloud-services-dotnet-diagnostics.md).

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Passaggio 1: Raccogliere e archiviare i dati dei contatori delle prestazioni
Dopo avere aggiunto il file della diagnostica alla soluzione Visual Studio, è possibile configurare la raccolta e l'archiviazione dei dati dei contatori delle prestazioni in un'applicazione Azure. Questa operazione viene eseguita tramite l'aggiunta di contatori delle prestazioni nel file della diagnostica. I dati di diagnostica, inclusi i dati dei contatori delle prestazioni, vengono innanzitutto raccolti nell'istanza e quindi salvati in modo permanente nella tabella WADPerformanceCountersTable del servizio tabelle di Azure, pertanto è necessario specificare l'account di archiviazione nell'applicazione. Se si esegue il test dell'applicazione in locale nell'emulatore di calcolo, è anche possibile archiviare i dati di diagnostica in locale nell'emulatore di archiviazione. Prima di archiviare i dati di diagnostica è necessario passare al [portale di Azure](http://portal.azure.com/) e creare un account di archiviazione classico. È consigliabile creare l'account di archiviazione nella stessa area geografica in cui si trova l'applicazione Azure. Mantenendo l'applicazione e l'account di archiviazione di Azure nella stessa area geografica, si evita il pagamento di costi per la larghezza di banda esterna e si riduce la latenza.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Aggiungere i contatori delle prestazioni nel file della diagnostica
È possibile usare diversi contatori. L'esempio seguente comprende diversi contatori delle prestazioni consigliati per il monitoraggio dei ruoli Web e di lavoro.

Aprire il file della diagnostica (diagnostics.wadcfg in SDK 2.4 e versioni precedenti o diagnostics.wadcfgx in SDK 2.5 e versioni successive) e aggiungere quanto segue all'elemento DiagnosticMonitorConfiguration:

```xml
<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
    <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
</PerformanceCounters>
```

L'attributo bufferQuotaInMB, che specifica la quantità massima di spazio di archiviazione disponibile nel file system per il tipo di raccolta dati (log di Azure, log di IIS ecc.). Il valore predefinito è 0. Quando viene raggiunta la quota massima, i dati meno recenti vengono eliminati man mano che vengono aggiunti nuovi dati. La somma di tutte le proprietà di bufferQuotaInMB deve essere maggiore del valore dell'attributo OverallQuotaInMB. Per informazioni dettagliate su come determinare la quantità di archiviazione necessaria per la raccolta dei dati di diagnostica, vedere la sezione relativa alla configurazione di WAD in [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

L'attributo scheduledTransferPeriod, che specifica l'intervallo tra i trasferimenti di dati pianificati, arrotondato per eccesso al minuto più vicino. Negli esempi seguenti è impostato su PT30M (30 minuti). Un valore di impostazione troppo basso, ad esempio 1 minuto, del periodo di trasferimento influisce negativamente sulle prestazioni dell'applicazione in produzione, ma può risultare utile per l'esecuzione rapida della diagnostica in fase di test. Il periodo di trasferimento pianificato deve essere sufficientemente basso da garantire che i dati di diagnostica non vengano sovrascritti nell'istanza, ma anche sufficientemente alto da non influire sulle prestazioni dell'applicazione.

L'attributo counterSpecifier specifica il contatore delle prestazioni di cui raccogliere i dati. L'attributo sampleRate specifica la frequenza con cui raccogliere i dati del contatore delle prestazioni, in questo caso 30 secondi.

Una volta aggiunti i contatori delle prestazioni di cui raccogliere i dati, salvare le modifiche nel file diagnostica. Sarà quindi necessario specificare l'account di archiviazione nel quale verranno salvati in modo permanente i dati di diagnostica.

### <a name="specify-the-storage-account"></a>Specificare l'account di archiviazione
Per salvare in modo permanente le informazioni di diagnostica nell'account di archiviazione di Azure, è necessario specificare una stringa di connessione nel file di configurazione del servizio (ServiceConfiguration.cscfg).

Per Azure SDK 2.5 l'account di archiviazione può essere specificato nel file diagnostics.wadcfgx.

> [!NOTE]
> Queste istruzioni si applicano solo ad Azure SDK 2.4 e versioni precedenti. Per Azure SDK 2.5 l'account di archiviazione può essere specificato nel file diagnostics.wadcfgx.
>
>

Per impostare le stringhe di connessione:

1. Aprire il file ServiceConfiguration.Cloud.cscfg usando l'editor di testo preferito e impostare la stringa di connessione per l'account di archiviazione. I valori *AccountName* e *AccountKey* sono disponibili nel dashboard dell'account di archiviazione del portale di Azure, in Chiavi di accesso.

    ```xml
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Salvare il file ServiceConfiguration.Cloud.cscfg.
3. Aprire il file ServiceConfiguration.Local.cscfg e verificare che UseDevelopmentStorage sia impostato su true.

    ```xml
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
   Ora che le stringhe di connessione sono impostate, i dati di diagnostica verranno salvati in modo permanente nell'account di archiviazione al momento della distribuzione dell'applicazione.
4. Salvare e compilare il progetto, quindi distribuire l'applicazione.

## <a name="step-2-optional-create-custom-performance-counters"></a>Passaggio 2: (Facoltativo) Creare contatori delle prestazioni personalizzati
Oltre ai contatori delle prestazioni predefiniti, è possibile aggiungere contatori delle prestazioni personalizzati per monitorare i ruoli Web o di lavoro. I contatori delle prestazioni personalizzati possono essere utilizzati per tenere traccia e monitorare il funzionamento specifico dell'applicazione e possono essere creati o eliminati in un'attività di avvio, in un ruolo Web o in un ruolo di lavoro con autorizzazioni elevate.

L'agente Diagnostica di Azure aggiorna la configurazione dei contatori delle prestazioni dal file con estensione wadcfg un minuto dopo l'avvio.  Se si creano contatori delle prestazioni personalizzati nel metodo OnStart e l'esecuzione delle attività di avvio dura più di un minuto, i contatori delle prestazioni personalizzati non risulteranno creati quando l'agente Diagnostica di Azure cercherà di caricarli.  In questo scenario Diagnostica di Azure acquisirà correttamente tutti i dati di diagnostica tranne i contatori delle prestazioni personalizzati.  Per risolvere questo problema, creare i contatori delle prestazioni in un'attività di avvio o spostare parte delle operazioni delle attività di avvio al metodo OnStart dopo avere creato i contatori delle prestazioni.

Per creare un semplice contatore delle prestazioni personalizzato denominato "\MyCustomCounterCategory\MyButton1Counter", effettuare i passaggi seguenti:

1. Aprire il file di definizione del servizio (CSDEF) dell'applicazione.
2. Aggiungere l'elemento Runtime all'elemento WebRole o WorkerRole per consentire l'esecuzione con privilegi elevati:

    ```xml
    <runtime executioncontext="elevated"/>
    ```
3. Salvare il file.
4. Aprire il file della diagnostica (diagnostics.wadcfg in SDK 2.4 e versioni precedenti o diagnostics.wadcfgx in SDK 2.5 e versioni successive) e aggiungere quanto segue a DiagnosticMonitorConfiguration:

    ```xml
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
      <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Salvare il file.
6. Creare la categoria dei contatori delle prestazioni personalizzati nel metodo OnStart del ruolo prima di richiamare base.OnStart. Nell'esempio C# seguente viene creata una categoria personalizzata, nel caso in cui non esista già:

    ```csharp
    public override bool OnStart()
    {
      if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
      {
         CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

         // add a counter tracking user button1 clicks
         CounterCreationData operationTotal1 = new CounterCreationData();
         operationTotal1.CounterName = "MyButton1Counter";
         operationTotal1.CounterHelp = "My Custom Counter for Button1";
         operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
         counterCollection.Add(operationTotal1);

         PerformanceCounterCategory.Create(
           "MyCustomCounterCategory",
           "My Custom Counter Category",
           PerformanceCounterCategoryType.SingleInstance, counterCollection);

         Trace.WriteLine("Custom counter category created.");
      }
      else {
        Trace.WriteLine("Custom counter category already exists.");
      }

    return base.OnStart();
    }
    ```
7. Aggiornare i contatori all'interno dell'applicazione. Nell'esempio seguente viene aggiornato un contatore delle prestazioni personalizzato per gli eventi Button1_Click:

    ```csharp
    protected void Button1_Click(object sender, EventArgs e)
    {
      PerformanceCounter button1Counter = new PerformanceCounter(
        "MyCustomCounterCategory",
        "MyButton1Counter",
        string.Empty,
        false);
      button1Counter.Increment();
      this.Button1.Text = "Button 1 count: " +
        button1Counter.RawValue.ToString();
    }
    ```
8. Salvare il file.  

I dati dei contatori delle prestazioni personalizzati verranno ora raccolti dal monitoraggio di diagnostica di Azure.

## <a name="step-3-query-performance-counter-data"></a>Passaggio 3: Eseguire query sui dati dei contatori delle prestazioni
Dopo che l'applicazione è stata distribuita ed è in esecuzione, il monitoraggio diagnostica inizierà a raccogliere i dati dei contatori delle prestazioni e a salvarli in modo permanente nell'archiviazione di Azure. Usare strumenti come Esplora server in Visual Studio, [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/) o [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) di Cerebrata per visualizzare i dati dei contatori delle prestazioni nella tabella WADPerformanceCountersTable. A livello di codice è anche possibile eseguire una query sul servizio tabelle usando [C#](../cosmos-db/table-storage-how-to-use-dotnet.md), [Java](../cosmos-db/table-storage-how-to-use-java.md), [Node.js](../cosmos-db/table-storage-how-to-use-nodejs.md), [Python](../cosmos-db/table-storage-how-to-use-python.md), [Ruby](../cosmos-db/table-storage-how-to-use-ruby.md) o [PHP](../cosmos-db/table-storage-how-to-use-php.md).

Nell'esempio C# seguente viene illustrato come eseguire una query di base sulla tabella WADPerformanceCountersTable e come salvare i dati di diagnostica in un file CSV. Dopo aver salvato i contatori delle prestazioni in un file CSV, è possibile usare le funzionalità grafiche di Microsoft Excel o altri strumenti per visualizzare i dati. Ricordarsi di aggiungere un riferimento a Microsoft.WindowsAzure.Storage.dll, che è stato incluso in Azure SDK per .NET a partire dalla versione di ottobre 2012. L'assembly viene installato nella directory %Programmi%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\.

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Table;
...

// Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
// you store your connection string using the Microsoft Azure service configuration
// system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
// to retrieve your storage connection string.  If you're not using Cloud Services, it's
// recommended that you store the connection string in your web.config or app.config file.
// Use the ConfigurationManager type to retrieve your storage connection string.

string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
//string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

// Get a reference to the storage account using the connection string.  You can also use the development
// storage account (Storage Emulator) for local debugging.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
//CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

// Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
  .Where(
    TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
      TableOperators.And,
      TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
      TableOperators.And,
      TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
    )
  )
);

// Execute the table query.
IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

// Process the query results and build a CSV file.
StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

foreach (PerformanceCountersEntity entity in result)
{
  sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
    + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
}

StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
sw.Write(sb.ToString());
sw.Close();
```

Per eseguire il mapping di entità a oggetti C#, viene utilizzata una classe personalizzata derivata da **TableEntity**. Il codice seguente definisce una classe dell'entità che rappresenta un contatore delle prestazioni nella tabella **WADPerformanceCountersTable** .

```csharp
public class PerformanceCountersEntity : TableEntity
{
  public long EventTickCount { get; set; }
  public string DeploymentId { get; set; }
  public string Role { get; set; }
  public string RoleInstance { get; set; }
  public string CounterName { get; set; }
  public double CounterValue { get; set; }
}
```

## <a name="next-steps"></a>Passaggi successivi
[Visualizzare altri articoli sulla diagnostica di Azure](../azure-diagnostics.md)
