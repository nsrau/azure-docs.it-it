<properties
    pageTitle="Come usare l'archiviazione tabelle (C++) | Microsoft Azure"
	description="Archiviare dati non strutturati nel cloud con il servizio di archiviazione tabelle di Azure, ovvero un archivio dati NoSQL."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
	ms.date="07/26/2016"
    ms.author="dineshm"/>

# Come usare l'archiviazione tabelle da C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## Overview  
In questa guida sono illustrati diversi scenari di utilizzo comuni del servizio di archiviazione tabelle di Azure. Gli esempi sono scritti in C++ e utilizzano la [libreria client di Archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Gli scenari presentati includono **creazione ed eliminazione di una tabella** e **uso di entità di tabella**.

>[AZURE.NOTE] Questa guida fa riferimento alla libreria client di Archiviazione di Azure per C++ versione 1.0.0 e successive. La versione consigliata per la libreria client di archiviazione è la 2.2.0, disponibile tramite [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## Creazione di un’applicazione C++  
In questa guida verranno usate le funzionalità di archiviazione che è possibile eseguire all'interno di un'applicazione C++. A tal fine, sarà necessario installare la libreria client di Archiviazione di Azure per C++ e creare un account di archiviazione Azure nella propria sottoscrizione Azure.

Per installare la libreria client di Archiviazione di Azure per C++, è possibile utilizzare i metodi seguenti:

-	**Linux:** seguire le istruzioni fornite nella pagina relativa al [README della libreria client di Archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
-	**Windows:** in Visual Studio, fare clic su **Strumenti > Gestione pacchetti NuGet > Console di gestione pacchetti**. Digitare il comando seguente nella [console Gestione pacchetti NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e premere INVIO.

		Install-Package wastorage

## Configurare l'applicazione per l'accesso all'archiviazione tabelle  
Aggiungere le istruzioni include seguenti all'inizio del file C++ in cui si desidera utilizzare le API di archiviazione di Azure per accedere alle tabelle:

	#include "was/storage_account.h"
	#include "was/table.h"

## Impostare una stringa di connessione di archiviazione di Azure  
I client di archiviazione di Azure usano le stringhe di connessione di archiviazione per archiviare endpoint e credenziali per l'accesso ai servizi di gestione dati. Quando si esegue un'applicazione client, è necessario specificare la stringa di connessione di archiviazione nel formato seguente. Usare il nome del proprio account di archiviazione e la chiave di accesso alle risorse di archiviazione indicata nel [portale di Azure](https://portal.azure.com) per i valori *AccountName* e *AccountKey*. Per informazioni sugli account di archiviazione e sulle chiavi di accesso, vedere [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md). In questo esempio viene illustrato come dichiarare un campo statico per memorizzare la stringa di connessione:

	// Define the connection string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Per testare l'applicazione nel proprio computer Windows locale, è possibile usare l'[emulatore di archiviazione](storage-use-emulator.md) di Azure che viene installato con [Azure SDK](https://azure.microsoft.com/downloads/). L'emulatore di archiviazione è un'utilità che simula i servizi BLOB, tabelle e di accodamento di Azure nel computer di sviluppo locale. Nell’esempio seguente viene illustrato come dichiarare un campo statico per memorizzare la stringa di connessione all’emulatore di archiviazione locale:

	// Define the connection string with Azure storage emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Per avviare l'emulatore di archiviazione di Azure, fare clic sul pulsante **Start** o premere il tasto WINDOWS. Iniziare a digitare **Emulatore di archiviazione di Azure** e quindi selezionare **Emulatore di archiviazione di Microsoft Azure** dall'elenco delle applicazioni.

Gli esempi seguenti presumono che sia stato usato uno di questi due metodi per ottenere la stringa di connessione di archiviazione.

## Recuperare la stringa di connessione  
Per rappresentare le informazioni dell'account di archiviazione, è possibile usare la classe **cloud\_storage\_account**. Per recuperare le informazioni sull'account di archiviazione dalla stringa di connessione alla risorsa di archiviazione, è possibile utilizzare il metodo parse.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Successivamente, ottenere un riferimento a una classe **cloud\_table\_client** in quanto consente di ottenere gli oggetti di riferimento per le tabelle e le entità archiviate all'interno del servizio di archiviazione tabelle. Il codice seguente crea un oggetto **cloud\_table\_client** usando l'oggetto account di archiviazione recuperato in precedenza:

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## Creare una tabella
L’oggetto **cloud\_table\_client** consente di ottenere oggetti di riferimento per tabelle ed entità. Il codice seguente consente di creare un oggetto **cloud\_table\_client** e di utilizzarlo per creare una nuova tabella.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();  

## Aggiungere un'entità a una tabella
Per aggiungere un'entità a una tabella, creare un nuovo oggetto **table\_entity** e passarlo a **table\_operation::insert\_entity**. Nel codice seguente il nome del cliente viene utilizzato come chiave di riga e il cognome come chiave di partizione. La combinazione della chiave di riga e della chiave di partizione di un'entità consentono di identificare in modo univoco l'entità nella tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse, tuttavia l'utilizzo di chiavi di partizione diverse assicura una maggiore scalabilità in caso di operazioni parallele. Per altre informazioni, vedere [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](storage-performance-checklist.md).

Il codice seguente consente di creare una nuova istanza della classe **table\_entity** con alcuni dati del cliente da memorizzare. Il codice quindi chiama **table\_operation::insert\_entity** per creare un oggetto **table\_operation** per inserire un’entità in una tabella e associa la nuova entità di tabella all’oggetto. Infine, il codice chiama il metodo execute sull’oggetto **cloud\_table**. E il nuovo oggetto **table\_operation** invia una richiesta al servizio tabelle per inserire la nuova entità cliente nella tabella "people".

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

## Inserire un batch di entità
Per inserire un batch di entità nel servizio tabelle, è possibile usare un'unica operazione di scrittura. Il codice seguente crea un oggetto **table\_batch\_operation** e quindi vi aggiunge tre operazioni di inserimento. Ciascuna operazione di inserimento viene aggiunta creando un nuovo oggetto entità, impostandone i valori, quindi chiamando il metodo insert sull'oggetto **table\_batch\_operation** per associare l'entità a una nuova operazione di inserimento. Per eseguire l'operazione, viene quindi chiamato **cloud\_table.execute**.

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

Di seguito sono riportate alcune informazioni sulle operazioni batch:

-	È possibile eseguire fino a 100 operazioni di inserimento, eliminazione, unione, sostituzione, inserimento o unione e inserimento o sostituzione in qualsiasi combinazione in un unico batch.
-	Un'operazione batch può prevedere un'operazione di recupero, se è l'unica operazione nel batch.
-	A tutte le entità di una singola operazione batch deve essere associata la stessa chiave di partizione.
-	Un'operazione batch è limitata a un payload di dati di 4 MB.

## Recuperare tutte le entità di una partizione
Per eseguire una query su una tabella e recuperare tutte le entità di una partizione, utilizzare un oggetto **table\_query**. Nell'esempio di codice seguente viene specificato un filtro per le entità in cui la chiave di partizione è 'Smith'. Questo esempio consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.

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

La query di questo esempio consente di visualizzare tutte le entità che soddisfano i criteri di filtro. Se si dispone di tabelle di grandi dimensioni ed è necessario scaricare le entità di tabella di frequente, è invece consigliabile archiviare i dati nei BLOB di Archiviazione di Azure.

## Recuperare un intervallo di entità in una partizione
Se non si desidera eseguire una query su tutte le entità di una partizione, è possibile specificare un intervallo combinando il filtro della chiave di partizione con quello della chiave di riga. Nell'esempio di codice seguente vengono usati due filtri per recuperare tutte le entità della partizione 'Smith' in cui la chiave di riga (nome) inizia con una lettera che precede la 'E' nell'alfabeto e quindi stampare i risultati della query.

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

## Recuperare una singola entità
Per recuperare una singola entità specifica, è possibile scrivere una query. Il codice seguente usa **table\_operation::retrive\_entity** per specificare il cliente 'Jeff Smith'. Questo metodo restituisce una sola entità anziché una raccolta, e il valore restituito si trova in **table\_result**. La specifica delle chiavi di partizione e di riga in una query costituisce la soluzione più rapida per recuperare una singola entità dal servizio tabelle.

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

## Sostituire un'entità
Per sostituire un'entità, recuperarla dal servizio tabelle, modificare l'oggetto entità e quindi salvare le modifiche nuovamente nel servizio tabelle. Il codice seguente consente di modificare il numero di telefono e l’indirizzo di posta elettronica di un cliente esistente. Anziché chiamare **table\_operation:: insert\_entity**, in questo codice viene utilizzato **table\_operation:: replace\_entity**. In questo modo l'entità viene completamente sostituita nel server, a meno che non sia stata modificata da quando è stata recuperata. In questo caso, infatti, l'operazione non viene eseguita per impedire all'applicazione di sovrascrivere inavvertitamente una modifica effettuata tra il recupero e l'aggiornamento da parte di un altro componente dell'applicazione. Per risolvere questo errore, recuperare di nuovo l'entità, apportare le modifiche, se ancora valide, quindi eseguire un'altra operazione **table\_operation::replace\_entity**. La sezione successiva illustra come ovviare a questo comportamento.

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

## Inserire o sostituire un'entità
Le operazioni **table\_operation::replace\_entity** non vengono eseguite se l'entità è stata modificata rispetto a quando è stata recuperata dal server. Per la corretta esecuzione di **table\_operation::replace\_entity**, è inoltre necessario recuperare innanzitutto l'entità dal server. In alcuni casi, tuttavia, non è noto se l'entità già esista nel server e i valori correnti in essa archiviati sono irrilevanti, pertanto devono essere sovrascritti completamente dall'aggiornamento. A tale scopo, si utilizzerà un’operazione **table\_operation:: insert\_or\_replace\_entity**. Questa operazione inserisce l'entità se non è già esistente oppure la sostituisce se esiste già, indipendentemente dalla data dell'ultimo aggiornamento. Nell'esempio di codice seguente l'entità customer per Jeff Smith viene recuperata comunque, ma viene salvata di nuovo nel server usando **table\_operation::insert\_or\_replace\_entity**. Tutte le modifiche apportate all'entità tra le operazioni di recupero e aggiornamento verranno sovrascritte.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Insert-or-replace an entity.
	azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

	properties_to_insert_or_replace.reserve(2);

	// Specify a phone number.
	properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

	// Specify an email address.
	properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

	// Create an operation to insert-or-replace the entity.
	azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

	// Submit the operation to the Table service.
	azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## Eseguire query su un subset di proprietà di entità  
Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. La query nel codice seguente utilizza il metodo **table\_query::set\_select\_columns** per restituire solo gli indirizzi di posta elettronica delle entità nella tabella.

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

>[AZURE.NOTE] L'esecuzione di una query di alcune proprietà di un'entità è un'operazione più efficiente rispetto al recupero di tutte le proprietà.

## Eliminare un'entità
È possibile eliminare facilmente un'entità dopo averla recuperata. Dopo il recupero dell’entità, chiamare **table\_operation::delete\_entity** con l’entità da eliminare. Quindi chiamare il metodo **cloud\_table.execute**. Il codice seguente recupera ed elimina un'entità con la chiave di partizione "Smith" e la chiave di riga "Jeff".

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

## Eliminare una tabella
L'esempio di codice seguente consente infine di eliminare una tabella dall'account di archiviazione. Una tabella eliminata non potrà essere creata nuovamente per un certo periodo di tempo.

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

## Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti seguenti per accedere ad altre informazioni su Archiviazione di Azure:

-	[Come usare l'archiviazione BLOB da C++](storage-c-plus-plus-how-to-use-blobs.md)
-	[Come usare l'archiviazione delle code da C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Elenco delle risorse di archiviazione di Azure in C++](storage-c-plus-plus-enumeration.md)
-	[Informazioni di riferimento sulla libreria client di archiviazione per C++](http://azure.github.io/azure-storage-cpp)
-	[Documentazione di Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=AcomDC_0817_2016--->