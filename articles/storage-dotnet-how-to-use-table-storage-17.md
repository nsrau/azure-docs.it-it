<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service (2.0)" pageTitle="How to use table storage | Microsoft Azure" metaKeywords="Get started Azure table, Azure nosql, Azure large structured data store, Azure table, Azure table storage, Azure table .NET, Azure table storage .NET, Azure table C#, Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Table Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Come utilizzare il servizio di archiviazione tabelle
====================================================

[versione 1.7](/it-it/develop/net/how-to-guides/table-services-v17/ "versione 1.7") [versione 2.0](/it-it/develop/net/how-to-guides/table-services/ "versione 2.0")

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione tabelle di Azure. Negli esempi, scritti in C\#, viene utilizzata l'API .NET. Gli scenari presentati includono **creazione ed eliminazione di una tabella, inserimento di entità ed esecuzione di query sulle entità in una tabella**. Per ulteriori informazioni sulle tabelle, vedere la sezione [Passaggi successivi](#next-steps).

Sommario
--------

-   [Informazioni sul servizio tabelle](#what-is)
-   [Concetti](#concepts)
-   [Creazione di un account di archiviazione di Azure](#create-account)
-   [Configurazione di una stringa di connessione di archiviazione](#setup-connection-string)
-   [Procedura: Accedere all'archiviazione tabelle a livello di codice](#configure-access)
-   [Procedura: Creare una tabella](#create-table)
-   [Procedura: Aggiungere un'entità a una tabella](#add-entity)
-   [Procedura: Inserire un batch di entità](#insert-batch)
-   [Procedura: Recuperare tutte le entità di una partizione](#retrieve-all-entities)
-   [Procedura: Recuperare un intervallo di entità in una partizione](#retrieve-range-entities)
-   [Procedura: Recuperare una singola entità](#retrieve-single-entity)
-   [Procedura: Aggiornare un'entità](#update-entity)
-   [Procedura: Eseguire una query su un subset di proprietà di entità](#query-entity-properties)
-   [Procedura: Inserire o sostituire un'entità](#insert-entity)
-   [Procedura: Eliminare un'entità](#delete-entity)
-   [Procedura: Eliminare una tabella](#delete-table)
-   [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Creazione di un accountCreazione di un account di archiviazione di Azure
------------------------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Configurazione di una stringa di connessioneConfigurazione di una stringa di connessione di archiviazione
---------------------------------------------------------------------------------------------------------

Nell'API di archiviazione di Azure per .NET è possibile utilizzare una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. È possibile inserire la stringa di connessione di archiviazione in un file di configurazione, anziché impostarla come hardcoded nel codice:

-   Quando si utilizza Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione dei servizi di Azure (file `*.csdef` e `*.cscfg`).
-   Quando si utilizza Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ovvero il file `web.config`.

In entrambi i casi è possibile recuperare la stringa di connessione utilizzando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### Configurazione della stringa di connessione per l'utilizzo con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione.

Per configurare la stringa di connessione nella configurazione dei servizi di Azure:

1.  All'interno di Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul ruolo Web o sul ruolo di lavoro nella cartella **Ruoli** del progetto di distribuzione di Azure, quindi scegliere **Proprietà**.
     ![BLOB5](./media/storage-dotnet-how-to-use-table-storage-17/blob5.png)

2.  Fare clic sulla scheda **Impostazioni** e quindi sul pulsante **Aggiungi impostazione**.
     ![BLOB6](./media/storage-dotnet-how-to-use-table-storage-17/blob6.png)

    Nella griglia delle impostazioni verrà visualizzata una nuova voce **Impostazione1**.

3.  Nell'elenco a discesa **Tipo** della nuova voce **Impostazione1** selezionare **Stringa di connessione**.
     ![BLOB7](./media/storage-dotnet-how-to-use-table-storage-17/blob7.png)

4.  Fare clic sul pulsante **...** a destra della voce **Impostazione1**. Verrà visualizzata la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

5.  Scegliere se si desidera utilizzare come destinazione l'emulatore di archiviazione (servizio Archiviazione di Azure simulato nel computer locale) oppure un effettivo account di archiviazione nel cloud. Il codice riportato in questa guida funziona con entrambe le opzioni. Se si desidera archiviare i dati BLOB nell'account di archiviazione creato in precedenza in Azure, immettere il valore di **Chiave di accesso primaria** copiato dal passaggio precedente di questa esercitazione.
     ![BLOB8](./media/storage-dotnet-how-to-use-table-storage-17/blob8.png)

6.  Modificare il valore **Impostazione1** della voce **Nome** specificando un nome più descrittivo, ad esempio **StorageConnectionString**. A questa stringa di connessione verrà fatto riferimento più avanti nel codice in questa guida.
     ![BLOB9](./media/storage-dotnet-how-to-use-table-storage-17/blob9.png)

### Configurazione della stringa di connessione quando si utilizza Siti Web o Macchine virtuali

Con Siti Web o Macchine virtuali è consigliabile utilizzare il sistema di configurazione .NET, ovvero il file `web.config`. Per archiviare la stringa di connessione, utilizzare l'elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Per ulteriori informazioni sulle stringhe di connessione di archiviazione, leggere [Configurazione delle stringhe di connessione](http://msdn.microsoft.com/it-it/library/windowsazure/ee758697.aspx).

È ora possibile eseguire le procedure di questa guida.

Accedere a livello di codiceProcedura: Accedere all'archiviazione tabelle a livello di codice
---------------------------------------------------------------------------------------------

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C\# in cui si desidera accedere ad Archiviazione di Azure a livello di codice:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

È possibile utilizzare i tipi **CloudStorageAccount** e **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni dell'account di archiviazione dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Creare una tabellaProcedura: Creare una tabella
-----------------------------------------------

Per ottenere oggetti di riferimento per tabelle ed entità, è possibile utilizzare un oggetto **CloudTableClient**. Il codice seguente consente di creare un oggetto **CloudTableClient** e di utilizzarlo per creare una nuova tabella. In tutto il codice di questa guida viene utilizzata una stringa di connessione di archiviazione archiviata nel servizio di configurazione dell'applicazione Azure. Sono inoltre disponibili altri modi per creare l'oggetto **CloudStorageAccount**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist
    string tableName = "people";
    tableClient.CreateTableIfNotExist(tableName);

Aggiungere un'entità a una tabellaProcedura: Aggiungere un'entità a una tabella
-------------------------------------------------------------------------------

Per eseguire il mapping di entità a oggetti C\#, viene utilizzata una classe personalizzata derivata da **TableServiceEntity**. Per aggiungere un'entità a una classe, creare prima una classe che definisca le proprietà dell'entità. Il codice seguente consente di definire una classe di entità che utilizza il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione. La combinazione della chiave di riga e della chiave di partizione di un'entità consentono di identificare in modo univoco l'entità nella tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse.

    public class CustomerEntity : TableServiceEntity
            {
                public CustomerEntity(string lastName, string firstName)
                {
                    this.PartitionKey = lastName;
            this.RowKey = firstName;
                }
            
                public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
            }

Per le operazioni su tabella che interessano entità è necessario un oggetto **TableServiceContext**. Tale oggetto tiene traccia dello stato del lato client di tutte le entità tabella create e a cui viene effettuato l'accesso nel codice client. Se si mantiene un oggetto lato client che rappresenta la singola entità, le operazioni di scrittura risulteranno più efficaci perché, durante le operazioni di salvataggio, nel servizio tabelle verranno aggiornati solo gli oggetti con modifiche. Il codice seguente consente di creare un oggetto **TableServiceContext** mediante la chiamata al metodo **GetDataServiceContext**. Viene quindi creata un'istanza della classe **CustomerEntity** e viene chiamato **serviceContext.AddObject** per inserire la nuova entità nella tabella. L'oggetto entità viene quindi aggiunto a **serviceContext**, tuttavia non si verifica alcuna operazione di servizio. La nuova entità viene infine inviata al servizio tabelle quando viene chiamato il metodo **SaveChangesWithRetries**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Add the new customer to the people table
    serviceContext.AddObject("people", customer1);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

Inserire un batch di entitàProcedura: Inserire un batch di entità
-----------------------------------------------------------------

Per inserire un batch di entità nel servizio tabelle, è possibile utilizzare un'unica operazione di scrittura. Il codice seguente consente di creare tre oggetti entità e aggiungere ognuno al contesto del servizio utilizzando il metodo **AddObject**. Viene quindi chiamato **SaveChangesWithRetries** con il parametro **SaveChangesOptions.Batch**. Se si omette **SaveChangesOptions.Batch**, verranno effettuate tre chiamate distinte al servizio tabelle. Di seguito sono riportate altre informazioni sulle operazioni batch:

1.  È possibile eseguire aggiornamenti, eliminazioni o inserimenti in batch.
2.  Una singola operazione batch può includere fino a 100 entità.
3.  A tutte le entità di una singola operazione batch deve essere associata la stessa chiave di partizione.

<!-- -->

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    string tableName = "people";

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a customer entity and add to the table
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.Email = "Jeff@contoso.com";
    customer.PhoneNumber = "425-555-0104";
    serviceContext.AddObject(tableName, customer);

    // Create another customer entity and add to the table
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
    serviceContext.AddObject(tableName, customer2);

    // Create a customer entity and add to the table
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.Email = "Denise@contoso.com";
    customer3.PhoneNumber = "425-555-0103";
    serviceContext.AddObject(tableName, customer3);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.Batch);

Recuperare tutte le entitàProcedura: Recuperare tutte le entità di una partizione
---------------------------------------------------------------------------------

Per eseguire una query su una tabella relativa alle entità in una partizione, è possibile utilizzare una query LINQ. Per creare una query dall'origine dati, chiamare **serviceContext.CreateQuery**. Nel codice seguente viene specificato un filtro per le entità in cui la chiave di partizione è 'Smith'. Per completare la creazione dell'oggetto **CloudTableQuery**, chiamare **AsTableServiceQuery&lt;CustomerEntity\>** sul risultato della query LINQ. Per usufruire dei risultati, è quindi possibile utilizzare l'oggetto **partitionQuery** creato in un ciclo **foreach**. Il codice seguente consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key
    CloudTableQuery<CustomerEntity> partitionQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith"
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in partitionQuery)
        {
            Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
        }

Recuperare un intervallo di entitàProcedura: Recuperare un intervallo di entità in una partizione
-------------------------------------------------------------------------------------------------

Se non si desidera eseguire una query su tutte le entità di una partizione, è possibile specificare un intervallo utilizzando il metodo **CompareTo** anziché i soliti operatori maggiore di (\>) e minore di (&lt;). Tali operatori infatti non consentono di creare correttamente la query. Nel codice seguente vengono utilizzati due filtri per recuperare tutte le entità della partizione 'Smith' in cui la chiave di riga (nome) inizia con una lettera che precede la 'E' nell'alfabeto e quindi stampare i risultati della query. Se si utilizzano le entità aggiunte alla tabella nella sezione di inserimento batch di questa guida, questa volta verranno restituite solo due entità, ovvero Ben e Denise Smith, mentre Jeff Smith non sarà incluso.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key,
    // with the row key being up to the letter "E"
    CloudTableQuery<CustomerEntity> entityRangeQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey.CompareTo("E") < 0
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in entityRangeQuery)
        {
            Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
        }

Recuperare una singola entitàProcedura: Recuperare una singola entità
---------------------------------------------------------------------

Per recuperare una singola entità specifica, è possibile scrivere una query. Nel codice seguente vengono utilizzati due filtri per specificare il cliente 'Jeff Smith'. Anziché **AsTableServiceQuery**, nel codice viene chiamato **FirstOrDefault**. Questo metodo restituisce una sola entità, anziché una raccolta, pertanto il valore restituito viene assegnato direttamente a un oggetto **CustomerEntity**. Viene restituito un valore Null se per nessuna entità è disponibile una corrispondenza esatta di chiave di riga e chiave di partizione. La specifica delle chiavi di partizione e di riga in una query costituisce la soluzione più rapida per recuperare una singola entità dal servizio tabelle.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

Aggiornare un'entitàProcedura: Aggiornare un'entità
---------------------------------------------------

Per aggiornare un'entità, recuperarla dal servizio tabelle, modificare l'oggetto entità e quindi salvare le modifiche nel servizio tabelle. Il codice seguente consente di modificare il numero di telefono di un cliente esistente. Anziché **AddObject** come effettuato in precedenza per l'inserimento, in questo codice viene chiamato **UpdateObject**. Il metodo **SaveChangesWithRetries** chiama il servizio tabelle e l'entità viene aggiornata a meno che non sia stata modificata da un'altra applicazione da quando è stata recuperata dall'applicazione corrente. Se si verifica questa situazione, viene generata un'eccezione, pertanto è necessario recuperare, modificare e salvare di nuovo l'entità. Questo schema di ripetizione dei tentativi è comune in un sistema di sistema di archiviazione distribuita.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Specify a new phone number
    specificEntity.PhoneNumber = "425-555-0105";

    // Update the entity
    serviceContext.UpdateObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

Eseguire una query su un subset di proprietàProcedura: Eseguire una query su un subset di proprietà di entità
-------------------------------------------------------------------------------------------------------------

Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. Questa tecnica, denominata proiezione, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. La query nel codice seguente restituisce solo gli indirizzi di posta elettronica di entità nella tabella. Per ulteriori informazioni sulla proiezione, vedere questo [post di blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx). Si noti che la proiezione non è supportata nell'emulatore di archiviazione locale, pertanto questo codice viene eseguito solo se si utilizza un account sul servizio tabelle.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Define a projection query that retrieves only the Email property
    var projectionQuery = 
        from e in serviceContext.CreateQuery<CustomerEntity>("people")
        select new
        {
            Email = e.Email
            // You can specify additional fields here
        };

    // Loop through the results, displaying the Email value
    foreach (var person in projectionQuery)
    {
        Console.WriteLine(person.Email);
    }

Inserire o sostituire un'entitàProcedura: Inserire o sostituire un'entità
-------------------------------------------------------------------------

Spesso si desidera aggiungere un'entità a una tabella senza sapere se sia già esistente nella tabella. Con un'operazione di inserimento o sostituzione è possibile creare una singola richiesta e inserire l'entità se non esiste oppure sostituirla se è già esistente. Sulla base degli esempi precedenti, il codice seguente consente di inserire o sostituire l'entità per 'Walter Harp'. Dopo aver creato una nuova entità, nel codice viene chiamato il metodo **serviceContext.AttachTo**. Viene quindi chiamato **UpdateObject** e infine **SaveChangesWithRetries** con il parametro **SaveChangesOptions.ReplaceOnUpdate**. Se si omette il parametro **SaveChangesOptions.ReplaceOnUpdate**, verrà eseguita un'operazione di inserimento o unione. Si noti che l'operazione di inserimento o sostituzione non è supportata nell'emulatore di archiviazione locale, pertanto questo codice viene eseguito solo se si utilizza un account sul servizio tabelle. Per ulteriori informazioni sulle operazioni di inserimento o sostituzione e di inserimento o unione, vedere questo [post di blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx).

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.Email = "Walter@contoso.com";
    customer5.PhoneNumber = "425-555-0106";

    // Attach this customer to the people table
    serviceContext.AttachTo("people", customer5);

    // Insert this customer if new, or replace if exists
    serviceContext.UpdateObject(customer5);

    // Submit the operation the table service, using the ReplaceOnUpdate option
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.ReplaceOnUpdate);

Eliminare un'entitàProcedura: Eliminare un'entità
-------------------------------------------------

È possibile eliminare facilmente un'entità dopo averla recuperata. È inoltre possibile utilizzare il metodo **AttachTo** per iniziare a tenerne traccia senza recuperarla dal server (vedere l'operazione di inserimento o sostituzione riportata in precedenza). Dopo aver attivato la traccia dell'entità con **serviceContext**, chiamare **DeleteObject** con l'entità per eliminarla. Chiamare quindi **SaveChangesWithRetries**. Il codice seguente consente di recuperare ed eliminare un'entità customer.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Delete the entity
    serviceContext.DeleteObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

Eliminare una tabellaProcedura: Eliminare una tabella
-----------------------------------------------------

Nell'esempio di codice seguente viene infine illustrato come eliminare una tabella da un account di archiviazione. Una tabella eliminata non potrà essere creata nuovamente per un certo periodo di tempo.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Delete the table it if exists
    tableClient.DeleteTableIfExist("people");

Passaggi successiviPassaggi successivi
--------------------------------------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio BLOB:
    -   [Informazioni di riferimento sulla libreria client .NET](http://msdn.microsoft.com/it-it/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [Informazioni di riferimento sulle API REST](http://msdn.microsoft.com/it-it/library/windowsazure/dd179355)
-   Per ulteriori informazioni sulle attività avanzate che è possibile eseguire con Archiviazione di Azure, vedere la pagina relativa all'[archiviazione e all'accesso ai dati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx).
-   Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
    -   Per archiviare dati non strutturati, utilizzare [Archiviazione BLOB](/it-it/develop/net/how-to-guides/blob-storage/).
    -   Per archiviare dati relazionali, utilizzare [Database SQL](/it-it/develop/net/how-to-guides/sql-database/).

