---
title: Esercitazione su Node.js per l'API SQL per Azure Cosmos DB
description: Esercitazione su Node.js che illustra come connettersi ad Azure Cosmos DB ed eseguire query usando l'API SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.openlocfilehash: ef493b6b21eb0ba0ad6d22a21e4e205a9fecacb6
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858063"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Esercitazione: Compilare un'applicazione console Node.js con l'SDK JavaScript per gestire i dati API SQL di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Gli sviluppatori possono avere applicazioni che usano dati documento NoSQL. È possibile usare un account API SQL in Azure Cosmos DB per archiviare e accedere a tali dati documento. Questa esercitazione illustra come compilare un'applicazione console Node.js per creare risorse di Azure Cosmos DB e sottoporle a query.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare e collegarsi a un account Azure Cosmos DB.
> * Con l'applicazione.
> * Creare un database.
> * Creare un contenitore
> * Aggiungere elementi al contenitore.
> * Eseguire operazioni di base sugli elementi, sul contenitore e sul database.

## <a name="prerequisites"></a>Prerequisiti 

Assicurarsi di disporre delle risorse seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 o versioni successive.

## <a name="create-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Creare prima di tutto un account Azure Cosmos DB. Se è già disponibile un account da usare, è possibile passare a [Configurare l'applicazione Node.js](#SetupNode). Se si usa l'emulatore Azure Cosmos DB, seguire la procedura descritta nell'articolo [Emulatore di Azure Cosmos DB](local-emulator.md) per configurare l'emulatore e proseguire con il passaggio [Configurare l'applicazione Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Configurare l'applicazione Node.js

Prima di iniziare la scrittura del codice per compilare l'applicazione, è possibile compilare il framework per l'applicazione. Eseguire i passaggi seguenti per configurare l'applicazione Node.js che ha il codice del framework:

1. Aprire il terminale preferito.
2. Individuare la cartella o la directory in cui si vuole salvare l'applicazione Node.js.
3. Creare file JavaScript vuoti con i comandi seguenti:

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. Creare e inizializzare un file `package.json`. Usare il comando seguente:
   * ```npm init -y```

5. Installare il modulo @azure/cosmos tramite npm. Usare il comando seguente:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Impostare le configurazioni dell'applicazione

Ora che l'applicazione esiste, è necessario assicurarsi che possa comunicare con Azure Cosmos DB. Tramite l'aggiornamento di alcune impostazioni di configurazione, come illustrato nei passaggi seguenti, è possibile impostare l'applicazione per comunicare con Azure Cosmos DB:

1. Aprire il file *config.js* nell'editor di testo che si preferisce.

1. Copiare e incollare il frammento di codice seguente nel file *config.js* e impostare le proprietà `endpoint` e `key` sull'URI dell'endpoint e sulla chiave primaria di Azure Cosmos DB. I nomi dei contenitori di database vengono impostati su **Tasks** e **Items**. La chiave di partizione che verrà usata per questa applicazione è **/category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   I dettagli su endpoint e chiave sono disponibili nel riquadro **Chiavi** del [portale di Azure](https://portal.azure.com).

   ![Screenshot di acquisizione delle chiavi dal portale di Azure][keys]

In JavaScript SDK si usano i termini generici *contenitore* ed *elemento*. Un contenitore può essere una raccolta, un grafo o una tabella. Un elemento può essere un documento, un arco/vertice o una riga ed è il contenuto all'interno di un contenitore. Nel frammento di codice precedente il codice `module.exports = config;` consente di esportare l'oggetto config, in modo che sia possibile farvi riferimento all'interno del file *app.js*.

## <a name="create-a-database-and-a-container"></a>Creare un database e un contenitore

1. Aprire il file *databaseContext.js* nell'editor di testo che si preferisce.

1. Copiare e incollare il codice seguente nel file *databaseContext.js*. Questo codice consente di definire una funzione che crea il database "Tasks", "Items" e il contenitore, se non esistono già nell'account Azure Cosmos:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Un database è un contenitore logico di elementi partizionati tra contenitori. Per creare un database, usare `createIfNotExists` o la funzione create della classe **Databases**. Un contenitore è costituito da elementi, che nel caso dell'API SQL sono documenti JSON. Per creare un contenitore, usare `createIfNotExists` o la funzione create della classe **Containers**. Dopo aver creato un contenitore, è possibile archiviare i dati ed eseguirvi query.

   > [!WARNING]
   > La creazione di un contenitore ha implicazioni in termini di prezzi. Visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) per sapere cosa aspettarsi.

