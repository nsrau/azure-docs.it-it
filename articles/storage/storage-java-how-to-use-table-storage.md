---
title: Come usare l&quot;archiviazione tabelle da Java | Microsoft Docs
description: Archiviare dati non strutturati nel cloud con il servizio di archiviazione tabelle di Azure, ovvero un archivio dati NoSQL.
services: storage
documentationcenter: java
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 45145189-e67f-4ca6-b15d-43af7bfd3f97
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: bb72ac092496b5962c98006aca61b8c085cb1694
ms.openlocfilehash: 2c73585e9d1f265c011cf94d1923ecec5f6a3435


---
# <a name="how-to-use-table-storage-from-java"></a>Come usare l'archiviazione tabelle da Java
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Panoramica
Questa guida illustra diversi scenari di utilizzo comuni del servizio di archiviazione tabelle di Azure. Gli esempi sono scritti in Java e usano [Azure Storage SDK per Java][Azure Storage SDK for Java]. Gli scenari presentati includono **creazione**, **visualizzazione di un elenco** ed **eliminazione** di tabelle, nonché **inserimento**, **esecuzione di query**, **modifica** ed **eliminazione** di entità in una tabella. Per altre informazioni sulle tabelle, vedere la sezione [Passaggi successivi](#Next-Steps) .

Nota: per gli sviluppatori che usano il servizio di archiviazione di Azure in dispositivi Android, è disponibile un SDK specifico. Per altre informazioni, vedere [Azure Storage SDK per Android][Azure Storage SDK for Android].

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Creare un'applicazione Java
In questa guida si utilizzeranno le funzionalità di archiviazione che possono essere eseguite in un'applicazione Java in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in Azure.

A questo scopo, è necessario installare Java Development Kit (JDK) e creare un account di archiviazione di Azure nella sottoscrizione di Azure. Dopo avere eseguito questa operazione, sarà necessario verificare che il sistema di sviluppo in uso soddisfi i requisiti minimi e le dipendenze elencate nel repository [Azure Storage SDK per Java][Azure Storage SDK for Java] su GitHub. Se il sistema soddisfa i requisiti, è possibile seguire le istruzioni per scaricare e installare le librerie di archiviazione di Azure per Java nel sistema dall'archivio indicato. Dopo avere completato queste attività, sarà possibile creare un'applicazione Java che usa gli esempi illustrati in questo articolo.

## <a name="configure-your-application-to-access-table-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione tabelle
Aggiungere le istruzioni import seguenti all'inizio del file Java in cui si useranno le API di archiviazione di Microsoft Azure per accedere alle tabelle:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Impostare una stringa di connessione di archiviazione di Azure
I client di archiviazione di Azure usano le stringhe di connessione di archiviazione per archiviare endpoint e credenziali per l'accesso ai servizi di gestione dati. Quando si esegue un'applicazione client, è necessario specificare la stringa di connessione di archiviazione nel formato seguente, usando il nome dell'account di archiviazione e la chiave di accesso primaria relativa all'account di archiviazione riportata nel [portale di Azure](https://portal.azure.com) per i valori *AccountName* e *AccountKey*. In questo esempio viene illustrato come dichiarare un campo statico per memorizzare la stringa di connessione:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

In un'applicazione in esecuzione in un ruolo di Microsoft Azure, questa stringa può essere archiviata nel file di configurazione del servizio *ServiceConfiguration.cscfg*ed è accessibile con una chiamata al metodo **RoleEnvironment.getConfigurationSettings** . Nell'esempio seguente viene recuperata la stringa di connessione da un elemento **Setting** denominato *StorageConnectionString* nel file di configurazione del servizio:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Gli esempi seguenti presumono che sia stato usato uno di questi due metodi per ottenere la stringa di connessione di archiviazione.

## <a name="how-to-create-a-table"></a>Procedura: creare una tabella
Per ottenere oggetti di riferimento per tabelle ed entità, è possibile usare un oggetto **CloudTableClient**. Il codice seguente consente di creare un oggetto **CloudTableClient** e di usarlo per creare un oggetto **CloudTable** che rappresenta una tabella denominata "people". (Nota: esistono altri modi per creare oggetti **CloudStorageAccount**. Per altre informazioni, vedere **CloudStorageAccount** nel [Riferimento all'SDK del client di archiviazione di Azure]).

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-tables"></a>Procedura: elencare le tabelle
Per ottenere un elenco di tabelle, chiamare il metodo **CloudTableClient.listTables()** per recuperare un elenco iterabile di nomi di tabelle.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-an-entity-to-a-table"></a>Procedura: aggiungere un’entità a una tabella
Per eseguire il mapping di entità a oggetti Java viene utilizzata una classe personalizzata che implementa **TableEntity**. Per comodità, la classe **TableServiceEntity** implementa **TableEntity** e usa la reflection per eseguire il mapping di proprietà ai metodi Get e Set denominati per le proprietà. Per aggiungere un'entità a una classe, creare prima una classe che definisca le proprietà dell'entità. Il codice seguente consente di definire una classe di entità che usa il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione. La combinazione della chiave di riga e della chiave di partizione di un'entità consentono di identificare in modo univoco l'entità nella tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

Per le operazioni su tabella che interessano entità è necessario un oggetto **TableServiceContext** . Questo oggetto definisce l'operazione da eseguire su un'entità, che può essere eseguita con un oggetto **CloudTable** . Il codice seguente crea una nuova istanza della classe **CustomerEntity** con alcuni dati del cliente da memorizzare. Il codice chiama quindi **TableOperation.insertOrReplace** per creare un oggetto **TableOperation** in modo da inserire un'entità in una tabella e vi associa la nuova classe **CustomerEntity**. Infine, il codice chiama il metodo **execute** sull'oggetto **CloudTable**, specificando la tabella "people" e il nuovo oggetto **TableOperation**, che quindi invia una richiesta al servizio di archiviazione per inserire la nuova entità cliente nella tabella "people" o sostituire l'entità se ne esiste già una.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-insert-a-batch-of-entities"></a>Procedura: inserire un batch di entità
Per inserire un batch di entità nel servizio tabelle, è possibile usare un'unica operazione di scrittura. Il codice seguente crea un oggetto **TableBatchOperation** quindi vi aggiunge tre operazioni di inserimento. Ciascuna operazione di inserimento viene aggiunta creando un nuovo oggetto entità, impostandone i valori, quindi chiamando il metodo **insert** sull'oggetto **TableBatchOperation** per associare l'entità a una nuova operazione di inserimento. Il codice chiama quindi il metodo **execute** sull'oggetto **CloudTable**, specificando la tabella "people" e l'oggetto **TableBatchOperation**, che invia il batch di operazioni su tabella al servizio di archiviazione in un'unica richiesta.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Di seguito sono riportate alcune informazioni sulle operazioni batch:

* È possibile eseguire fino a 100 operazioni di inserimento, eliminazione, unione, sostituzione, inserimento o unione e inserimento o sostituzione in qualsiasi combinazione in un unico batch.
* Un'operazione batch può prevedere un'operazione di recupero, se è l'unica operazione nel batch.
* A tutte le entità di una singola operazione batch deve essere associata la stessa chiave di partizione.
* Un'operazione batch è limitata a un payload di dati di 4 MB.

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>Procedura: recuperare tutte le entità di una partizione
Per eseguire una query su una tabella relativa alle entità in una partizione è possibile utilizzare **TableQuery**. Chiamare **TableQuery.from** per creare su una particolare tabella una query che restituisca un tipo di risultato specificato. Nel codice seguente viene specificato un filtro per le entità in cui la chiave di partizione è 'Smith'. **TableQuery.generateFilterCondition** è un metodo helper che consente di creare filtri per le query. Chiamare **where** sul riferimento restituito dal metodo **TableQuery.from** per applicare il filtro alla query. Quando la query viene eseguita con una chiamata a **execute** sull'oggetto **CloudTable** restituisce un oggetto **Iterator** con il tipo di risultato **CustomerEntity** specificato. Per usufruire dei risultati, è quindi possibile utilizzare l'oggetto **Iterator** restituito in ogni ciclo. Il codice seguente consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>Procedura: recuperare un intervallo di entità in una partizione
Se non si desidera eseguire una query su tutte le entità di una partizione, è possibile specificare un intervallo usando gli operatori di confronto in un filtro. Nel codice seguente vengono combinati due filtri per recuperare tutte le entità della partizione "Smith" in cui la chiave di riga (nome) inizia con una lettera che precede la 'E' nell'alfabeto e quindi stampare i risultati della query. Se si utilizzano le entità aggiunte alla tabella nella sezione di inserimento batch di questa guida, questa volta verranno restituite solo due entità, ovvero Ben e Denise Smith, mentre Jeff Smith non sarà incluso.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-retrieve-a-single-entity"></a>Procedura: recuperare una singola entità
Per recuperare una singola entità specifica, è possibile scrivere una query. Il codice seguente chiama **TableOperation.retrieve** con i parametri della chiave di partizione e della chiave di riga per specificare il cliente "Jeff Smith", invece di creare un oggetto **TableQuery** e usare filtri per eseguire la stessa operazione. Quando si esegue l'operazione di recupero viene restituita una sola entità piuttosto che una raccolta. Il metodo **getResultAsType** esegue il cast del risultato al tipo di destinazione dell'assegnazione, un oggetto **CustomerEntity**. Se questo tipo non è compatibile con il tipo specificato per la query, verrà generata un'eccezione. Viene restituito un valore Null se per nessuna entità è disponibile una corrispondenza esatta di chiave di riga e chiave di partizione. La specifica delle chiavi di partizione e di riga in una query costituisce la soluzione più rapida per recuperare una singola entità dal servizio tabelle.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-modify-an-entity"></a>Procedura: modificare un'entità
Per modificare un'entità, recuperarla dal servizio tabelle, modificare l'oggetto entità e quindi salvare le modifiche nel servizio tabelle con un'operazione di sostituzione o unione. Il codice seguente consente di modificare il numero di telefono di un cliente esistente. Anziché **TableOperation.insert** come effettuato in precedenza per l'inserimento, in questo codice viene chiamato **TableOperation.replace**. Il metodo **CloudTable.execute** chiama il servizio tabelle e l'entità viene sostituita a meno che non sia stata modificata da un'altra applicazione da quando è stata recuperata dall'applicazione corrente. Se si verifica questa situazione, viene generata un'eccezione, pertanto è necessario recuperare, modificare e salvare di nuovo l'entità. Questo schema di ripetizione dei tentativi che supporta la concorrenza ottimistica è comune in un sistema di sistema di archiviazione distribuita.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-query-a-subset-of-entity-properties"></a>Procedura: eseguire query su un subset di proprietà di entità
Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. Questa tecnica, denominata proiezione, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. La query nel codice seguente utilizza il metodo **select** per restituire solo gli indirizzi di posta elettronica di entità nella tabella. I risultati vengono proiettati in una raccolta di **stringhe** con l'aiuto di un oggetto **EntityResolver**, che esegue la conversione di tipo sulle entità restituite dal server. Per altre informazioni sulla proiezione, vedere [Azure Tables: Introducing Upsert and Query Projection][Azure Tables: Introducing Upsert and Query Projection] (Tabelle di Azure: Introduzione di Upsert e proiezione di query in tabelle di Azure). Si noti che la proiezione non è supportata nell'emulatore di archiviazione locale, pertanto questo codice viene eseguito solo se si utilizza un account sul servizio tabelle.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-insert-or-replace-an-entity"></a>Procedura: inserire o sostituire un'entità
Spesso si desidera aggiungere un'entità a una tabella senza sapere se sia già esistente nella tabella. Con un'operazione di inserimento o sostituzione è possibile creare una singola richiesta e inserire l'entità se non esiste oppure sostituirla se è già esistente. Sulla base degli esempi precedenti, il codice seguente consente di inserire o sostituire l'entità per "Walter Harp". Dopo aver creato una nuova entità, nel codice viene chiamato il metodo **TableOperation.insertOrReplace** . Questo codice chiama quindi il metodo **execute** sull'oggetto **CloudTable** con la tabella e l'operazione di inserimento o sostituzione tabella come parametri. Per aggiornare solo parte di un'entità, è invece possibile utilizzare il metodo **TableOperation.insertOrMerge** . Si noti che l'operazione di inserimento o sostituzione non è supportata nell'emulatore di archiviazione locale, pertanto questo codice viene eseguito solo se si usa un account sul servizio tabelle. Per altre informazioni sull'operazione di inserimento o sostituzione e l'operazione di inserimento o unione, vedere [Azure Tables: Introducing Upsert and Query Projection][Azure Tables: Introducing Upsert and Query Projection] (Tabelle di Azure: Introduzione di Upsert e proiezione di query in tabelle di Azure).

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-an-entity"></a>Procedura: eliminare un'entità
È possibile eliminare facilmente un'entità dopo averla recuperata. Dopo il recupero dell'entità, chiamare **TableOperation.delete** con l'entità da eliminare. Chiamare **execute** sull'oggetto **CloudTable**. Il codice seguente consente di recuperare ed eliminare un'entità customer.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-table"></a>Procedura: eliminare una tabella
Nell'esempio di codice seguente viene infine illustrato come eliminare una tabella da un account di archiviazione. Una tabella eliminata non potrà essere creata nuovamente per un certo periodo di tempo, di solito inferiore a&40; secondi.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

* [Azure Storage SDK per Java][Azure Storage SDK for Java]
* [Riferimento all'SDK del client di archiviazione di Azure][Riferimento all'SDK del client di archiviazione di Azure]
* [API REST di Archiviazione di Azure][Azure Storage REST API]
* [Blog del team di Archiviazione di Azure][Azure Storage Team Blog]

Per altre informazioni, vedere anche il [Centro per sviluppatori Java](/develop/java/).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Riferimento all'SDK del client di archiviazione di Azure]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Tables: Introducing Upsert and Query Projection]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx



<!--HONumber=Dec16_HO2-->


