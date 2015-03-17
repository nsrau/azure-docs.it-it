<properties 
	pageTitle="Come usare il servizio di archiviazione tabelle (PHP) | Microsoft Azure" 
	description="Informazioni su come usare il Servizio tabelle da PHP per creare ed eliminare una tabella e per inserire, eliminare ed eseguire query su tabelle." 
	services="storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>

# Come usare il Servizio tabelle da PHP

In questa guida verranno illustrati diversi scenari di uso comuni del Servizio tabelle di Azure. Gli esempi sono scritti in PHP e usano [Azure SDK per PHP][download]. Gli scenari presentati includono **creazione ed eliminazione di una tabella, inserimento ed eliminazione di entità ed esecuzione di query sulle entità in una tabella**. Per altre informazioni sul Servizio tabelle di Azure, vedere la sezione [Passaggi successivi](#NextSteps) .

##Sommario

* [Informazioni sul servizio tabelle](#what-is)
* [Concetti](#concepts)
* [Creare un account di archiviazione di Azure](#CreateAccount)
* [Creare un'applicazione PHP](#CreateApplication)
* [Configurare l'applicazione per accedere al Servizio tabelle](#ConfigureStorage)
* [Configurare una connessione di Archiviazione di Azure](#ConnectionString)
* [Procedura: Creare una tabella](#CreateTable)
* [Procedura: Aggiungere un'entità a una tabella](#AddEntity)
* [Procedura: Recuperare una singola entità](#RetrieveEntity)
* [Procedura: Recuperare tutte le entità di una partizione](#RetEntitiesInPartition)
* [Procedura: Recuperare un subset di entità in una partizione](#RetrieveSubset)
* [Procedura: Recuperare un subset di proprietà di entità](#RetPropertiesSubset)
* [Procedura: Aggiornare un'entità](#UpdateEntity)
* [Procedura: Operazioni batch su tabella](#BatchOperations)
* [Procedura: Eliminare una tabella](#DeleteTable)
* [Passaggi successivi](#NextSteps)

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a id="CreateAccount"></a>Creare un account di archiviazione di Azure

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

##<a id="CreateApplication"></a>Creare un'applicazione PHP

Per creare un'applicazione PHP che accede al Servizio tabelle di Azure, è sufficiente fare riferimento alle classi in Azure SDK per PHP dall'interno del codice. Per creare l'applicazione, è possibile usare qualsiasi strumento di sviluppo, incluso il Blocco note.

In questa guida si useranno le funzionalità del Servizio tabelle che possono essere chiamate da un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

##<a id="GetClientLibrary"></a>Acquisire le librerie client di Azure

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureStorage"></a>Configurare l'applicazione per accedere al Servizio tabelle

Per usare le API del Servizio tabelle di Azure, è necessario:

1. Fare riferimento al file autoloader mediante l'istruzione [require_once][require_once] e
2. Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe **ServicesBuilder**.

> [AZURE.NOTE]
> In questo esempio (e in altri esempi in questo articolo) si presuppone che siano state installate le librerie client PHP per Azure tramite Composer. Se le librerie sono state installate manualmente o come pacchetto PEAR, sarà necessario fare riferimento al file autoloader <code>WindowsAzure.php</code>.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Negli esempi seguenti, l'istruzione `require_once` verrà sempre visualizzata, ma si farà riferimento solo alle classi necessarie per eseguire l'esempio.

##<a id="ConnectionString"></a>Configurare una connessione di archiviazione di Azure

Per creare un'istanza di un client del Servizio tabelle di Azure, è necessario innanzitutto disporre di una stringa di connessione valida. Il formato della stringa di connessione del Servizio tabelle è:

Per accedere a un servizio attivo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Per accedere alla memoria dell'emulatore:

	UseDevelopmentStorage=true


Per creare un client di servizio di Azure, è necessario usare la classe **ServicesBuilder**. È possibile:

* passare la stringa di connessione direttamente a essa o
* usare **CloudConfigurationManager (CCM)** per cercare la stringa di connessione in più origini esterne:
	* per impostazione predefinita viene fornito con il supporto per un'origine esterna, ovvero le variabili ambientali
	* è possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource**

Per gli esempi illustrati in questo articolo, la stringa di connessione verrà passata direttamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


##<a id="CreateTable"></a>Procedura: Creare una tabella

Un oggetto **TableRestProxy** consente di creare una tabella usando il metodo **createTable**. Durante la creazione di una tabella, è possibile impostare il timeout del Servizio tabelle. Per altre informazioni sul timeout del Servizio tabelle, vedere [Impostazione di timeout per operazioni del servizio tabelle][table-service-timeouts].

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

	try	{
		// Create table.
		$tableRestProxy->createTable("mytable");
	}
	catch(ServiceException $e){
		$code = $e->getCode();
		$error_message = $e->getMessage();
		// Handle exception based on error codes and messages.
		// Error codes and messages can be found here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
	}

Per informazioni sulle restrizioni ai nomi delle tabelle, vedere [Informazioni sul modello di dati del servizio tabelle][table-data-model].

##<a id="AddEntity"></a>Procedura: Aggiungere un'entità a una tabella

Per aggiungere un'entità a una tabella, creare un nuovo oggetto **Entity** e passarlo a **TableRestProxy->insertEntity**. Si noti che durante la creazione di un'entità è necessario specificare le chiavi `PartitionKey` e `RowKey`. Si tratta di identificatori univoci dell'entità e sono valori che possono essere interrogati molto più velocemente rispetto ad altre proprietà dell'entità. Il sistema usa `PartitionKey` per distribuire automaticamente le entità della tabella su molti nodi di archiviazione. Le entità con lo stesso oggetto `PartitionKey` vengono archiviate nello stesso nodo. Operazioni su più entità archiviate sullo stesso nodo verranno eseguite più efficacemente che non su entità archiviate in nodi diversi. L'oggetto `RowKey` è l'ID univoco di un'entità all'interno di una partizione.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$entity = new Entity();
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", 
						 EdmType::DATETIME, 
						 new DateTime("2012-11-05T08:15:00-08:00"));
	$entity->addProperty("Location", EdmType::STRING, "Home");
	
	try{
		$tableRestProxy->insertEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
	}

Per informazioni sulle proprietà e i tipi di tabelle, vedere [Informazioni sul modello di dati del servizio tabelle][table-data-model].

La classe **TableRestProxy** offre due metodi alternativi per l'inserimento delle entità: **insertOrMergeEntity** e **insertOrReplaceEntity**. Per usare questi metodi, creare una nuova **Entity** e passarla come parametro a uno dei due metodi. Ogni metodo inserirà l'entità se non esiste già. Se l'entità esiste già, **insertOrMergeEntity** aggiornerà i valori delle proprietà esistenti e aggiungerà nuove proprietà se non esistono, mentre **insertOrReplaceEntity** sostituirà completamente un'entità esistente. Nell'esempio seguente viene illustrato come usare **insertOrMergeEntity**. Se l'entità con `PartitionKey` "tasksSeattle" e `RowKey` "1" non esiste già, verrà inserita. Se è stata inserita in precedenza (come illustrato nell'esempio precedente), la proprietà `DueDate` verrà aggiornata e verrà aggiunta la proprietà `Status`. Verranno aggiornate anche le proprietà `Description` e `Location`, ma con valori che non apporteranno alcuna modifica. Se queste due ultime proprietà non sono state aggiunte come illustrato nell'esempio, ma erano disponibili nell'entità di destinazione, i loro valori esistenti non subiranno alcuna modifica.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	//Create new entity.
	$entity = new Entity();
	
	// PartitionKey and RowKey are required.
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	
	// If entity exists, existing properties are updated with new values and
	// new properties are added. Missing properties are unchanged.
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
	$entity->addProperty("Location", EdmType::STRING, "Home");
	$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.
	
	try	{
		// Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
		// would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
		$tableRestProxy->insertOrMergeEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	   

##<a id="RetrieveEntity"></a>Procedura: Recuperare una singola entità

Il metodo **TableRestProxy->getEntity** consente di recuperare una singola entità eseguendo una query su `PartitionKey` e `RowKey`. Nell'esempio seguente, la chiave di partizione `tasksSeattle` e la chiave di riga "1" vengono passate al metodo **getEntity**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entity = $result->getEntity();

	echo $entity->getPartitionKey().":".$entity->getRowKey();

##<a id="RetEntitiesInPartition"></a>Procedura: Recuperare tutte le entità di una partizione

Le query di entità vengono create usando filtri (per altre informazioni, vedere [Query di tabelle ed entità][filters]). Per recuperare tutte le entità in una partizione, usare il filtro "PartitionKey eq *partition_name*". Nell'esempio seguente viene illustrato come recuperare tutte le entità nella partizione `tasksSeattle` passando un filtro al metodo **queryEntities**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "PartitionKey eq 'tasksSeattle'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

##<a id="RetrieveSubset"></a>Procedura: Recuperare un subset di entità in una partizione

Lo stesso modello applicato nell'esempio precedente può essere usato per recuperare un subset di entità in una partizione. Il subset di entità recuperato sarà determinato dal filtro usato (per altre informazioni, vedere [Query di tabelle ed entità][filtri]). Nell'esempio seguente viene illustrato come usare un filtro per recuperare tutte le entità con un valore `Location` specifico e `DueDate` precedente a una data specificata.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

##<a id="RetPropertiesSubset"></a>Procedura: Recuperare un subset di proprietà di entità

È possibile recuperare un subset di proprietà di entità eseguendo una query. Questa tecnica, denominata *proiezione*, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Per specificare la proprietà da recuperare, passare il nome della proprietà al metodo **Query->addSelectField**. Per aggiungere altre proprietà, è possibile chiamare questo metodo più volte. Dopo l'esecuzione di **TableRestProxy->queryEntities**, per le entità restituite saranno presenti solo le proprietà selezionate. Se si desidera restituire un subset di entità di tabella, usare un filtro come illustrato nelle query precedenti.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\QueryEntitiesOptions;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$options = new QueryEntitiesOptions();
	$options->addSelectField("Description");
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	// All entities in the table are returned, regardless of whether 
	// they have the Description field.
	// To limit the results returned, use a filter.
	$entities = $result->getEntities();

	foreach($entities as $entity){
		$description = $entity->getProperty("Description")->getValue();
		echo $description."<br />";
	}

##<a id="UpdateEntity"></a>Procedura: Aggiornare un'entità

È possibile aggiornare un'entità esistente usando i metodi **Entity->setProperty** e **Entity->addProperty** e sull'entità e quindi chiamando **TableRestProxy->updateEntity**. Nell'esempio seguente viene recuperata un'entità, modificata una proprietà, rimossa un'altra proprietà e aggiunta una nuova proprietà. Si noti che la rimozione di una proprietà è effettuata impostando il suo valore su **null**. 

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	
	$entity = $result->getEntity();
	
	$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
	
	$entity->setPropertyValue("Location", null); //Removed Location.
	
	$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

	try	{
		$tableRestProxy->updateEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="DeleteEntity"></a>Procedura: Eliminare un'entità

Per eliminare un'entità, passare il nome della tabella e le chiavi `PartitionKey` e `RowKey` dell'entità al metodo **TableRestProxy->deleteEntity**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete entity.
		$tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Si noti che per effettuare controlli di concorrenza è possibile impostare il valore Etag per un'entità da eliminare usando il metodo **DeleteEntityOptions->setEtag** e passando l'oggetto **DeleteEntityOptions** a **deleteEntity** come quarto parametro.

##<a id="BatchOperations"></a>Procedura: Operazioni batch su tabella

Il metodo **TableRestProxy->batch** consente di eseguire più operazioni in una sola richiesta. In questo caso, è necessario aggiungere operazioni all'oggetto **BatchRequest** e quindi passare l'oggetto **BatchRequest** al metodo **TableRestProxy->batch**. Per aggiungere un'operazione all'oggetto **BatchRequest**, è possibile chiamare più volte uno dei metodi seguenti:

* **addInsertEntity** (per aggiungere un'operazione insertEntity)
* **addUpdateEntity** (per aggiungere un'operazione updateEntity)
* **addMergeEntity** (per aggiungere un'operazione mergeEntity)
* **addInsertOrReplaceEntity** (per aggiungere un'operazione insertOrReplaceEntity)
* **addInsertOrMergeEntity** (per aggiungere un'operazione insertOrMergeEntity)
* **addDeleteEntity** (per aggiungere un'operazione deleteEntity)

Nell'esempio seguente viene illustrato come eseguire le operazioni **insertEntity** e **deleteEntity** in una sola richiesta:

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;
	use WindowsAzure\Table\Models\BatchOperations;

 	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	// Create list of batch operation.
	$operations = new BatchOperations();
	
	$entity1 = new Entity();
	$entity1->setPartitionKey("tasksSeattle");
	$entity1->setRowKey("2");
	$entity1->addProperty("Description", null, "Clean roof gutters.");
	$entity1->addProperty("DueDate", 
						  EdmType::DATETIME, 
						  new DateTime("2012-11-05T08:15:00-08:00"));
	$entity1->addProperty("Location", EdmType::STRING, "Home");
	
	// Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

	// Add operation to list of batch operations.
	$operations->addDeleteEntity("mytable", "tasksSeattle", "1");
	
	try	{
		$tableRestProxy->batch($operations);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Per altre informazioni su operazioni batch su tabella, vedere [Esecuzione di transazioni di gruppi di entità][entity-group-transactions].

##<a id="DeleteTable"></a>Procedura: Eliminare una tabella

Infine, per eliminare una tabella, passare il nome della tabella al metodo **TableRestProxy->deleteTable**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete table.
		$tableRestProxy->deleteTable("mytable");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="NextSteps"></a>Passaggi successivi

A questo punto, dopo avere appreso le nozioni di base del Servizio tabelle di Azure, visitare i collegamenti seguenti per altre informazioni su come eseguire attività di archiviazione più complesse.

- Vedere le informazioni di riferimento in MSDN: [Archiviazione] []
- Blog del team di Archiviazione di Azure: <http://blogs.msdn.com/b/windowsazurestorage/>

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Archiviazione]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/windowsazure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/windowsazure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/windowsazure/dd894038.aspx
<!--HONumber=42-->