## <a name="import-the-configuration"></a>Importare la configurazione

1. Aprire il file *app.js* nell'editor di testo che si preferisce.

1. Copiare e incollare il codice seguente per importare il modulo `@azure/cosmos`, la configurazione e l'oggetto databaseContext definito nei passaggi precedenti. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Connettersi all'account Azure Cosmos

Nel file *app.js* copiare e incollare il codice seguente per usare l'endpoint e la chiave salvati in precedenza per creare un nuovo oggetto CosmosClient.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Se ci si connette all'**emulatore di Cosmos DB**, disabilitare la verifica TLS per il processo del nodo:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Ora che è disponibile il codice per inizializzare il client Azure Cosmos DB, è possibile esaminare come usare le risorse di Azure Cosmos DB.

## <a name="query-items"></a><a id="QueryItem"></a>Eseguire query sugli elementi

Azure Cosmos DB supporta l'esecuzione di query complesse sugli elementi JSON archiviati in ogni contenitore. Il codice di esempio seguente mostra una query che è possibile eseguire sugli elementi nel contenitore. È possibile eseguire una query sugli elementi usando la funzione query della classe `Items`. Aggiungere il codice seguente al file *app.js* per eseguire una query sugli elementi dell'account Azure Cosmos:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Creare un elemento

Per creare un elemento, è possibile usare la funzione create della classe `Items`. Quando si usa l'API SQL, gli elementi vengono proiettati come documenti, ovvero contenuti JSON definiti dall'utente (arbitrario). In questa esercitazione viene creato un nuovo elemento nel database Tasks.

1. Nel file app.js impostare la definizione dell'elemento:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Aggiungere il codice seguente per creare l'elemento definito in precedenza:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Aggiornare un elemento

Azure Cosmos DB supporta la sostituzione del contenuto degli elementi. Copiare e incollare il codice seguente nel file *app.js*. Questo codice consente di recuperare un elemento dal contenitore e aggiorna il campo *isComplete* impostandolo su true.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Eliminare un elemento

Azure Cosmos DB supporta l'eliminazione di elementi JSON. Il codice seguente illustra come recuperare un elemento in base al relativo ID ed eliminarlo. Copiare e incollare il codice seguente nel file *app.js*:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Eseguire l'applicazione Node.js

Il codice finale avrà un aspetto simile al seguente:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

Nel terminale trovare il file ```app.js``` ed eseguire il comando:

```bash 
node app.js
```

Verrà visualizzato l'output dell'app introduttiva. L'output dovrebbe essere analogo al testo di esempio seguente.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>Ottenere la soluzione completa per l'esercitazione su Node.js

Se non si ha tempo per completare le procedure dell'esercitazione o se si vogliono solo scaricare gli esempi di codice, è possibile ottenerli da [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Per eseguire la soluzione Guida introduttiva contenente tutto il codice riportato in questo articolo, è necessario avere:

* Un [account Azure Cosmos DB][create-account].
* La soluzione [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) disponibile su GitHub.

Installare le dipendenze del progetto tramite npm. Usare il comando seguente:

* ```npm install``` 

Successivamente, nel file ```config.js```, aggiornare i valori config.endpoint e config.key come illustrato nel [Passaggio 3: Impostare le configurazioni dell'applicazione](#Config).  

Nel terminale trovare quindi il file ```app.js``` ed eseguire il comando:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando tali risorse non servono più, è possibile eliminare il gruppo di risorse, l'account Azure Cosmos DB e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse usato per l'account Azure Cosmos DB, selezionare **Elimina**, quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitorare un account Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
