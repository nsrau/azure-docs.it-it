---
title: Come usare il servizio Tabella di archiviazione di Azure o l'API del servizio tabelle di Azure Cosmos DB da PHP | Microsoft Docs
description: Informazioni su come usare l'API del servizio tabelle da PHP per creare ed eliminare una tabella e per inserire, eliminare ed eseguire query sulla tabella.
services: cosmos-db
documentationcenter: php
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/22/2018
ms.author: mimig
ms.openlocfilehash: 64ce8206b4b0ab3c831417d6e478f9c059188345
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="how-to-use-azure-storage-table-service-or-cosmos-db-table-api-from-php"></a>Come usare il servizio Tabella di archiviazione di Azure o l'API del servizio tabelle di Cosmos DB da PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Panoramica
Questo documento illustra come eseguire scenari comuni con il servizio Tabella di archiviazione di Azure e l'API del servizio tabelle di Azure Cosmos DB. Gli esempi sono scritti in PHP e usano la [libreria client PHP per Tabella di archiviazione di Azure][download]. Gli scenari presentati includono operazioni di **creazione ed eliminazione di una tabella**, nonché di **inserimento, eliminazione ed esecuzione di query sulle entità di una tabella**. Per altre informazioni sul servizio tabelle di Azure, vedere la sezione [Passaggi successivi](#next-steps) .

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Creare un account del servizio di Azure

