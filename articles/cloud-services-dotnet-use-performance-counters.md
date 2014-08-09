<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="" editor="" />

Utilizzo dei contatori di prestazioni in Azure
==============================================

È possibile utilizzare i contatori delle prestazioni in un'applicazione Azure per raccogliere dati che contribuiscono a individuare i colli di bottiglia e a ottimizzare le prestazioni di sistema e dell'applicazione. I contatori delle prestazioni disponibili per Windows Server 2008, Windows Server 2012, IIS e ASP.NET possono essere utilizzati per raccogliere dati e per determinare l'integrità dell'applicazione Azure.

In questo argomento viene illustrato come abilitare i contatori delle prestazioni nell'applicazione utilizzando il file di configurazione diagnostics.wadcfg. Per informazioni sul monitoraggio delle prestazioni dell'applicazione nel [Portale di gestione di Azure](http://manage.windowsazure.com), vedere [Come monitorare i servizi cloud](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/). Per ulteriori indicazioni dettagliate sulla creazione di una strategia di registrazione e traccia e sull'utilizzo della diagnostica e di altre tecniche per risolvere i problemi e ottimizzare le applicazioni Azure, vedere le [procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure](http://msdn.microsoft.com/it-it/library/windowsazure/hh771389.aspx).

Questa attività include i passaggi seguenti:

-   [Prerequisiti](#prereqs)
-   [Passaggio 1: Raccolta e archiviazione dei dati dei contatori delle prestazioni](#step1)
-   [Passaggio 2: (Facoltativo) Creazione dei contatori delle prestazioni personalizzati](#step2)
-   [Passaggio 3: Esecuzione di query sui dati dei contatori delle prestazioni](#step3)
-   [Passaggi successivi](#nextsteps)
-   [Risorse aggiuntive](#additional)

Prerequisiti
------------

In questo articolo si presuppone che il monitoraggio diagnostica sia stato importato nell'applicazione e che il file di configurazione diagnostics.wadcfg sia stato aggiunto alla soluzione Visual Studio. Per ulteriori informazioni vedere i passaggi 1 e 2 della procedura di [abilitazione della diagnostica in Azure](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/).

Passaggio 1: Raccolta e archiviazione dei dati dei contatori delle prestazioni
------------------------------------------------------------------------------

Dopo aver aggiunto il file diagnostics.wadcfg alla soluzione Visual Studio è possibile configurare la raccolta e l'archiviazione dei dati dei contatori delle prestazioni in un'applicazione Azure. Questa operazione viene eseguita tramite l'aggiunta di contatori delle prestazioni nel file diagnostics.wadcfg. I dati di diagnostica, inclusi i dati dei contatori delle prestazioni, vengono innanzitutto raccolti nell'istanza e quindi salvati in modo permanente nella tabella **WADPerformanceCountersTable** del servizio tabelle di Azure, pertanto è necessario specificare l'account di archiviazione nell'applicazione. Se si esegue il test dell'applicazione in locale nell'emulatore di calcolo, è anche possibile archiviare i dati di diagnostica in locale nell'emulatore di archiviazione. Prima di archiviare i dati di diagnostica è necessario passare al [Portale di gestione di Azure](http://manage.windowsazure.com) e creare un account di archiviazione. È consigliabile creare l'account di archiviazione nella stessa area geografica in cui si trova l'applicazione Azure per evitare di sostenere costi di larghezza di banda esterna e ridurre la latenza.

### Aggiungere i contatori delle prestazioni nel file diagnostics.wadcfg

È possibile raccogliere i dati di numerosi contatori delle prestazioni; l'esempio seguente comprende diversi contatori delle prestazioni consigliati per il monitoraggio dei ruoli Web e di lavoro.

Aprire il file diagnostics.wadcfg e aggiungere all'elemento **DiagnosticMonitorConfiguration** il codice seguente:

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
		-->?
      <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
        </PerformanceCounters>    

L'attributo **bufferQuotaInMB**, che specifica la quantità massima di spazio di archiviazione disponibile nel file system per il tipo di raccolta dati (log di Azure, log di IIS ecc.). Il valore predefinito è 0. Quando viene raggiunta la quota massima, i dati meno recenti vengono eliminati man mano che vengono aggiunti nuovi dati. La somma di tutte le proprietà di **bufferQuotaInMB** deve essere maggiore del valore dell'attributo **OverallQuotaInMB**. Per informazioni dettagliate su come determinare la quantità di archiviazione necessaria per la raccolta dei dati di diagnostica, vedere la sezione relativa alla configurazione di WAD in [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure](http://msdn.microsoft.com/it-it/library/windowsazure/hh771389.aspx).

L'attributo **scheduledTransferPeriod**, che specifica l'intervallo tra i trasferimenti di dati pianificati, arrotondato per eccesso al minuto più vicino. Negli esempi seguenti è impostato su PT30M (30 minuti). Un valore di impostazione troppo basso, ad esempio 1 minuto, del periodo di trasferimento influisce negativamente sulle prestazioni dell'applicazione in produzione, ma può risultare utile per l'esecuzione rapida della diagnostica in fase di test. Il periodo di trasferimento pianificato deve essere sufficientemente basso da garantire che i dati di diagnostica non vengano sovrascritti nell'istanza, ma anche sufficientemente alto da non influire sulle prestazioni dell'applicazione.

L'attributo **counterSpecifier** specifica il contatore delle prestazioni di cui raccogliere i dati.

L'attributo **sampleRate** specifica la frequenza con cui raccogliere i dati del contatore delle prestazioni, in questo caso 30 secondi.

Una volta aggiunti i contatori delle prestazioni di cui raccogliere i dati, salvare le modifiche nel file diagnostics.wadcfg. Sarà quindi necessario specificare l'account di archiviazione nel quale verranno salvati in modo permanente i dati di diagnostica.

### Specificare l'account di archiviazione

Per salvare in modo permanente le informazioni di diagnostica nell'account di archiviazione di Azure, è necessario specificare una stringa di connessione nel file di configurazione del servizio (ServiceConfiguration.cscfg). In Strumenti di Azure per Visual Studio versione 1.4 (agosto 2011) e versioni successive sono consentiti più file di configurazione (ServiceConfiguration.cscfg) in locale e nel cloud. È utile disporre di più configurazioni del servizio per la diagnostica perché in questo modo è possibile utilizzare gratuitamente l'emulatore di archiviazione per il test locale mantenendo al contempo un file di configurazione separato per la produzione.

Per impostare le stringhe di connessione:

1.  Aprire il file ServiceConfiguration.Cloud.cscfg utilizzando l'editor di testo preferito e impostare la stringa di connessione per l'account di archiviazione:

        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
        </ConfigurationSettings>

    I valori **AccountName** e **AccountKey** sono disponibili nel dashboard dell'account di archiviazione sul Portale di gestione, in **Manage Keys**.

2.  Salvare il file ServiceConfiguration.Cloud.cscfg.
3.  Aprire il file ServiceConfiguration.Local.cscfg e verificare che **UseDevelopmentStorage** sia impostato su true (impostazione predefinita).

        <ConfigurationSettings>

        </ConfigurationSettings>

    Ora che le stringhe di connessione sono impostate, i dati di diagnostica verranno salvati in modo permanente nell'account di archiviazione al momento della distribuzione dell'applicazione.

4.  Salvare e compilare il progetto, quindi distribuire l'applicazione.

Passaggio 2: (Facoltativo) Creazione dei contatori delle prestazioni personalizzati
-----------------------------------------------------------------------------------

Oltre ai contatori delle prestazioni predefiniti, è possibile aggiungere contatori delle prestazioni personalizzati per monitorare i ruoli Web o di lavoro. I contatori delle prestazioni personalizzati possono essere utilizzati per tenere traccia e monitorare il funzionamento specifico dell'applicazione e possono essere creati o eliminati in un'attività di avvio, in un ruolo Web o in un ruolo di lavoro con autorizzazioni elevate.

Per creare un semplice contatore delle prestazioni personalizzato denominato "\\MyCustomCounterCategory\\MyButton1Counter", effettuare i passaggi seguenti:

1.  Aprire il file di definizione del servizio (CSDEF) dell'applicazione.
2.  Aggiungere l'elemento **Runtime** all'elemento **WebRole** o **WorkerRole** per consentire l'esecuzione con privilegi elevati:

        <Runtime executionContext="elevated" />

3.  Salvare il file.
4.  Aprire il file diagnostics.wadcfg e aggiungere all'elemento **DiagnosticMonitorConfiguration** il codice seguente:

        <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
        </PerformanceCounters>        

5.  Salvare il file.
6.  Creare la categoria dei contatori delle prestazioni personalizzati nel metodo **OnStart** del ruolo prima di richiamare **base.OnStart**. Nell'esempio C\# seguente viene creata una categoria personalizzata, nel caso in cui non esista già:

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
           else{
           Trace.WriteLine("Custom counter category already exists.");
           }
           
           return base.OnStart();
           }

7.  Aggiornare i contatori all'interno dell'applicazione. Nell'esempio seguente viene aggiornato un contatore delle prestazioni personalizzato per gli eventi **Button1\_Click**:

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

8.  Salvare il file.

Una volta completati questi passaggi, i dati dei contatori delle prestazioni personalizzati vengono raccolti dal monitoraggio Diagnostica Azure.

Passaggio 3: Esecuzione di query sui dati dei contatori delle prestazioni
-------------------------------------------------------------------------

Dopo che l'applicazione è stata distribuita ed è in esecuzione, il monitoraggio diagnostica inizierà a raccogliere i dati dei contatori delle prestazioni e a salvarli in modo permanente nell'archiviazione di Azure. Utilizzare strumenti come **Esplora server in Visual Studio**, [Esplora archivi Azure](http://azurestorageexplorer.codeplex.com/) o [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) di Cerebrata per visualizzare i dati dei contatori delle prestazioni nella tabella **WADPerformanceCountersTable**. A livello di codice è inoltre possibile eseguire una query sul servizio tabelle utilizzando [C\#][], [Java](http://www.windowsazure.com/it-it/develop/java/how-to-guides/table-service/), [Node.js][], [Python](http://www.windowsazure.com/it-it/develop/python/how-to-guides/table-service/) o [PHP](http://www.windowsazure.com/it-it/develop/php/how-to-guides/table-service/).

Nell'esempio C\# seguente viene illustrato come eseguire una semplice query sulla tabella **WADPerformanceCountersTable** e come salvare i dati di diagnostica in un file CSV. Dopo aver salvato i contatori delle prestazioni in un file CSV, è possibile utilizzare le funzionalità grafiche di Microsoft Excel o altri strumenti per visualizzare i dati. Ricordarsi di aggiungere un riferimento a Microsoft.WindowsAzure.Storage.dll, che è stato incluso in Azure SDK per .NET a partire dalla versione di ottobre 2012. L'assembly viene installato nella directory %Program Files%\\Microsoft SDKs\\Windows Azure.NET SDK\\version-num\\ref\\.

     using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Table;

        ...

        // Get the connection string. When using Azure Cloud Services, it is recommended 
        // you store your connection string using the Azure service configuration
        // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type 
        // to retrieve your storage connection string.  If you're not using Cloud Services, it's
        // recommended that you store the connection string in your web.config or app.config file.
        // Use the ConfigurationManager type to retrieve your storage connection string.
        
        string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
        //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;
            
        // Get a reference to the storage account using the connection string.  You can also use the development storage account (Storage Emulator)
        // for local debugging.              
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

Per eseguire il mapping di entità a oggetti C\#, viene utilizzata una classe personalizzata derivata da **TableEntity**. Il codice seguente definisce una classe dell'entità che rappresenta un contatore delle prestazioni nella tabella **WADPerformanceCountersTable**.

     public class PerformanceCountersEntity : TableEntity
        {
            public long EventTickCount { get; set; }
            public string DeploymentId { get; set; }
            public string Role { get; set; }
            public string RoleInstance { get; set; }
            public string CounterName { get; set; }
            public double CounterValue { get; set; }                
        }

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base della raccolta dei contatori delle prestazioni, vedere i collegamenti seguenti per imparare a implementare scenari più complessi di risoluzione dei problemi.

-   [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure](http://msdn.microsoft.com/it-it/library/windowsazure/hh771389.aspx)
-   [Come monitorare i servizi cloud](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/)
-   [Come utilizzare il blocco applicazione per la scalabilità automatica](http://www.windowsazure.com/it-it/develop/net/how-to-guides/autoscaling/)
-   [Compilazione di applicazioni cloud elastiche e resilienti](http://msdn.microsoft.com/it-it/library/hh680949(PandP.50).aspx)

Risorse aggiuntive
------------------

-   [Abilitazione della diagnostica in Azure](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/)
-   [Raccogliere dati di registrazione utilizzando Diagnostica Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433048.aspx)
-   [Debug di un'applicazione Azure](http://msdn.microsoft.com/it-it/library/windowsazure/ee405479.aspx)


