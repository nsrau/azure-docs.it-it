---
title: Come usare l'archiviazione tabelle da PHP | Microsoft Docs
description: Informazioni su come usare il Servizio tabelle da PHP per creare ed eliminare una tabella e per inserire, eliminare ed eseguire query su tabelle.
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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 7fa82875ba823f1a4a9a886d4f699ca6757c3a3b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-use-azure-table-storage-from-php"></a>Come usare l'Archiviazione tabelle di Azure da PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Panoramica
Questa guida illustra come eseguire scenari comuni con il servizio tabelle di Azure. Gli esempi sono scritti in PHP e usano [Azure SDK per PHP][download]. Gli scenari presentati includono **creazione ed eliminazione di una tabella, inserimento ed eliminazione di entità ed esecuzione di query sulle entità in una tabella**. Per altre informazioni sul servizio tabelle di Azure, vedere la sezione [Passaggi successivi](#next-steps) .

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Creare un'applicazione PHP
Per creare un'applicazione PHP che accede al Servizio tabelle di Azure, è sufficiente fare riferimento alle classi in Azure SDK per PHP dall'interno del codice. Per creare l'applicazione, è possibile usare qualsiasi strumento di sviluppo, incluso il Blocco note.

In questa guida vengono usate le funzionalità del servizio tabelle che possono essere chiamate da un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

## <a name="get-the-azure-client-libraries"></a>Acquisire le librerie client di Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Configurare l'applicazione per accedere al Servizio tabelle
Per utilizzare le API del Servizio tabelle di Azure, è necessario:

1. Fare riferimento al file autoloader mediante l'istruzione [require_once][require_once]
2. Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe **ServicesBuilder** .

> [!NOTE]
> Gli esempi in questo articolo presuppongono che siano state installate le librerie client PHP per Azure tramite Composer. Se le librerie sono state installate manualmente, sarà necessario fare riferimento al file autoloader <code>WindowsAzure.php</code> .
>
>

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Negli esempi seguenti viene mostrata sempre l'istruzione `require_once` , ma si fa riferimento solo alle classi necessarie per l'esecuzione dell'esempio.

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure
Per creare un'istanza di un client del servizio tabelle di Azure, è necessario avere prima una stringa di connessione valida. Il formato della stringa di connessione del servizio tabelle è:

Per accedere a un servizio attivo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Per accedere alla memoria dell'emulatore:

```php
UseDevelopmentStorage=true
```

Per creare un client di servizio di Azure, è necessario usare la classe **ServicesBuilder** . È possibile:

* passare la stringa di connessione direttamente a essa o
* utilizzare **CloudConfigurationManager (CCM)** per cercare la stringa di connessione in più origini esterne:
  * per impostazione predefinita, viene fornito con il supporto per un'origine esterna, ovvero le variabili ambientali
  * è possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource**

Per gli esempi illustrati in questo articolo, la stringa di connessione verrà passata direttamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
```

## <a name="create-a-table"></a>Creare una tabella
Un oggetto **TableRestProxy** consente di creare una tabella usando il metodo **createTable**. Durante la creazione di una tabella, è possibile impostare il timeout del servizio tabelle. Per altre informazioni sul timeout del servizio tabelle, vedere [Setting Timeouts for Table Service Operations][table-service-timeouts] (Impostazione di timeout per le operazioni del servizio tabelle).

```php
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Create table.
    $tableRestProxy->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
}
```

Per informazioni sulle restrizioni ai nomi delle tabelle, vedere [Understanding the Table Service Data Model][table-data-model] (Informazioni sul modello di dati del servizio tabelle).

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella
Per aggiungere un'entità a una tabella, creare un nuovo oggetto **Entity** e passarlo a **TableRestProxy->insertEntity**. Si noti che durante la creazione di un'entità, è necessario specificare le chiavi `PartitionKey` e `RowKey`. Si tratta di identificatori univoci dell'entità e sono valori che possono essere interrogati molto più velocemente rispetto ad altre proprietà dell'entità. Il sistema usa `PartitionKey` per distribuire automaticamente le entità della tabella su molti nodi di archiviazione. Le entità con lo stesso `PartitionKey` vengono archiviate nello stesso nodo. Operazioni su più entità archiviate nello stesso nodo vengono eseguite più efficacemente che non su entità archiviate in nodi diversi. `RowKey` è l'ID univoco di un'entità all'interno di una partizione.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

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
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Per informazioni sulle proprietà e i tipi di tabelle, vedere [Understanding the Table Service Data Model][table-data-model] (Informazioni sul modello di dati del servizio tabelle).

La classe **TableRestProxy** offre due metodi alternativi per l'inserimento di entità: **insertOrMergeEntity** e **insertOrReplaceEntity**. Per utilizzare questi metodi, creare una nuova **Entity** e passarla come parametro a uno dei due metodi. Ogni metodo inserirà l'entità se non esiste già. Se l'entità esiste già, **insertOrMergeEntity** aggiornerà i valori delle proprietà esistenti e aggiungerà nuove proprietà se non esistono, mentre **insertOrReplaceEntity** sostituirà completamente un'entità esistente. Nell'esempio seguente viene illustrato come utilizzare **insertOrMergeEntity**. Se l'entità con `PartitionKey` "tasksSeattle" e `RowKey` "1" non esiste già, verrà inserita. Se è stata inserita in precedenza (come illustrato nell'esempio precedente), la proprietà `DueDate` verrà aggiornata e verrà aggiunta la proprietà `Status`. Verranno aggiornate anche le proprietà `Description` e `Location`, ma con valori che non apporteranno alcuna modifica. Se queste due ultime proprietà non sono state aggiunte come illustrato nell'esempio, ma erano disponibili nell'entità di destinazione, i loro valori esistenti non subiranno alcuna modifica.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

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

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableRestProxy->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Recuperare una singola entità
Il metodo **TableRestProxy->getEntity** consente di recuperare una singola entità eseguendo una query su `PartitionKey` e `RowKey`. Nell'esempio seguente la chiave di partizione `tasksSeattle` e la chiave di riga `1` vengono passate al metodo **getEntity**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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
È possibile recuperare un subset di proprietà di entità eseguendo una query. Questa tecnica, denominata *proiezione*, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Per specificare la proprietà da recuperare, passare il nome della proprietà al metodo **Query->addSelectField**. Per aggiungere altre proprietà, è possibile chiamare questo metodo più volte. Dopo l'esecuzione di **TableRestProxy->queryEntities**, per le entità restituite saranno presenti solo le proprietà selezionate. Se si desidera restituire un subset di entità di tabella, utilizzare un filtro come illustrato nelle query precedenti.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableRestProxy->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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
È possibile aggiornare un'entità esistente usando i metodi **Entity->setProperty** e **Entity->addProperty** sull'entità e quindi chiamando **TableRestProxy->updateEntity**. Nell'esempio seguente viene recuperata un'entità, modificata una proprietà, rimossa un'altra proprietà e aggiunta una nuova proprietà. Si noti che per rimuovere una proprietà, è necessario impostarne il valore su **Null**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();

$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

$entity->setPropertyValue("Location", null); //Removed Location.

$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableRestProxy->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Eliminare un'entità
Per eliminare un'entità passare il nome della tabella e le chiavi `PartitionKey` e `RowKey` dell'entità al metodo **TableRestProxy->deleteEntity**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete entity.
    $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Si noti che per effettuare controlli di concorrenza è possibile impostare il valore Etag per un'entità da eliminare usando il metodo **DeleteEntityOptions->setEtag** e passando l'oggetto **DeleteEntityOptions** a **deleteEntity** come quarto parametro.

## <a name="batch-table-operations"></a>Operazioni batch su tabella
Il metodo **TableRestProxy->batch** consente di eseguire più operazioni in una sola richiesta. In questo caso è necessario aggiungere le operazioni all'oggetto **BatchRequest** e quindi passare l'oggetto **BatchRequest** al metodo **TableRestProxy->batch**. Per aggiungere un'operazione all'oggetto **BatchRequest** , è possibile chiamare più volte uno dei metodi seguenti:

* **addInsertEntity** (per aggiungere un'operazione insertEntity)
* **addUpdateEntity** (per aggiungere un'operazione updateEntity)
* **addMergeEntity** (per aggiungere un'operazione mergeEntity)
* **addInsertOrReplaceEntity** (per aggiungere un'operazione insertOrReplaceEntity)
* **addInsertOrMergeEntity** (per aggiungere un'operazione insertOrMergeEntity)
* **addDeleteEntity** (per aggiungere un'operazione deleteEntity)

L'esempio seguente illustra come eseguire le operazioni **insertEntity** e **deleteEntity** in una sola richiesta:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

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

try    {
    $tableRestProxy->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete table.
    $tableRestProxy->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo avere appreso le nozioni di base del servizio tabelle di Azure, usare i collegamenti seguenti per altre informazioni su attività di archiviazione più complesse.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.

* [Centro sviluppatori PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