È possibile usare le tabelle con l'archiviazione tabelle di Azure o l'API del servizio tabelle di Azure Cosmos DB. Per altre informazioni sulle differenze tra i servizi, vedere [Offerte per tabelle](table-introduction.md#table-offerings). Sarà necessario creare un account per il servizio che si intende usare. 

### <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Il modo più semplice per creare il primo account di archiviazione è tramite il [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account).

È possibile creare un account di archiviazione anche con [Azure PowerShell](../storage/common/storage-powershell-guide-full.md) o tramite l'[interfaccia della riga di comando di Azure](../storage/common/storage-azure-cli.md).

Se si preferisce non creare un account di archiviazione in questa fase, è anche possibile usare l'emulatore di archiviazione di Azure per eseguire e testare il codice in un ambiente locale. Per altre informazioni, vedere [Usare l'emulatore di archiviazione di Azure per sviluppo e test](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Per istruzioni sulla creazione di un account di Azure Cosmos DB, vedere [Creare un account dell'API di tabella](create-table-dotnet.md#create-a-database-account).

## <a name="create-a-php-application"></a>Creare un'applicazione PHP

L'unico requisito per creare un'applicazione PHP per l'accesso al servizio Tabella di archiviazione o all'API del servizio tabelle di Azure Cosmos DB è fare riferimento alle classi nell'SDK azure-storage-table per PHP all'interno del codice. Per creare l'applicazione, è possibile usare qualsiasi strumento di sviluppo, incluso il Blocco note.

In questo documento vengono usate le funzionalità del servizio Tabella di archiviazione o di Azure Cosmos DB che possono essere chiamate da un'applicazione PHP a livello locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

## <a name="get-the-client-library"></a>Ottenere la libreria client

1. Creare un file denominato composer.json nella radice del progetto e aggiungere nel file il codice seguente:
```json
{
  "require": {
    "microsoft/azure-storage-table": "*"
  }
}
```
2. Scaricare [composer.phar](http://getcomposer.org/composer.phar) nella radice. 
3. Aprire un prompt dei comandi ed eseguire il comando seguente nella radice del progetto:
```
php composer.phar install
```
In alternativa, passare alla [libreria client PHP per Tabella di archiviazione di Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) in GitHub per clonare il codice sorgente.


## <a name="add-required-references"></a>Aggiungere i riferimenti necessari
Per usare il servizio Tabella di archiviazione o le API di Azure Cosmos DB, è necessario:

* Fare riferimento al file autoloader mediante l'istruzione [require_once][require_once]
* Fare riferimento a tutte le classi usate.

L'esempio seguente illustra come includere il file autoloader e fare riferimento alla classe **TableRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

Negli esempi seguenti viene mostrata sempre l'istruzione `require_once` , ma si fa riferimento solo alle classi necessarie per l'esecuzione dell'esempio.

## <a name="add-a-storage-table-service-connection"></a>Aggiungere una connessione al servizio Tabella di archiviazione
Per creare un'istanza di un client del servizio Tabella di archiviazione, è necessario avere prima una stringa di connessione valida. Di seguito è riportato il formato della stringa di connessione al servizio Tabella di archiviazione:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>Aggiungere una connessione ad Azure Cosmos DB
Per creare un'istanza di un client del servizio tabelle di Azure Cosmos DB, è necessario avere prima una stringa di connessione valida. Di seguito è riportato il formato della stringa di connessione ad Azure Cosmos DB:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>Aggiungere una connessione all'emulatore di archiviazione
Per accedere all'emulatore di archiviazione:

```php
UseDevelopmentStorage = true
```

Per creare un client del servizio tabelle di Azure o di Azure Cosmos DB, è necessario usare la classe **TableRestProxy**. È possibile:

* passare la stringa di connessione direttamente a essa o
* usare **CloudConfigurationManager (CCM)** per cercare la stringa di connessione in più origini esterne:
  * per impostazione predefinita viene fornito con il supporto per un'origine esterna - ovvero le variabili ambientali
  * È possibile aggiungere nuove origini estendendo la classe `ConnectionStringSource`.

Per gli esempi illustrati in questo articolo, la stringa di connessione viene passata direttamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Creare una tabella
Un oggetto **TableRestProxy** consente di creare una tabella usando il metodo **createTable**. Durante la creazione di una tabella, è possibile impostare il timeout del servizio tabelle. Per altre informazioni sul timeout del servizio tabelle, vedere [Setting Timeouts for Table Service Operations][table-service-timeouts] (Impostazione di timeout per le operazioni del servizio tabelle).

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Per informazioni sulle restrizioni ai nomi delle tabelle, vedere [Understanding the Table Service Data Model][table-data-model] (Informazioni sul modello di dati del servizio tabelle).

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella
Per aggiungere un'entità a una tabella, creare un nuovo oggetto **Entity** e passarlo a **TableRestProxy->insertEntity**. Si noti che durante la creazione di un'entità, è necessario specificare le chiavi `PartitionKey` e `RowKey`. Si tratta di identificatori univoci dell'entità e sono valori che possono essere interrogati molto più velocemente rispetto ad altre proprietà dell'entità. Il sistema usa `PartitionKey` per distribuire automaticamente le entità della tabella in molti nodi di archiviazione. Le entità con lo stesso `PartitionKey` vengono archiviate nello stesso nodo. Operazioni su più entità archiviate nello stesso nodo vengono eseguite più efficacemente che non su entità archiviate in nodi diversi. `RowKey` è l'ID univoco di un'entità all'interno di una partizione.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Per informazioni sulle proprietà e i tipi di tabelle, vedere [Understanding the Table Service Data Model][table-data-model] (Informazioni sul modello di dati del servizio tabelle).

La classe **TableRestProxy** offre due metodi alternativi per l'inserimento di entità: **insertOrMergeEntity** e **insertOrReplaceEntity**. Per utilizzare questi metodi, creare una nuova **Entity** e passarla come parametro a uno dei due metodi. Ogni metodo inserirà l'entità se non esiste già. Se l'entità esiste già, **insertOrMergeEntity** aggiornerà i valori delle proprietà esistenti e aggiungerà nuove proprietà se non esistono, mentre **insertOrReplaceEntity** sostituirà completamente un'entità esistente. Nell'esempio seguente viene illustrato come utilizzare **insertOrMergeEntity**. Se l'entità con `PartitionKey` "tasksSeattle" e `RowKey` "1" non esiste già, verrà inserita. Tuttavia, se è già stata inserita (come illustrato nell'esempio precedente), viene aggiornata la proprietà `DueDate` e viene aggiunta la proprietà `Status`. Verranno aggiornate anche le proprietà `Description` e `Location`, ma con valori che non apporteranno alcuna modifica. Se queste due ultime proprietà non sono state aggiunte come illustrato nell'esempio, ma erano disponibili nell'entità di destinazione, i loro valori esistenti non subiranno alcuna modifica.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

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

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Recuperare una singola entità
Il metodo **TableRestProxy->getEntity** consente di recuperare una singola entità eseguendo una query su `PartitionKey` e `RowKey`. Nell'esempio seguente la chiave di partizione `tasksSeattle` e la chiave di riga `1` vengono passate al metodo **getEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperare tutte le entità di una partizione
Le query di entità vengono create usando i filtri. Per altre informazioni, vedere [Querying Tables and Entities][filters] (Query di tabelle ed entità). Per recuperare tutte le entità in una partizione usare il filtro "PartitionKey eq *partition_name*". Nell'esempio seguente viene illustrato come recuperare tutte le entità nella partizione `tasksSeattle` passando un filtro al metodo **queryEntities** .

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Recuperare un subset di entità in una partizione
Lo stesso modello applicato nell'esempio precedente può essere usato per recuperare un subset di entità in una partizione. Il subset di entità recuperato viene determinato dal filtro usato. Per altre informazioni, vedere [Querying Tables and Entities][filters] (Query di tabelle ed entità). L'esempio seguente illustra come usare un filtro per recuperare tutte le entità con un valore `Location` specifico e un valore `DueDate` precedente a una data specificata.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Recuperare un subset di proprietà di entità
È possibile recuperare un subset di proprietà di entità eseguendo una query. Questa tecnica, denominata *proiezione*, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Per specificare una proprietà da recuperare, passare il nome della proprietà al metodo **Query->addSelectField**. Per aggiungere altre proprietà, è possibile chiamare questo metodo più volte. Dopo l'esecuzione di **TableRestProxy->queryEntities**, per le entità restituite saranno presenti solo le proprietà selezionate. Se si desidera restituire un subset di entità di tabella, utilizzare un filtro come illustrato nelle query precedenti.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
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
```

## <a name="update-an-entity"></a>Aggiornare un'entità
È possibile aggiornare un'entità esistente usando i metodi **Entity->setProperty** ed **Entity->addProperty** sull'entità e quindi chiamando **TableRestProxy->updateEntity**. Nell'esempio seguente viene recuperata un'entità, modificata una proprietà, rimossa un'altra proprietà e aggiunta una nuova proprietà. Si noti che per rimuovere una proprietà, è necessario impostarne il valore su **Null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Eliminare un'entità
Per eliminare un'entità passare il nome della tabella e le chiavi `PartitionKey` e `RowKey` dell'entità al metodo **TableRestProxy->deleteEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Ai fini dei controlli di concorrenza, è possibile impostare il valore Etag di un'entità da eliminare usando il metodo **DeleteEntityOptions->setEtag** e passando l'oggetto **DeleteEntityOptions** a **deleteEntity** come quarto parametro.

## <a name="batch-table-operations"></a>Operazioni batch su tabella
Il metodo **TableRestProxy->batch** consente di eseguire più operazioni in una sola richiesta. In questo caso è necessario aggiungere le operazioni all'oggetto **BatchRequest** e quindi passare l'oggetto **BatchRequest** al metodo **TableRestProxy->batch**. Per aggiungere un'operazione all'oggetto **BatchRequest** , è possibile chiamare più volte uno dei metodi seguenti:

* **addInsertEntity** (per aggiungere un'operazione insertEntity)
* **addUpdateEntity** (per aggiungere un'operazione updateEntity)
* **addMergeEntity** (per aggiungere un'operazione mergeEntity)
* **addInsertOrReplaceEntity** (per aggiungere un'operazione insertOrReplaceEntity)
* **addInsertOrMergeEntity** (per aggiungere un'operazione insertOrMergeEntity)
* **addDeleteEntity** (per aggiungere un'operazione deleteEntity)

L'esempio seguente illustra come eseguire le operazioni **insertEntity** e **deleteEntity** in una sola richiesta. 

> [!NOTE]
> Azure Cosmos DB non supporta ancora le operazioni batch per le tabelle. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

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

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Per altre informazioni su operazioni batch su tabelle, vedere [Performing Entity Group Transactions][entity-group-transactions] (Esecuzione di transazioni di gruppi di entità).

## <a name="delete-a-table"></a>Eliminare una tabella
Infine, per eliminare una tabella, passare il nome della tabella al metodo **TableRestProxy->deleteTable**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base sul servizio tabelle di Azure e su Azure Cosmos DB, usare i collegamenti seguenti per visualizzare altre informazioni.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.

* [Centro sviluppatori PHP](/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: http://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
