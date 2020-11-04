---
title: Creare un'applicazione .NET Framework o Core di Azure Cosmos DB tramite l'API Gremlin
description: Presenta un esempio di codice .NET Framework/Core che permette di connettersi ad Azure Cosmos DB ed eseguire query sul servizio
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: jasonh
ms.custom: devx-track-dotnet
ms.openlocfilehash: 816a16b34c16b2ccc95cbe57aec482b8d3e13fc6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099760"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Avvio rapido: Creare un'applicazione .NET Framework o Core usando l'account dell'API Gremlin di Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Console Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave-valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questo avvio rapido illustra come creare un account, un database e un grafo (contenitore) dell'[API Gremlin](graph-introduction.md) in Azure Cosmos DB tramite il portale di Azure. In seguito, si crea e si esegue un'app console basata sul driver open source [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet).  

## <a name="prerequisites"></a>Prerequisiti

Se Visual Studio 2019 non è ancora installato, è possibile scaricare e usare la **versione gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Aggiungere un grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Per clonare un'app API Gremlin da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire un prompt dei comandi, creare una nuova cartella denominata git-samples e quindi chiudere il prompt dei comandi.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una nuova cartella in cui installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Aprire quindi Visual Studio e il file della soluzione.

5. Ripristinare i pacchetti NuGet nel progetto. Sono inclusi il driver Gremlin.Net e il pacchetto Newtonsoft.Json.


6. È anche possibile installare il driver Gremlin.Net manualmente tramite Gestione pacchetti NuGet o l'[utilità della riga di comando NuGet](/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). 

Tutti i frammenti di codice seguenti sono tratti dal file Program.cs.

* Impostare i parametri di connessione in base all'account creato in precedenza: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* I comandi Gremlin da eseguire sono elencati in un dizionario:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Creare i nuovi oggetti connessione `GremlinServer` e `GremlinClient` usando i parametri forniti in precedenza:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Eseguire ogni query Gremlin usando l'oggetto `GremlinClient` con un'attività asincrona. È possibile leggere le query Gremlin dal dizionario definito nel passaggio precedente ed eseguirle. Recuperare successivamente il risultato e leggere i valori, formattati come dizionario, usando la classe `JsonSerializer` del pacchetto Newtonsoft.Json:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Dal [portale di Azure](https://portal.azure.com/) passare all'account di database del grafo. Nella scheda **Panoramica** sono visualizzati due endpoint: 
 
   **URI .NET SDK** : questo valore viene usato quando ci si connette all'account del grafo tramite la libreria Microsoft.Azure.Graphs. 

   **Endpoint Gremlin** : questo valore viene usato quando ci si connette all'account del grafo tramite la libreria Gremlin.Net.

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="Copiare l'endpoint":::

   Per eseguire questo esempio, copiare il valore **Endpoint Gremlin** ed eliminare il numero di porta alla fine, in modo che l'URI diventi `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`. Il valore dell'endpoint dovrebbe essere simile a `testgraphacct.gremlin.cosmosdb.azure.com`

1. Passare quindi alla scheda **Chiavi** e copiare il valore **PRIMARY KEY** dal portale di Azure. 

1. Dopo aver copiato l'URI e il valore PRIMARY KEY dell'account, salvarli in una nuova variabile di ambiente nel computer locale che esegue l'applicazione. Per impostare la variabile di ambiente, aprire una finestra del prompt dei comandi ed eseguire il comando seguente. Assicurarsi di sostituire i valori <URI_account_Azure_Cosmos> e <Valore_PRIMARY_KEY_account_Azure_Cosmos>.

   ```console
   setx Host "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Aprire il file *Program.cs* e aggiornare le variabili "database" e "container" con i nomi del database e del contenitore (che è anche il nome del grafico) creati sopra.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Salvare il file Program.cs. 

L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Eseguire l'app console

Premere CTRL + F5 per eseguire l'applicazione. L'applicazione stamperà i comandi della query Gremlin e i risultati nella console.

   La finestra della console visualizza i vertici e gli archi aggiunti al grafo. Al completamento dello script, premere INVIO per chiudere la finestra della console.

## <a name="browse-using-the-data-explorer"></a>Esplorare i dati con Esplora dati

È ora possibile tornare a Esplora dati nel portale di Azure per esplorare i nuovi dati del grafo ed eseguire query su di essi.

1. In Esplora dati il nuovo database viene visualizzato nel riquadro Graph. Espandere il database e i nodi del contenitore e quindi fare clic su **Grafico**.

2. Fare clic sul pulsante **Applica filtro** per usare la query predefinita per visualizzare tutti i vertici nel grafico. I dati generati dall'app di esempio vengono visualizzati nel riquadro Graphs (Grafi).

    È possibile ingrandire o ridurre il grafico, espanderne lo spazio di visualizzazione, aggiungere altri vertici e spostare i vertici nell'area di visualizzazione.

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="Visualizzare il grafico in Esplora dati nel portale di Azure":::

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare un grafo con Esplora dati e come eseguire un'app. È ora possibile creare query più complesse e implementare la potente logica di attraversamento dei grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)