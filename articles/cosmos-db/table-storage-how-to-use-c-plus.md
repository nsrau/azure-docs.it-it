---
title: Come usare l'archiviazione tabelle di Azure e l'API Tabella di Azure Cosmos DB con C++
description: Archiviare dati strutturati nel cloud usando l'archiviazione tabelle di Azure o l'API Tabelle di Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: 48222bf3f964f8c728f980f839c460862a8212ca
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818632"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Come usare l'archiviazione tabelle di Azure e l'API del servizio tabelle di Azure Cosmos DB con C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Questa guida illustra diversi scenari di uso comune del servizio di archiviazione tabelle di Azure o dell'API Tabella di Microsoft Azure Cosmos DB. Gli esempi sono scritti in C++ e utilizzano la [libreria client di Archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Questo articolo include gli scenari seguenti:

* Creare ed eliminare una tabella
* Usare le entità di tabella

> [!NOTE]
> Questa guida fa riferimento alla libreria client di Archiviazione di Azure per C++ versione 1.0.0 e successive. La versione consigliata per la libreria client di Archiviazione di Azure è la 2.2.0, disponibile tramite [NuGet](https://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Creare account

### <a name="create-an-azure-service-account"></a>Creare un account del servizio di Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Creare un account per l'API di tabella di Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Creazione di un’applicazione C++

In questa guida vengono usate le funzionalità di archiviazione di un'applicazione C++. A tale scopo, installare la libreria client di Archiviazione di Azure per C++.

Per installare la libreria client di Archiviazione di Azure per C++, usare i metodi seguenti:

* Linux. Seguire le istruzioni fornite nella pagina relativa alla [libreria client di Archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
* Windows. In Visual Studio selezionare **Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**. Nella finestra **Console di Gestione pacchetti** eseguire il comando seguente:

  ```powershell
  Install-Package wastorage
  ```

Per altre informazioni sulla **Console di Gestione pacchetti**, vedere [Installare e gestire pacchetti con la Console di Gestione pacchetti in Visual Studio](/nuget/tools/package-manager-console).

### <a name="configure-access-to-the-table-client-library"></a>Configurare l'accesso alla libreria client delle tabelle

Per usare le API di archiviazione di Azure per accedere alle tabelle, aggiungere le istruzioni `include` seguenti all'inizio del file C++:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

I client di archiviazione di Azure o di Cosmos DB usano le stringhe di connessione per archiviare endpoint e credenziali per l'accesso ai servizi di gestione dati. Quando si esegue un'applicazione client, è necessario specificare la stringa di connessione di archiviazione o di Azure Cosmos DB nel formato appropriato.

### <a name="set-up-an-azure-storage-connection-string"></a>Configurare una stringa di connessione di archiviazione di Azure

Questo esempio illustra come dichiarare un campo statico per memorizzare la stringa di connessione di Archiviazione di Azure:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Usare il nome dell'account di archiviazione per `<your_storage_account>`. Per <your_storage_account_key>, usare la chiave di accesso per l'account di archiviazione elencato nel [portale di Azure](https://portal.azure.com). Per informazioni sugli account di archiviazione e sulle chiavi di accesso, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Configurare una stringa di connessione di Azure Cosmos DB

Questo esempio illustra come dichiarare un campo statico per memorizzare la stringa di connessione di Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Usare il nome dell'account di Azure Cosmos DB per `<your_cosmos_db_account>`. Immettere la chiave primaria per `<your_cosmos_db_account_key>`. Immettere l'endpoint elencato nel [portale di Azure](https://portal.azure.com) per `<your_cosmos_db_endpoint>`.

Per testare l'applicazione nel proprio computer Windows locale, è possibile usare l'emulatore di archiviazione di Azure che viene installato con [Azure SDK](https://azure.microsoft.com/downloads/). L'emulatore di archiviazione è un'utilità che simula i servizi BLOB, tabelle e di accodamento di Azure nel computer di sviluppo locale. L'esempio seguente illustra come dichiarare un campo statico per memorizzare la stringa di connessione per l'emulatore di archiviazione locale:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Per avviare l'emulatore di archiviazione di Azure, dal desktop di Windows selezionare il pulsante **Start** o premere il tasto WINDOWS. Accedere all'*Emulatore di archiviazione di Microsoft Azure* ed eseguirlo. Per altre informazioni, vedere [Usare l'emulatore di archiviazione di Azure per sviluppo e test](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Recuperare la stringa di connessione

Per visualizzare le informazioni dell'account di archiviazione, è possibile usare la classe `cloud_storage_account`. Per recuperare le informazioni dell'account di archiviazione dalla stringa di connessione di archiviazione, usare il metodo `parse`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ottenere quindi un riferimento a una classe `cloud_table_client`. Questa classe consente di ottenere gli oggetti di riferimento per le tabelle e le entità archiviate all'interno del servizio di archiviazione tabelle. Il codice seguente crea un oggetto `cloud_table_client` usando l'oggetto account di archiviazione recuperato in precedenza:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Creare e aggiungere entità a una tabella

### <a name="create-a-table"></a>Creare una tabella

Un oggetto `cloud_table_client` consente di ottenere oggetti di riferimento per tabelle ed entità. Il codice seguente crea un oggetto `cloud_table_client` e lo usa per creare una nuova tabella.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità a una tabella, creare un nuovo oggetto `table_entity` e passarlo a `table_operation::insert_entity`. Nel codice seguente il nome del cliente viene utilizzato come chiave di riga e il cognome come chiave di partizione. La combinazione della chiave di riga e della chiave di partizione di un'entità consentono di identificare in modo univoco l'entità nella tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse. L'uso di chiavi di partizione diverse consente una maggiore scalabilità delle operazioni parallele. Per altre informazioni, vedere [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../storage/common/storage-performance-checklist.md).

Il codice seguente crea una nuova istanza di `table_entity` con alcuni dati del cliente da memorizzare. Il codice quindi chiama `table_operation::insert_entity` per creare un oggetto `table_operation` per inserire un'entità in una tabella e associa la nuova entità di tabella all'oggetto. Infine, il codice chiama il metodo `execute` sull'oggetto `cloud_table`. Il nuovo oggetto `table_operation` invia una richiesta al servizio tabelle per inserire la nuova entità cliente nella tabella `people`.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Inserire un batch di entità

Per inserire un batch di entità nel servizio tabelle, è possibile usare un'unica operazione di scrittura. Il codice seguente crea un oggetto `table_batch_operation` e quindi vi aggiunge tre operazioni di inserimento. Ciascuna operazione di inserimento viene aggiunta creando un nuovo oggetto entità, impostandone i valori e quindi chiamando il metodo `insert` sull'oggetto `table_batch_operation` per associare l'entità a una nuova operazione di inserimento. Il codice chiama quindi `cloud_table.execute` per eseguire l'operazione.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Di seguito sono riportate alcune informazioni sulle operazioni batch:

* È possibile eseguire fino a 100 operazioni `insert`, `delete`, `merge`, `replace`, `insert-or-merge` e `insert-or-replace` in qualsiasi combinazione in un singolo batch.  
* Un'operazione batch può prevedere un'operazione di recupero, se è l'unica operazione nel batch.  
* A tutte le entità di una singola operazione batch deve essere associata la stessa chiave di partizione.  
* Un'operazione batch è limitata a un payload di dati di 4 MB.  

## <a name="query-and-modify-entities"></a>Eseguire query sulle entità e modificarle

### <a name="retrieve-all-entities-in-a-partition"></a>Recuperare tutte le entità di una partizione

Per eseguire una query su una tabella per tutte le entità di una partizione, usare un oggetto `table_query`. L'esempio di codice seguente specifica un filtro per le entità in cui la chiave di partizione è `Smith`. Questo esempio consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.  

> [!NOTE]
> Questi metodi non sono attualmente supportati per C++ in Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

La query di questo esempio restituisce tutte le entità che soddisfano i criteri di filtro. Se si dispone di tabelle di grandi dimensioni ed è necessario scaricare le entità di tabella di frequente, è invece consigliabile archiviare i dati nei BLOB di Archiviazione di Azure.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperare un intervallo di entità in una partizione

Se non si intende eseguire una query su tutte le entità di una partizione, è possibile specificare un intervallo. Combinare il filtro della chiave di partizione con un filtro della chiave di riga. L'esempio di codice seguente usa due filtri per recuperare tutte le entità della partizione `Smith` in cui la chiave di riga (nome) inizia con una lettera che precede la `E` nell'alfabeto e quindi stampa i risultati della query.  

> [!NOTE]
> Questi metodi non sono attualmente supportati per C++ in Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Recuperare una singola entità

Per recuperare una singola entità specifica, è possibile scrivere una query. Il codice seguente usa `table_operation::retrieve_entity` per specificare il cliente `Jeff Smith`. Questo metodo restituisce una sola entità anziché una raccolta e il valore restituito si trova in `table_result`. La specifica delle chiavi di partizione e di riga in una query costituisce la soluzione più rapida per recuperare una singola entità dal servizio tabelle.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Sostituire un'entità

Per sostituire un'entità, recuperarla dal servizio tabelle, modificare l'oggetto entità e quindi salvare le modifiche nuovamente nel servizio tabelle. Il codice seguente consente di modificare il numero di telefono e l’indirizzo di posta elettronica di un cliente esistente. Invece di chiamare `table_operation::insert_entity`, questo codice usa `table_operation::replace_entity`. Con questo approccio l'entità viene completamente sostituita nel server, a meno che non sia stata modificata dopo essere stata recuperata. In questo caso, l'operazione ha esito negativo. Questo errore impedisce all'applicazione di sovrascrivere una modifica effettuata tra il recupero e l'aggiornamento da parte di un altro componente. Per gestire correttamente questo errore, recuperare di nuovo l'entità, apportare le modifiche, se ancora valide, e quindi eseguire un'altra operazione `table_operation::replace_entity`.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Inserire o sostituire un'entità

Le operazioni `table_operation::replace_entity` hanno esito negativo se l'entità è stata modificata dopo essere stata recuperata dal server. Per la corretta esecuzione dell'operazione `table_operation::replace_entity` è anche necessario recuperare prima l'entità dal server. In alcuni casi non è certo che l'entità sia presente nel server. I valori correnti archiviati in essa sono irrilevanti perché l'aggiornamento li sovrascrive tutti. Per ottenere questo risultato, usare un'operazione `table_operation::insert_or_replace_entity`. Questa operazione inserisce l'entità, se non esiste, altrimenti la sostituisce. Nell'esempio di codice seguente l'entità customer per `Jeff Smith` viene comunque recuperata, ma viene salvata di nuovo nel server usando `table_operation::insert_or_replace_entity`. Tutte le modifiche apportate all'entità tra le operazioni di recupero e aggiornamento verranno sovrascritte.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Eseguire query su un subset di proprietà di entità

Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. La query nel codice seguente usa il metodo `table_query::set_select_columns` per restituire solo gli indirizzi di posta elettronica di entità nella tabella.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> L'esecuzione di una query di alcune proprietà di un'entità è un'operazione più efficiente rispetto al recupero di tutte le proprietà.
>

## <a name="delete-content"></a>Eliminare contenuto

### <a name="delete-an-entity"></a>Eliminare un'entità

È possibile eliminare un'entità dopo averla recuperata. Dopo aver recuperato un'entità, chiamare `table_operation::delete_entity` con l'entità da eliminare. Chiamare quindi il metodo `cloud_table.execute`. Il codice seguente recupera ed elimina un'entità con chiave di partizione `Smith` e chiave di riga `Jeff`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Eliminare una tabella

L'esempio di codice seguente consente infine di eliminare una tabella dall'account di archiviazione. Una tabella eliminata non potrà essere creata nuovamente per un certo periodo di tempo dopo l'eliminazione.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>risoluzione dei problemi

Per Visual Studio Community Edition, se nel progetto si verificano errori di compilazione a causa dei file di inclusione *storage_account.h* e *table.h*, rimuovere l'opzione del compilatore **/permissive-** :

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Proprietà**.
1. Nella finestra di dialogo **Pagine delle proprietà** espandere **Proprietà di configurazione**, quindi **C/C++** e infine selezionare **Lingua**.
1. Impostare **Modalità di conformità** su **No**.

## <a name="next-steps"></a>Passaggi successivi

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.

Seguire questi collegamenti per altre informazioni sull'archiviazione di Azure e l'API del servizio tabelle in Azure Cosmos DB:

* [Introduzione all'API del servizio tabelle](table-introduction.md)
* [Elenco delle risorse di archiviazione di Azure in C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Informazioni di riferimento sulla libreria client di archiviazione per C++](https://azure.github.io/azure-storage-cpp)
* [Documentazione di Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/)
